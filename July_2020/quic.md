# quiche demo

## Build

quiche项目地址：https://github.com/cloudflare/quiche

测试时环境：

```bash
quiche v0.3.0 # aw当时似乎是v0.2.0，一开始尝试最新的v0.4.0不行，最后用v0.3.0
cargo 1.43.0
go version go1.14.4 linux/amd64
```

### dependence

- 编译quiche的依赖
  - cargo
  - golang
- 编译example的依赖
  - libev
  - uthash

### build commend

```bash
# build quiche
$ cargo build
# build example
$ cd examples
$ make server client
```

## libev

libev - a high performance full-featured event loop written in C

### ABOUT LIBEV

Libev is an event loop: you register interest in certain events (such as a file descriptor being readable or a timeout occurring), and it will manage these event sources and provide your program with events.

To do this, it must take more or less complete control over your process (or thread) by executing the *event loop* handler, and will then communicate events via a callback mechanism.

You register interest in certain events by registering so-called *event watchers*, which are relatively small C structures you initialise with the details of the event, and then hand it over to libev by *starting* the watcher.



> To see why, imagine a system with a clock that only offers full second
> resolution (think windows if you can't come up with a broken enough OS
> yourself). 

### WATCHET TYPES
`ev_io` - is this file descriptor readable or writable?
`ev_timer` - relative and optionally repeating timeouts



2. Use a timer and re-start it with `ev_timer_again` inactivity.

This is the easiest way, and involves using `ev_timer_again` instead of `ev_timer_start`.

To implement this, configure an `ev_timer` with a `repeat` value of `60` and then call `ev_timer_again` at start and each time you successfully read or write some data. If you go into an idle state where you do not expect data to travel on the socket, you can `ev_timer_stop` the timer, and `ev_timer_again` will automatically restart it if need be.

That means you can ignore both the `ev_timer_start` function and the `after` argument to `ev_timer_set`, and only ever use the `repeat` member and `ev_timer_again`.

At start:

```
ev_init (timer, callback);
timer->repeat = 60;
ev_timer_again (loop, timer);
```

Each time there is some activity:

```
ev_timer_again (loop, timer);
```

It is even possible to change the time-out on the fly, regardless of whether the watcher is active or not:

```
timer->repeat = 30.;
ev_timer_again (loop, timer);
```

This is slightly more efficient then stopping/starting the timer each time you want to modify its timeout value, as libev does not have to completely remove and re-insert the timer from/into its internal data structure.

It is, however, even simpler than the "obvious" way to do it.

### Usage in server side

- 对sock的io watcher

```c
ev_io watcher;

struct ev_loop *loop = ev_default_loop(0);

ev_io_init(&watcher, recv_cb, sock, EV_READ);
ev_io_start(loop, &watcher);
watcher.data = &c;

ev_loop(loop, 0);
```

- 用于NALU发送的time watcher

```c
ev_timer_init(&conn_io->my_timer, send_nalu, 0, 0.005);
conn_io->my_timer.data = conn_io;
ev_timer_start(loop, &conn_io->my_timer);
```



## Framework

- 二者均需要对socket的watcher

### server

- 增加了一个timer用于发送nalu，在连接建立后启动

```c
ev_timer_init(&conn_io->my_timer, send_nalu, 0, 0.005);
conn_io->my_timer.data = conn_io;
ev_timer_start(loop, &conn_io->my_timer);
```

- `send_nalu`中根据当前时间模拟实时流发送nalu

### client

- 接受包后分析头部：`check_special_signals`
  - NALU头？
  - 指令头？
- 是NALU的话，写入文件（or stdout）



反馈信道：client向server发送请求，server可做出相应处理。

## Test commend

```bash
./server -a 127.0.0.1 -p 1234 -c cert.crt -k cert.key -i ~/Videos/ParkScene.h265 -f 24 -l server.log -r 1  
./client -a 127.0.0.1 -p 1234 -c cert.crt -o recv.h265 -l client.log  | ffplay  -i pipe:0 -probesize 32  -fflags nobuffer -v info
```



