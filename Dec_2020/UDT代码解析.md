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

