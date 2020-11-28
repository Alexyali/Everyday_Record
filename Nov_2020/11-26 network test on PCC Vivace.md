

## 测试环境和工具

使用tc工具调整网络的带宽，RTT和丢包率等信息。使用实验室服务器和工位主机进行双向收发测试。

修改pcc的发送端程序，使其可以控制总运行时长，并输出包含速率和网络信息的日志。

使用python对日志数据进行分析。

## 测试Vivace在变化的网络带宽下的表现。

用TC控制带宽在60Mbps和100Mbps下切换。每次切换持续20秒。实验结果显示，虽然Vivace对带宽变化的反应快，基本在1~2秒内完成切换。但是Vivace向下切换速率时，会有短暂的RTT突增现象。并且当切换时间减少到10秒后更加严重。

![1126_test_100Mbps_60Mbps](C:\Users\Alex\Documents\多媒体实验室\Everyday_Record\Nov_2020\11-26 result\1126_test_100Mbps_60Mbps.png)

## 测试Vivace在丢包的情况下的表现。

分别设置丢包率为0%，10%和15%。 其中无丢包增加了10ms的RTT。 

- 丢包率0
![1126_test_100Mbps_10ms](C:\Users\Alex\Documents\多媒体实验室\Everyday_Record\Nov_2020\11-26 result\1126_test_100Mbps_10ms.png)

- 丢包率10%
![1126_test_100Mbps_10%](C:\Users\Alex\Documents\多媒体实验室\Everyday_Record\Nov_2020\11-26 result\1126_test_100Mbps_10%.png)

- 丢包率15%
![1126_test_100Mbps_15%](C:\Users\Alex\Documents\多媒体实验室\Everyday_Record\Nov_2020\11-26 result\1126_test_100Mbps_15%.png)

- 指标汇总表
| loss/% | 平均带宽/Mbps | 带宽标准差/Mbps | Avg RTT/ms | STD RTT/ms |
| ------ | ------------- | --------------- | ---------- | ---------- |
| 0      | 91.27         | 1.80            | 10.65      | 0.33       |
| 10     | 90.50         | 6.78            | 0.76       | 1.05       |
| 15     | 83.26         | 11.54           | 0.70       | 1.19       |

显然随着丢包率的上升，Vivace直到丢包率为10%都维持较高的吞吐量，但是当丢包率达到15%后带宽下降明显。丢包率上升还会导致发送速率和RTT的波动增大。