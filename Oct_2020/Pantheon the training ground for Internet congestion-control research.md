# Pantheon: the training ground for Internet congestion-control research

`2018 USENIX Annual Technical Conference`

## Abstract

Pantheon是用于互联网传输协议和拥塞控制的训练平台。它作为研究工具的价值有三点。首先，拥塞控制机制的相关性能在动态的网络下变化很剧烈。其次Pantheon可以捕获真实网络路径的多种表现。这保证了可复制快速的实验，并接近真实网络的结果。最后，Pantheon已经用于发展新的拥塞控制算法。

## Introduction

Pantheon有四个部分：

- 包含一系列传输层协议和拥塞控制算法，每个确认可以编译并且在连续集成的系统上运行；
- 全球无线和有线网络上网络节点的多样测试平台
- 一组网络仿真器，每个都经过校准以匹配两个节点之间的真实网络路径的性能
- 一个连续测试系统，定期在真实网络上评估Pantheon

## Relate work

- 网络测量工具。PlanetLab, Emulab, ORBIT为测试传输协议提供了测量节点。
- 网络仿真。拥塞控制研究长期使用网络仿真器，比如`ns-2`，实时仿真器包括`Dummynet, NetEm, Mininet, Mahimahi`
- 拥塞控制。Tahoe对网络反馈做出响应，ACK和丢包分别对应增加和减小拥塞窗口。研究者把拥塞控制作为目标函数和网络先验条件的函数。Remy采用离线优化器在网络仿真环境下优化一个全局效用分数。PCC采用一个在线优化器，自适应调整发送速率去最大化本地效用分数，作为丢包和RTT采样的响应。

## Design and implementation

Pantheon可以在多种网络路径下自动测量许多传输协议和拥塞控制机制的性能。目标是帮助研究者更快和可复制地发展并测试算法。

Pantheon的多个用途：在真实网络下比较现存的拥塞控制机制；校准可以准确重现真实网络表现的网络仿真器；设计并测试新的拥塞控制机制。

## Findings

- 在小范围的路径选择中评估拥塞控制算法的性能肯导致误判的正向结果，因为他们不能在一个大范围的路径内泛化