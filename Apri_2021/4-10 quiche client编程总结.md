## 4-10 quiche client编程总结

### git操作

只有两个commit的时候如何合并到第一个？

```shell
$ git reset --soft HEAD^1
$ git commit --amend
```

修改git的user信息

```shell
git config --local user.name [name]
git config --local user.email [email]
```

修改已提交commit的author信息

```shell
git commit --amend --reset-author
```

## TCP Socket总结

client端

`socket()`产生连接用的套接字

`connect()`和目标地址建立连接

连接建立完成后

`send()`从指定buf读取，并发送数据

`recv()`接收数据，并保存到固定buf

`close()`关闭套接字

## QUIC examples总结

client端

输入host, port。解析地址信息到`struct addrinfo* peer`，根据peer调用`socket`生成sock

调用UDP层`connect()`判断是否可以建立连接？？？p.s.UDP怎么调用connect?

配置quiche_config，为数据流创建随机序号

调用`quiche_connect()`生成连接套接字`quiche_conn *conn`

调用ev库并初始化`recv_cb()`函数，ev激活`timeout_cb()`

调用`flush_egress()`发送一次数据  p.s.合理推测为发送connect信息，因为`quiche_connect()`没有用到sock信息。

进入到`ev_loop()`，sock每次读入数据就触发`recv_cb()`

### recv_cb()函数分析

1. 首先是while循环，不断调用`recv()`和`quiche_conn_recv()`函数，首先解除UDP头部，再解除QUIC头部，并将数据保存到结构体`conn`

2. 判断quiche连接是否关闭，此处跳过分析

3. 判断连接已经建立并且未发送HTTP请求，则调用`quiche_conn_stream_send()`将请求信息保存到`conn`
   - 疑问1：`conn`可以同时保存待发送和已接收数据吗
   - 疑问2：必须存在此步才能打开数据传输通道吗
4. 已经建立连接并且发送HTTP请求后，调用`quiche_conn_readable(), quiche_stream_iter_next(), quiche_conn_stream_recv()`等函数将`conn`的解除QUIC头部的原始数据保存到buf中。并判断`fin == true`，如果是，则关闭连接。
5. 完成前述所有步骤后调用`flush_egress()`发送`conn`的数据

### QUIC client改造计划

`init()`：包括生成sock，和配置config

`connect()`：发送connect信息到server并判断是否连接成功

`recv()`：从sock读取数据包并解析为原始数据并保存到buf

`send()`：从buf中读取数据并打包为QUIC, UDP包头并发送

`flush_egress()`：私有函数，用于`connect(), send()`等函数，将`conn`中所有待发送数据用`send()`发送