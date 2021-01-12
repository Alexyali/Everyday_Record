# 01-12 UDT CTCP测试

UDT CTCP用的是简单的Reno算法

在appclient里面调整UDT_SNDBUF，可以改变发送缓冲区大小，从而改变RTT的上限值。

在./app/cc.h中修改CTCP的init()函数中的setRTO()，改变超时设计。



