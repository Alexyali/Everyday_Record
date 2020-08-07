# quiche code 
- 必须先开server.c，它会监听自己的端口
- 如果先开client.c，它会尝试连接server.c，并且超时断联
- 线程启动顺序：ffplay, server, client, ffmpeg
- 问题：合并编码器和服务端
- 场景：先开接收端监听，再开推送端
- 问题：现在是server监听，client主动连
- 方法：调换server和client位置，让client发送数据，server接收数据
- 方法：屏蔽server发送数据函数和client接收数据函数，启用client发送数据函数和server接收数据函数
- 问题ev_io watcher和ev_loop loop代表什么

## server.c
- create_conn()初始化并启动了pipe_send()的回调函数，用于发送视频数据
- recv_cb()中的while循环下会判断quiche_conn_is_establish()，并使用quiche_conn_stream_recv()接收数据
- flush_egress()：调用了quiche_conn_send(conn_io->conn)和sendto(conn_io->sock)，并且初始化conn_io->timer.repeat和ev_timer_again(loop) 
- create_conn()：使用ev_init()了timeout_cb()，并输出new connection，使用了timer
- recv_cb()：首先进入while循环，调用了create_conn()
- timeout_cb()：调用quiche_conn_on_timeout(conn_io->conn)然后调用quiche_conn_is_closed最后ev_timer_stop()
- main() ：使用ev_io_init()监听sock是否读入数据，如果是则进入recv_cb回调函数。使用ev_io_start()启动循环，再调用ev_loop()
```
ev_io_init(&watcher, recv_cb, sock, EV_READ); //listen to `sock`, and enter `recv_cb` when read from sock
ev_io_start(loop, &watcher);
watcher.data = &c; //c.sock = sock
ev_loop(loop, 0);
```

## client.c
- 在结构体conn_io中用`ev_timer`类型定义了`keyboard_timer`
- 在recv_cb()中对`keyboard_timer`定义repeat并调用`ev_timer_again()`
- 在main()中使用`ev_init()`初始化了`keyboard_timer`并调用了`keyboard_cb()`，后者用于向server端发送数据
- `keyboard_cb()`判断quiche_conn_is_establish()后使用quiche_conn_stream_send发送数据给server

## ev.h
- `ev_init`,`ev_io_init`,`ev_timer_init` all in the `create_conn` function
- ev_init(ev,cb)  //need set *timer.repeat* 
- when `timeout_cb` used: other event are not trigger and after repeat time
```
ev_init(&conn_io->timer, timeout_cb);
conn_io->timer.data = conn_io;

ev_init(&conn_io->keyboard_timer, keyboard_cb);
conn_io->keyboard_timer.data = conn_io;
```
- ev_io_init(ev,cb,fd,events) // trigger when events from fd
```
ev_io_init(&watcher, recv_cb, sock, EV_READ);
ev_io_start(loop, &watcher);
watcher.data = conns;


ev_io_init(&conn_io->vpipe_watcher, vpipe_send_cb, vpipe_fd, EV_READ);
conn_io->vpipe_watcher.data = conn_io;
ev_io_start(loop, &conn_io->vpipe_watcher);
```
- ev_timer_init(ev,cb,after,repeat) // every repeat time once
```
ev_timer_init(&conn_io->my_timer, send_nalu, 0, 0.005);
conn_io->my_timer.data = conn_io;
ev_timer_start(loop, &conn_io->my_timer);
```
- setting timer repeat and use `ev_timer_again`
```
# flush_egress()
conn_io->timer.repeat = 5;
ev_timer_again(loop, &conn_io->timer);

# recv_cb()
conn_io->keyboard_timer.repeat = 0.1;
ev_timer_again(loop, &conn_io->keyboard_timer);
```

## today work
- client can send video to server, self define ``
- problem: when server is closed, client wil not end?
- if set `max_send_len` > 1350 bytes, client error and show buffer is too large?
```
ev_init(&conn_io->send_timer, client_send_cb);
conn_io->send_timer.data = conn_io;
conn_io->send_timer.repeat = 0.001;
ev_timer_again(loop, &conn_io->send_timer);
```
