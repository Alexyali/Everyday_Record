# 9-18 交互功能

- `Send_Server` 的 `recv_cb` 函数支持收到返回的数据并解析
  - 首先判断 `if (**quiche_conn_is_established**(conn_io->conn))`
  - 成立后，用循环`while (**quiche_stream_iter_next**(readable, &s))` 读取各个流的数据
  - `recv_len = quiche_conn_stream_recv(conn_io->conn, s, buf, sizeof(buf),&fin)` 获取第s个流的数据并存入buf
- 在`quiche_conn_stream_recv` 的命令后输出buf的值即可
- client 采用`quic-hevc`的`hevc_0.3.0_pipe` 分支编译，支持在键盘输入字符就给server端反馈信号
- 交互测试
  - 运行my_host_linux
  - 运行client，并在键盘多次输入字母`s`
  - 输出结果：`client echo times: 3` 说明收到了三条来自客户端返回的消息，完成交互

