# TCP Westwood 

Westwood是为了解决TCP在无线链路在随机丢包的情况下性能差而设计的。

它的解决思路是估计可用带宽(ABE)，每次遇到丢包的时候，将cwnd和ssthresh设置为ABE*RTT。这样即使遇到随机丢包，ABE的值不改变，吞吐量不会降低。

Westwood的伪代码如下：

```c
rate_control()
{
	if (n DUPACKS are received)
		ssthresh = (ABE * RTT_min)/seg_size
		if (cwnd > ssthresh)
			cwnd = ssthresh
		endif
	endif
	
	if (timeout expires)
		ssthresh = (ABE * RTT_min)/seg_size
		if ssthresh < 2
			ssthresh = 2
		endif
	cwnd = 1
	endif
}
```



## 代码分析

函数调用关系

- **tcp_westwood_init()**    *初始化函数参数*
- **tcp_westwood_event()**    *处理丢包等事件*
  - tcp_westwood_bw_rttmin()    # 根据ABE和min rtt计算合适的cwnd
- **tcp_westwood_ack()**    *当ACK到达时触发*
  - westwood_update_window()    # 每次RTT更新一次ABE，bw = bk / delta
    - westwood_filter() # 根据历史值和当前估计值，计算当前的ABE
      - westwood_do_filter()   # 计算加权平均值
  - westwood_acked_count()   # 在计算ack数据总大小(bk)时，处理延迟或部分ack的影响
  - update_rtt_min()   # 更新rtt的最小值
  - westwood_fast_bw() # 当处于快速路径时选择该函数，可以直接更新ABE和RTT
    - westwood_update_window()
    - update_rtt_min()
- **tcp_westwood_pkts_acked()**    *处理完一组ACK数据包时触发：更新当前的 RTT*

## tcp jiffies

TCP Westwood中采用jiffies代替传统的timestamp.

jiffy是在<linux/jiffies.h>下声明的内核的时间单位。同时引入一个新常量，HZ，它是一秒内jiffies增加的次数。HZ也就是jiffer每秒自增的频率。每次增加称为一个tick。HZ由硬件和内核版本决定，统计取决于时钟中断频率。

> 小例子

如果HZ=1000，那么jiffies每秒增加1000次，每个tick为1 ms。