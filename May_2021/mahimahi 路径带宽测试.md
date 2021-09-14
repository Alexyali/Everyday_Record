## mahimahi 路径带宽测试

只需要保证mm-link和客户端连续执行，则每次的路径启动点相同

服务端终端：

```shell
./server 0.0.0.0 1234
```

客户端终端：

```shell
mm-link /usr/share/mahimahi/traces/Verizon-LTE-short.down /usr/share/mahimahi/traces/Verizon-LTE-short.down -- ./client 100.64.0.1 1234
```

实验结果：

实验路径为`/usr/share/mahimahi/traces/Verizon-LTE-short.down`，采用CUBIC算法，可以充分利用网络带宽。分别执行两次客户端程序，每次都持续60s，对比两者log记录的吞吐量数值。

两者对应时间戳的吞吐量误差平均值为-0.016

两者对应时间戳的吞吐量误差标准差为0.284

该LTE路径测量的带宽平均值为4.248Mbps，标准差为1.69