# quic_hevc 测试

## client 测试

### 单独开启client到自动关闭
- 实验1：在`recv_cb`和`timeout_cb`处打点
	- recv_cb -> timeout_cb * N
- 实验2： 在`timeout_cb`函数开始处和`quiche_conn_is_closed`时打点
	- N * timeout -> conn_closed
- 实验3：是否调用keyboard_cb()
	- 否，因为`quiche_conn_is_established` false？
	- `recv_cb` return as result from `recv` nothing
- 原因： timer.repeat 控制调用cb函数的间隔时间，而**main**函数的`max_idle_timeout`控制最大空载超时，也就设定`conn_is_closed` 在两端断开连接后return true的最大超时时间
- 8-8结果：实现了client端自动关闭，修改了main函数的timeout时间参数配置

## 明天的任务
- 直接从quiche的源代码改起？
- 现在是直接从buffer读数据，但是应该建立一个FIFO队列读数据
- 怎么和host_linux联合，如何写makefile