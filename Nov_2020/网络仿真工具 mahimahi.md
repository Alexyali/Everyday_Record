# 网络仿真工具 mahimahi

mahimahi是一个轻量的、可组合的网络仿真工具

链路仿真：mm-delay, mm-loss, mm-link

分析脚本：mm-throughput-graph, mm-delay-graph

## mahimahi和tc的比较

- 采用tc控制延迟似乎对本地环路传输无效，只对连接外网有效。但是mahimahi可以对本地环路有效，需要填本地ip。

- 在本机采用tc控制速率控制的是上传带宽，下载带宽不受限。

## 如何安装

[mahimahi](http://mahimahi.mit.edu/#getting)

```
$ sudo add-apt-repository ppa:keithw/mahimahi
$ sudo apt-get update
$ sudo apt-get install mahimahi
```

查看帮助手册`man mahimahi`

## 链路仿真工具

- mm-delay [delay] ：增加delay大小的单向延迟
- mm-loss uplink|downlink [rate] ：离开容器（uplink）或者进入容器（downlink）的丢包率
- mm_onoff uplink|downlink [mean-on-time] [mean-off-time] ：容器的上行或者下行连接会断续，并且在连接和断开之间切换

## 观察工具

mm-meter [--meter-uplink] [--meter-downlink]