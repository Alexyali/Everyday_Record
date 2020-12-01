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
- mm-link：仿真链接。链接所在文件夹`/usr/share/mahimahi/traces`  

## 观察工具

mm-meter [--meter-uplink] [--meter-downlink]

## 如何使用mahimahi

在已经运行mahimahi的终端输入`printenv`可以查看mahimahi容器对应的IP地址，用于双向测试。一般来说mahimahi容器对应的ip地址为`100.64.0.1`

以pcc算法测试为例，首先运行接收端程序，开始监听

```
./pccserver recv 1234
```

然后使用mahimahi建立仿真网络环境，并同时运行发送端程序

```
mm-loss uplink 0.01 sh -c './pccclient send 100.64.0.1 1234'
```

`mm-loss uplink 0.01`为增加的丢包率为0.1仿真网络，后面的命令则是发送端程序

