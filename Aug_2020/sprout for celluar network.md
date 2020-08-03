# Sprout
`Stochastic Forecasts Achieve High Throughput and Low Delay over Cellular Networks`  
`nsdi 2013`
- 在移动网络（迅速变化的网络带宽）下取得高带宽和低延迟
- 在网络状况变化大的环境下效果最好
- Sprout是一个端到端传输层协议，用于需要高带宽和低延迟的交互式应用
## Background
- 移动网络的吞吐量变化很大，因此交互式应用的效果很差
- 测试应用包括Skype, Google Hangout, Facetime
- 在测试的网络路径下，即使可用网络容量很大，Skype也没有利用，而且当网络容量急速恶化时，Skype会引入较大的延迟
- 原因：现存机制对拥塞信号的判断不对，多采用丢包或者RTT的增加判断拥塞，而反馈不及时。关键因素是自扰的队列延迟，吞吐量超过容量导致队列填满

## sprout goal
- 最大的吞吐量
- 约束延迟超过额定值（100ms）的风险

## method
- 推断：根据间隔到达时间的分布来推测链接速率
- 预测：预测未来的链接速率
	- 根据随机游走(Brownian motion)建模速率
	- 接收端做出预测，并将结果在ack中返回
- 控制：尽可能多发，但是保证95%的几率所有数据包的到达时间在100ms内
	- 填满100ms的预测窗，也即每次计算下一个100ms内可以发送的数据包数量

## evaluation
- sprout 可以准确预测网络带宽的变化，并避免自扰延迟
- 在LTE网络连接下，相比Vegas, Compound等低延迟拥塞控制机制也取得了更低的延迟和更高的带宽
- Sprout-EWMA牺牲了一些延迟，取得了比Cubic更高的带宽
- 根据`PCC Vivance`论文的结果，Sprout的吞吐量还是比BBR低一倍，但是延迟更低
- 没有和WebRTC对比