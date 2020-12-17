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

