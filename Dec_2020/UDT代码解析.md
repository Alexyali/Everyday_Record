# UDT代码解析

## 拥塞控制模块

### 切换CC算法

**appclient.cpp**第54行setsockopt可以选择拥塞控制算法类型

具体的算法类型可以在`core/cc.h`中的class中去找

UDT运行时默认的算法为`CUDTCC`，在`core.cpp`实例化CUDT时初始化拥塞控制算法实例

```C++
m_pCCFactory = new CCCFactory<CUDTCC>;
```

但是可以通过`setsockopt`选择合适的其他类型(CTCP or CUDPBlast)

```C++
UDT::setsockopt(client, 0, UDT_CC, new CCCFactory<CTCP>, sizeof(CCCFactory<CTCP>));
```

## ACK机制

在onACK()函数中依次输出`m_iSndCurrSeqNo, m_iLastACK, m_dCWndSize`，会发现：
$$
m_iSndCurrSeqNo = m_iLastACK + m_dCWndSize
$$
其中`m_iSndCurrSeqNo`是当前发送的最大数据包序号，`m_iLastACK`是上次onACK确认的数据包序号，m_dCWndSize是当前的拥塞窗口大小。

每秒响应的ACK次数固定为167次，也就是大概每隔0.06s响应一次