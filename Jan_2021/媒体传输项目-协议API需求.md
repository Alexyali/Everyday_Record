# 媒体传输项目-协议API需求

项目采用QUIC的quiche实现，需要提供和quiche相同功能或者类似功能的C++ API接口。

最终提供的API调用方式为：头文件和动态库/静态库（xxx.h + xxx.so/xxx.a）

quiche的github地址：[quiche](https://github.com/cloudflare/quiche.git)

### Debug

输出debug日志信息，参考`quiche_enable_debug_logging()`

### Config

可以设置协议配置文件的函数，参考`quiche_config_new()`, `quiche_config_set_max_packet_size()`等。

在协议配置文件中可以设置拥塞控制算法，参考`quiche_config_set_cc_algorithm()`

### 建联机制

服务端**accept()**函数，参考`quiche_accept()`

客户端**connect()**函数，参考`quiche_connect()`

从握手包中解析peer端的版本和地址等信息的函数，参考`quiche_header_info()`

版本协商相关函数，参考`quiche_version_is_supported()`, `quiche_negotiate_version()`

判断连接是否建立，参考`quiche_conn_is_established()`

判断连接是否关闭，参考`quiche_conn_is_closed()`

处理超时事件，参考`quiche_conn_on_timeout()`

### 数据收发

获取当前流的最大发送容量函数，参考`quiche_conn_stream_capacity()`

从内存中读取数据并发送到peer端，参考`quiche_conn_send()`, `quiche_conn_stream_send()`

从网络中接收数据并保存到内存，参考`quiche_conn_recv()`, `quiche_conn_stream_recv()`

支持多流传输，可以轮替读写不同流的数据，参考`quiche_conn_readable()`, `quiche_stream_iter_next()`

判断流是否终止，已经接收完所有数据，参考`quiche_conn_stream_finished()`

### 反馈网络信息

可以读取该连接相关的传输信息，例如带宽和延迟，参考`quiche_conn_stats()`