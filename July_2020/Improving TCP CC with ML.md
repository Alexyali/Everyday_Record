# Improving TCP CC with ML

## Introduction
- 背景：为了支持高带宽网络，一个原则是让链路缓冲大小和链路带宽成正比，但是这会造成`bufferfloat`，也就是数据包超过缓冲区后将带来高延迟。当共存的TCP连接数量很小时，一个未缓冲满（缓冲区小于规则建议的）的瓶颈链接会导致cwnd因为丢包经常下降
- TCP CC 方案是否可以学习拥塞丢包？本文题材了一个使用监督学习的丢包检测器LP，并结合TCP CC方案来预测并减少拥塞丢包。通过调整决策阈值，LP-TCP在丢包和吞吐量之间取得了平衡。和NewReno相比，LP-TCP在相似RTT的情况下取得了更高的吞吐量。
- 但是当网络的拓扑和参数变化时，LP-TCP需要重新学习。因此第二个问题是CC方案是否可以在动态网络环境下自适应学习？本文提出了RL-TCP，目标函数参数包括吞吐量、延迟和丢包率。和NewReno和Q-learning TCP相比，在单连接情况下，RL-TCP可以取得更低RTT和更高吞吐量。

## Related Work
- 基于窗口的方法：Reno, Vegas, Cubic, Compound. 根据特定的反馈信号，按照固定规则调整cwnd
- 基于学习的方法：Remy, PCC, Q-learning. 

## 提出的方案
- 有线网络，瓶颈链接缓冲不足的场景，采用机器智能提高CC的表现
- 提出一个基于监督学习和一个基于RL的方法，这两个方案共享一个架构。
- 该架构包含三个部分：
	- 感知机器，处理来自网络的信号，结合发送端的变量，输入代表当前状态的序列
	- 学习器，包含一个在线学习机器或者模型，输入当前状态，输出预测结果
	- 执行器，根据预测执行动作，比如调整cwnd
- 感知机器计算反映网络拥塞状态的统计数据，包括数据包间发送时间、ACK间隔到达时间、RTT. 学习器是智能体的大脑，学习特定状态和可能行动之间的对应关系。学习器的设计和训练是一个优秀的基于学习的方法的关键。本文基于学习的CC机制仍然基于Reno.

### LP-TCP
- Reno每当出现丢包就减半cwnd，因此LP-TCP预测并减少丢包事件，降低发送速率减少的频率，获得更高的吞吐量
- 预测一个包如果发送就丢包的概率，如果大于阈值就减少cwnd；否则就发送

### RL-TCP
- 没有先验知识的智能体通过行动和从环境接受奖励来学习如何行动，和QTCP相比，RL-TCP面对缓冲不足瓶颈链接的网络，设计状态和行动。RL-TCP根据网络动态处理时间信用分配
- RL-TCP 在Reno的拥塞避免阶段进行优化，动态调整cwnd

## 实验评估
- compared with Reno and QTCP
- BW = 10Mbps, RTT = 150ms, BDP = 150 packets
- two main scenarios
	- single sender, buffer size = 5, 50 ,150 (under-buffered for lower than BDP)
	- 4 senders, sharing one bottleneck, buffer size = 50
- metrics: throughput, delay, packet loss rate
- 分析： 两种方法的吞吐量的提升不明显，LP-TCP的延迟基本没有降低；RL-TCP的延迟降低明显。原因可能是因为在Reno的框架下，基本没有丢包，因此吞吐量有上界。延迟显著降低可能是因为在拥塞避免阶段，RL方法可以选择多种步长。
```
cwnd = cwnd + x, x= -1.0,+1,+3
cwnd = cwnd + x/cwnd
```

## 笔记
- 方法值得参考，但是基于Reno修改太局限了，导致性能比较一般
- 基于学习的CC算法可能对参数敏感，RL-TCP的动作空间需要根据网络配置进行不同设计；基于RL的算法公平性有待提高