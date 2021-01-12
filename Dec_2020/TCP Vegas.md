# TCP Vegas

TCP Vegas通过计算期望数据速率和实际的数据速率的差值来估计网络的拥塞程度，从而调整TCP窗口大小。

- W：窗口大小
- T：最小的往返延迟
- RE：期望吞吐量，RE=W/T
- Ts：实际往返延迟
- R：实际吞吐量

$$
Diff = T(R_E - R) 
$$

$$
Diff = \frac{W}{T_s}(T_s - T) = R(T_s - T)
$$

根据**Little law**，该表达式等价于网络中排队的数据包个数，可以视为拥塞的信号。

定义两个阈值α和β并且α<β，窗口W控制规则如下：

- 如果α <= Diff <= β，则W不变 
- 如果Diff >= β，则W减1，因为这意味着拥塞已经开始出现
- 如果Diff <= α，则W增1，因为实际吞吐量小于期望值，该连接可能没有完全利用网络带宽

TCP Vegas尝试保持网络中一定数量的排队数据。太多排队数据可能导致拥塞，但是太少排队数据会阻止连接迅速利用突然增加的可用带宽。Vegas还消除了Reno的振荡问题，减少了端到端延迟和抖动。

Vegas和Reno在同一链路争夺带宽时，Reno最终会占据绝大部分带宽。因为Reno会尽量填满缓冲区，而Vegas则尽可能占据很小的缓冲空间。Reno填满缓存导致Ts迅速增加，为了保持Diff的稳定，Vegas的吞吐量R会迅速降低到非常小。因此Vegas在公网上竞争不过Reno，没有得到广泛部署。

### Vegas算法理解

$$
Diff = R(T_s - T)
$$

首先网络吞吐量和RTT随飞行数据的增加的变化分为三个阶段：

- app limit: inflight<BDP, 也可以理解为W<CT，此时RTT是最小值，吞吐量处于增加状态
- buffer limit: BDP<inflight<BDP+Buffer，此时队列开始暂存数据包，吞吐量达到网络容量，RTT随inflight增加而增大
- loss limit: inflight>BDP+Buffer, 此时队列内存已用完，出现丢包，网络拥塞严重

处于app限制阶段时，Ts约等于T，因此Diff约为0，满足条件`Diff <= α`，因此需要增大cwnd；处于buffer限制阶段时，R=C不变，Ts不断增大，满足条件`Diff >= β`，因此需要减小cwnd。

因此当一个连接中只有Vegas流时，通过控制Diff(就是队列存储数据大小)维持在一定区间，保证网络传输处于buffer限制阶段，在维持较低RTT的情况下，最大化网络吞吐量。

### 算法程序实现

[frome linux kernel](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/tree/net/ipv4/tcp_vegas.c)

- vegas_enable()：启动算法

- vegas_init()：初始化最小往返延迟baseRTT，并调用vegas_enable

- tcp_vegas_pkts_acked()：采用最小值滤波得到minRTT（当前的端到端延迟=传输延迟+排队延迟）和baseRTT（最小往返延迟=传输延迟）

- tcp_vegas_cong_avoid()：拥塞避免算法。只有当收集到足够多RTT样本时才采用Vegas，如果RTT计数小于2，则仍采用reno的拥塞避免。否则说明收集到足够的RTT样本值。计算当前RTT，目标窗口和Diff差值。

  ```c++
  // rtt is prop.delay + queue.delay
  rtt = vegas->minRTT;
  // target_cwnd = actual_rate * baseRTT
  target_cwnd = tp->snd_cwnd * vegas->baseRTT / rtt;
  // diff is the queue data
  diff = tp->snd_cwnd * (rtt - vegas->baseRTT) / vegas->baseRTT;
  ```

  - 如果还处于慢启动阶段，判断diff是否大于gamma=1，如果是，说明开始排队，需要进入拥塞避免阶段：将当前cwnd设置为目标cwnd，并将ssthresh设置为当前cwnd。如果diff小于gamma，则说明还处于慢启动阶段。

  ```c++
  if (diff > gamma && tp->snd_cwnd <= tp->snd_ssthresh) {
  	tp->snd_cwnd = min(tp->snd_cwnd, (u32)target_cwnd+1);
  	tp->snd_ssthresh = tcp_vegas_ssthresh(tp);
  }
  else if (tp->snd_cwnd <= tp->snd_ssthresh) {
  	tcp_slow_start(tp);
  }
  ```

  - 如果已经进入拥塞避免阶段，也就是snd_cwnd > ssthresh，那么进入vegas调整阶段，如果diff > beta，说明排队数据太多，需要降低cwnd，并保持拥塞避免阶段。否则如果diff < alpha，说明排队数据太少，需要增大cwnd。如果diff在两者之间，不需要调整cwnd。

  ```c++
  if (diff > beta) {
  	tp->snd_cwnd--;
      tp->snd_ssthresh = tcp_vegas_ssthresh(tp);
  }
  else if (diff < alpha) {
      tp->snd_cwnd++;
  }
  else { }
  ```



## Little law

$$
L = \lambda * W
$$

在一个排队过程中，如果1/λ是两个连续unit的间隔到达时间的平均值，L是系统中unit的平均值，W是一个unit在系统中花费的平均时间。如果这三个平均值是有限的，并且对应的随机过程严格平稳并且到达过程是度量传递的，且非零均值。那么上述公式成立。
