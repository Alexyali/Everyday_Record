# Make TCP Scheme Great for Cellular Network

Github地址：`https://github.com/Soheil-ab/DeepCC.v1.0`  

与其设计一个全新的TCP算法，本文设计了一个可以用于增强无线移动网络下TCP拥塞控制算法性能的插件。DeepCC使用DRL方法让基于吞吐量的传统拥塞控制算法达到期望延迟。

### 动机

- 增强传统TCP算法而不是取代

- 基于学习的算法不用手动调整参数
- 吞吐量导向的算法往往可以达到非常高的吞吐量和很高的延迟。对延迟敏感类应用来说影响很大。但是如果可以控制吞吐量导向的算法的延迟不超过期望值，就可以达到较低的延迟和很高的吞吐量。

### 相关工作

为移动蜂窝网络设计的拥塞控制算法

| Name     | Year | Publish   | Method                                         |
| -------- | ---- | --------- | ---------------------------------------------- |
| Sprout   | 2013 | NSDI      | stochastic prediction of link rate             |
| Verus    | 2015 | SIGCOMM   | using delay profile to get cwnd                |
| PropRate | 2017 | CoNEXT    | using buffer delay                             |
| ExLL     | 2018 | CoNEXT    | infer rate by pattern of data reception        |
| DeepCC   | 2020 | IEEE JSAC | using DRL to control max cwnd in loss-based CC |

### 系统概述

吞吐量导向的算法可以在移动网络下取得高吞吐量和高延迟的原因是它们计算出的CWND通常大于最优值。因此当链路容量上升时，它们总有充分多的数据包发送，有很高的利用率。但是在BTS中的大量数据包会在链路容量下降时引入大量的自限队列延迟。

吞吐量导向的TCP机制可以通过控制CWND的最大值来控制。DeepCC控制CWND的最大值而不是具体值。这可以将底层的TCP系统视为一个黑盒，并且可以作通用插件适用多种不同的拥塞控制算法。

DeepCC尝试控制数据包的平均延迟低于应用的期望目标值，同时保持高吞吐量。为此，应用将期望目标延迟作为可选参数在创建TCP socket的时候传入DeepCC。DeepCC的监控模块周期性收集系统的CWND和数据包统计数据。每个RTT，状态生成模块将监控模块收集的信息用来生成合适的状态向量，用来表示该时间段的环境状态。DRL智能体根据新的状态向量计算新的动作。DRL智能体根据状态向量、和当前状态联系的奖励计算出合适的CWND的最大值。最后综合CWND和最大值决定发送多少数据到网络中。

### 监控模块

每个RTT，监控模块生成统计信息，输入到状态生成模块。考虑以下统计信息：

- d: 每个RTT的数据包平均延迟
- n: 用于计算d的样本个数
- p: 每个RTT的平均吞吐量
- cwnd: 底层TCP计算的当前cwnd

监控模块通过观察到达的ACK数据包生成统计信息，周期性计算d, n, p并传输参数到状态生成模块。监控模块作为填充层与TCP机制独立。

### 状态生成模块

状态生成器管理期望目标延迟和网络的动态信息，并生成DRL智能体需要的输入状态。每次网络监控器传输数据包信息时，状态生成器会更新状态向量。

一个挑战是，DRL的学习过程需要从高动态范围的状态中提取有用的特征并映射到动作。另一个挑战是，智能体如何有效学习cwnd的策略，因为环境的不确定性会让问题丧失马尔可夫特性。如果智能体只根据当前状态进行决策可能影响智能体的运行。因此提出两个技术：1）滤波核；2）递归结构。

- 滤波核。将延迟和目标值转换为一个简单的核函数。并用它来编码吞吐量和延迟。t时刻观察到的统计信息可以重写为一个向量。
- 递归结构：用于解决马尔可夫属性缺失的问题。St是一个长度为m的向量，包括了当前观察信息和m-1个历史的统计信息

### 奖励生成模块

DeepCC的目标是保持数据包的平均延迟低于目标值，并且最大化吞吐量。智能体惩罚或者奖励吞吐量，ACK数目和平均延迟/目标值的乘积。因此智能体会首先将平均延迟降低到目标值以下，然后最大化吞吐量。

