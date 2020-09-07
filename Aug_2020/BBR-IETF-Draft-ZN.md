# BBR-IETF-Draft-ZN

`https://tools.ietf.org/pdf/draft-cardwell-iccrg-bbr-congestion-control-00.pdf`  

`https://www.ietf.org/proceedings/99/slides/slides-99-iccrg-iccrg-presentation-2-00.pdf`  

## Abstract

- BBR测量连接最近的交付速率和RRT，计算最大带宽和最小往返延迟
- BBR控制发送速率和最大飞行数据总量
- 相比于基于丢包的算法，BBR可以在随机丢包或者小缓冲区下取得更高的吞吐量，或者在大缓冲区的情况下取得更低延迟，避免了缓冲溢出
- BBR算法可以应用于数据包ACK机制的传输协议，包括：TCP，QUIC，SRT。

## Introduction

### 网络拥塞的基本概念

- 拥塞可以认为是一个网络路径的飞行数据总量（已经发送但是收端未确认的数据）超过了**BDP**（带宽延迟积）。网络状况可以分为三个阶段：应用限制，带宽限制和缓冲限制阶段。
  - 应用限制：`inflight < BDP` 此时RTT为最小延迟且维持不变，交付速率（数据包从发送到确认收到的速率）随inflight的增加而线性增加
  - 带宽限制：`BDP < inflight < BDP + BufSize` 此时交付速率达到最大带宽且保持不变，网络的缓冲队列开始累积数据包，此时RTT随infligt的增加而线性增加
  - 缓冲限制：`inflight > BDP + BufSize` 此时网络的缓冲队列填满，无法缓存新的数据包，导致丢包+重传的恶性循环，最终网络吞吐量大幅下降
- 基于丢包的Reno, Cubic拥塞控制算法只有当网络丢包才开始控制/降低发送速率。有两个问题：
  - 浅缓冲：丢包发生在拥塞之前（猜测是因为BDP比BufSize大很多，随机丢包更有可能发生在应用限制阶段）。由于基于丢包的算法无法区分随机丢包和拥塞丢包，只要出现丢包就减半发送速率，很难充分利用带宽。一个例子是维持10Gbps, 10ms RTT的网络要求丢包率低于3E-8，而丢包率1%将只能维持3Mbps的吞吐量
  - 深缓冲：丢包发生在拥塞之后。BufSize比BDP大很多，会导致`bufferfloat`的问题，也就是不断发送数据使得网络的缓冲溢出，这必然导致不必要的秒级的队列延迟。

### BBR的基本概念



