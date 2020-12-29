# UDT协议

### 选择原因

PCC Allegro， Vivace算法在UDT协议基础上实现

`PCC Vivace: Online-Learning Congestion Control`  

北京大学提出的Iris在UDT和QUIC上实现

`Statistical  Learning Based Congestion Control for Real-time Video Communication`  

### UDT简介

UDT论文：`UDT: UDP-based data transfer for high-speed wide area networks`  

UDT旨在有效利用迅速出现的高速广域网络。它建立在具有可靠性控制和拥塞控制的UDP之上，这使得安装非常容易。拥塞控制算法是使UDT有效利用高带宽的主要内部功能。同时，我们还实现了一组API，以支持简单的应用程序实现，包括可靠的数据流和部分可靠的消息传递。原始的UDT库也已扩展到Composable UDT，它可以支持各种拥塞控制算法。 

UDT通过操作系统提供的套接字接口使用UDP。同时，它为应用程序提供了UDT套接字接口。应用程序可以像调用系统套接字API一样调用UDT套接字API。应用程序还可以为UDT提供拥塞控制类实例来处理控制事件，因此可以使用自定义的拥塞控制算法。

下图是UDT的层间架构图。UDT是UDP基础上的应用层。应用通过UDT套接字交换数据，再使用UDP套接字来发送或者接收数据。应用程序还可以提供定制的CC算法。

![12-28 UDT-Arch](C:\Users\Alex\Documents\多媒体实验室\Everyday_Record\Dec_2020\12-28 UDT-Arch.PNG)

### UDT协议设计

UDT是面向连接的双工协议。 它支持可靠的数据流和部分可靠的消息传递。数据流从发送端到接收端，但是控制流在两个接收器之间交互。接收端负责触发并处理所有控制事件，包括拥塞控制和可靠控制。

UDT使用基于速率的拥塞控制和基于窗口的流量控制来调节传出数据流量。 速率控制会在每个恒定间隔更新数据包发送周期，而流量控制会在每次收到确认数据包时更新流窗口大小。

**数据包结构**

UDT中有两种类型的包：数据包和控制包，根据头部的1 bit标志位区分。UDT数据包包含基于包的序列号，消息序列号和相对时间戳（建立连接后开始计数，以微秒为单位）。

## UDT编译

UDT地址：[UDT online](http://udt.sourceforge.net/)

直接make即可，但是运行时可能出现找不到libudt.so的问题，打开终端运行以下指令：

```
$ sudo apt install libudt-dev
```



