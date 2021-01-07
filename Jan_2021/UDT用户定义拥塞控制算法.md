# UDT用户定义拥塞控制算法

地址：`doc/doc/t-cc.htm`

所有的拥塞控制回调函数都在`CCC`的C++类中，你需要继承这个类来定义自己的拥塞控制算法。

CCC类有两个控制变量：`m_dPktSndPeriod`是代表数据包发送周期的浮点数，单位是ms。`m_dCWndSize`是代表拥塞窗口的浮点数。拥塞控制算法需要更新至少一个参数。对于纯基于窗口的方法，`m_dPktSndPeriod=0`.

最快学习CCC的方法是参考`udt/app/cc.h`，其中包含了两个控制算法，可以在其基础上设计自己的算法。

下面利用CCC写一个可靠的UDP突发控制

```c++
class CUDPBlast: public CCC
{
public:
  CUDPBlast() {m_dCWndSize = 83333.0;}

public:
  void setRate(int mbps)
  {
    m_dPktSndPeriod = (m_iMSS * 8.0) / mbps;
  }
};
```

在这个例子中，CUPBlast继承了基础类CCC。并且设置拥塞窗口为很大的值，因此不会影响数据包发送。方法`SetRate()`可以用于设置固定的数据包发送速率。

应用程序可以使用`setsockopt/getsockopt`来分配控制算法到具体的UDT实例，并设置参数。

```c++
UDT::setsockopt(usock, 0, UDT_CC, new CCCFactory<CUDPBlast> , sizeof(CCCFactory <CUDPBlast> ));
```

要设置特定的数据发送速率，应用程序需要获取UDT套接字usock使用的具体CCC类实例的句柄。

```C++
CUDPBlast* cchandle = NULL;
int temp;
UDT::getsockopt(usock, 0, UDT_CC, &cchandle, &temp);
if (NULL != cchandle)
  cchandle->setRate(500);
```

UDT/CCC可以用于实现基于速率的控制和TCP变体等算法。例如CUBIC和Vegas。

