# QUIC总结-AW

## QUIC简介
- 目的是结合UDP的低延迟特性和TCP的可靠性
- HTTP/2的多路复用功能，解决了TCP的队头阻塞问题
- 连接迁移功能，应对用户网络切换
- 可插拔的拥塞控制机制

## QUICHE
- 在服务器端运行`./server Host Port` 这里的Host为服务器自己的IP
- 在客户端运行`./client Host Port` 这里的Host为服务器IP

### client.c
- `main`函数
	- 创建UDP套接字，往sock中存入文件描述符
	- 使用connect建立UDP连接
	- 设置config的配置参数，如最大空载超时（断开连接）
	- 使用`quiche_connect`创建quiche连接。握手信息存在`quiche_conn *conn`，而sock和conn存在conn_io中，握手信息在`flush_egress`被调用时才发出
	- libev的loop设置，两个事件监听：一个基于IO的watcher，用于监听sock的读写。socket接收到数据时触发`recv_cb`函数
	- libev另一个事件为超时监控，超时后调用`timeout_cb`关闭连接和事件循环。timer在每次调用`flush_egress`后更新，然后超时触发
	- ev_loop开启循环，ev_break停止循环，EVBREAK_ALL停止所有循环
- flush_egress
	- 通过`quiche_conn_send`将conn的数据写为quic包，然后send出去。每次flush后重置超时标志
- recv_cb
	- `recv`从socket的数据读入buf，然后通过`quiche_conn_recv`写入conn。当连接已经建立后，函数用`quiche_conn_stream_send`发送GET index.html信息。接下来接收数据。`quiche_conn_readable`和`quiche_stream_iter_next`将可读的数据流通过`quiche_conn_stream_recv`读入buf中。

### quiche握手

- client的flush_egress发送握手信息，server收到后检查版本，如果符合则在服务端产生token发送给客户端。服务端的版本协商和token发送通过sendto完成。当客户端再次发送数据到server，server可以确认连接，然后发送信息到client

### server.c

- main
	- 两个event：`recv_cb`监控socket数据读取(ev_io)，`timeout_cb`监控超时(ev_timer)。超时监控在create_conn函数中初始化。
- recv_cb
	- 在socket收到数据后触发，用quiche_header_info解析头部。如果目标地址第一次建立连接，则进行版本协商和分配token。create_conn连接建立。
- create_conn
	- 生成服务端的scid并使用`quiche_accept`建立连接，初始化timer

### DEBUG

- 服务端认证不通过：客户端设置config的公钥地址
- 服务端发送丢包“：该丢包不是发送在传输阶段，而是发送时丢失。`quiche_conn_stream_send`一次性发送超过14520字节的数据时可能发生数据丢失。将MAX_BUFLEN改小可以解决问题。
- `quiche_conn_stream_capacity`可以用于查看当前流的剩余空间，当网络状况差的时候，流可用空间为0。故在发送函数中添加流容量监控。