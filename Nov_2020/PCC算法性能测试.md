# PCC算法性能测试

下载地址：[PCC-Uspace](https://github.com/PCCproject/PCC-Uspace)

在本机和服务器上测试PCC算法性能。服务器运行pccclient，发送数据；本机运行pccserver，接收数据。本机和服务器用有线局域网直接连接，网络参数如下：

| 网络       | 带宽/Mbps | RTT/ms | 丢包率/% |
| ---------- | --------- | ------ | -------- |
| 有线局域网 | 1000      | 0.3    | 0        |

采用tc命令行控制网络情况（RTT增加20ms 上行速率最大100mbps 丢包率为1%）

```
$ sudo tc qdisc add dev eno2 root netem delay 20ms rate 100mbit loss 1%
```

如果要删除tc网络限制

```
# if want to delete 
$ sudo tc qdisc del dev eno2 root
```

首先本机运行pccserver

```
./app/pccserver recv 1234
```

服务器运行pccclient，效用函数采用基本效用函数，高带宽低延迟

```
./app/pccclient send 172.16.7.228 1234 Vivace Vivace
```

测试结果为PCC发送速率在85-95Mbps之间，RTT约为20ms。