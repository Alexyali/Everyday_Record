# QTCP 使用RL的自适应拥塞控制
`QTCP: Adaptive Congestion Control with Reinforcement Learning`

## Introduction
- 本文基于RL设计了QTCP（基于Q-learning的TCP CC）自动决定最优的`cwnd`策略，根据在线观察网络环境。
- QTCP根据收集的网络参数，连续更新状态-动作组合，使用Q学习算法搜索最佳动作，比如如何调整cwnd，提高长期回报
- 挑战：CC问题的连续高维状态空间，可能需要很大空间存储大量状态，训练也会更耗时。为了加速学习过程，采用了函数近似的方法，减少状态空间大小
- 贡献1： 提出了QTCP，可以在线学习策略调整cwnd，达到高带宽低延迟
- 贡献2：提出了Kanerva编码算法，可以应用于大型状态空间，加速收敛

## Reno problem
- 采用AIMD方法控制cwnd，可以保证收敛到最佳带宽，兼顾效率和公平性
- 无法适应未知网络环境，Reno不适合高带宽高延迟网络，在拥塞避免阶段的增加速度太慢
- 无法学习历史信息，例如Reno可以根据网络情况在拥塞避免阶段加速cwnd的增加速度

## QTCP Overview
- 发送端就是智能体，和网络环境互动，并采取一系列动作（调整cwnd）探索最优解。达到目标，如高带宽低延迟。
- QTCP的RL元素
	- STATE：根据选择参数决定的网络状态
		- avg_send 发送两个包的平均间隔
		- avg_ack 收到两个连续ACK的间隔
		- avg_rtt 平均rtt
	- ACTION：提高、保持、降低目前的cwnd
		- 3 actions: add 10 bytes, reduce 1 byte, hold
	- REWARD：吞吐量和延迟
		- `r = a*log(throughput)-b*log(RTT)`
	- 训练：采用Q-learning算法进行训练，这是一种时间差分方法
- CC应用学习算法的关键是选择用于建模的大量状态组合
- QTCP是第一个将RL应用于TCP拥塞控制的方法
