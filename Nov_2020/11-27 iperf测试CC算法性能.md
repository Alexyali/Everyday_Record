# 11-27 iperf测试CC算法性能

使用iperf进行测试

`iperf -c [ip addr] -t [run time] -Z [congestion control]`

可选择的拥塞控制包括reno, cubic和bbr

| CC\Limit\BW(Mbps) | 无限制 | BW=100   | BW=100 loss 5% | BW=100 RTT=100ms |
| ----------------- | ------ | -------- | -------------- | ---------------- |
| Reno              | 873    | 97.8     | 62.5           | 94.9             |
| Cubic             | 876    | 97.823.6 | 23.6           | 93.2             |
| BBR               | 876    | 96.4     | 93.1           | 91.4             |

发现的问题：

- 在丢包严重的情况下，reno和cubic带宽损失严重

测试各算法在丢包率变化下的吞吐量，限制最大带宽为100Mbps

| loss  | 0    | 1%   | 2%   | 3%   | 4%   | 5%   |
| ----- | ---- | ---- | ---- | ---- | ---- | ---- |
| Reno  | 97.8 | 95.4 | 92.6 | 86.4 | 70.8 | 62.5 |
| Cubic | 97.8 | 95.1 | 86.2 | 64.1 | 41.9 | 23.6 |
| BBR   | 96.4 | 95.6 | 95.4 | 94.9 | 93.3 | 93.1 |

![1127_loss_test](C:\Users\Alex\Documents\多媒体实验室\Everyday_Record\Nov_2020\1127_loss_test.PNG)

