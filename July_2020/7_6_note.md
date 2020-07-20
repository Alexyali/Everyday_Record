7-6
---------------------
# client.c
---------------------
int main()
```
ev_io_init(&watcher, recv_cb, conn_io->sock, EV_READ);
ev_io_start(loop, &watcher);
watcher.data = conn_io;

ev_init(&conn_io->timer, timeout_cb);
conn_io->timer.data = conn_io;

ev_init(&conn_io->keyboard_timer, keyboard_cb);
conn_io->keyboard_timer.data = conn_io;

flush_egress(loop, conn_io); //get connect?

ev_loop(loop, 0); //begin loop
```

--------------------
# server.c
--------------------
## 对sock的watcher
```
ev_io watcher;
struct ev_loop *loop = ev_default_loop(0);
ev_io_init(&watcher, recv_cb, sock, EV_READ);
ev_io_start(loop, &watcher);
watcher.data = &c;
```
## 发送nalu的调用层次
- main:
ev_io_init(&watcher, recv_cb, sock, EV_READ);

- recv_cb:
conn_io = create_conn(loop, odcid, odcid_len);

- create_conn:
ev_timer_init(&conn_io->my_timer, send_nalu, 0, 0.005);
conn_io->my_timer.data = conn_io;
ev_timer_start(loop, &conn_io->my_timer);

## recv上行数据
- read = recvfrom()
  if(read < 0) 
    if(errno == EWOULDBLOCK)
      printf("recv would bolck"); 

- done = quiche_conn_recv()
  if(done = QUICHE_ERR_DONE)
    printf("done reading");
  printf("recv %d bytes\n",done);
  
- if (quiche_conn_is_established(conn_io->conn)) 
    while (quiche_stream_iter_next(readable, &s))
      recv_len = quiche_conn_stream_recv()
      if (recv_len > 0) break;
      
- struct sockaddr_storage peer_addr;
  socklen_t peer_addr_len = sizeof(peer_addr);
  memset(&peer_addr, 0, peer_addr_len);
  recvfrom(conns->sock, buf, sizeof(buf), 0, (struct sockaddr *) &peer_addr,&peer_addr_len);

# git 指令
- git clone [http] 下载仓库源码，默认master分支
- git branch -a 查看所有分支
- git checkout *branch* 切换到需要的分支
- git pull 将远程仓库和本地仓库合并
# ffmpeg to audio
```
$ ffmpeg -i inputfile out.aac
```
 
# quiche-pipe
## quic + single stream
- create 4 pipes
```
$ mkfifo svideopipe
$ mkfifo saudiopipe
$ mkfifo cvideopipe
$ mkfifo caudiopipe
```
- ffplay initial
```
$ ffplay -i cvideopipe
$ FFREPORT=file=ffplay_repo.log:level=16 ffplay -i cvideopipe 
$ FFREPORT=file=ffplay_repo.log:level=16 ffplay -i cvideopipe -infbuf
$ FFREPORT=file=ffplay_repo_ori.log:level=16 ffplay -i cvideopipe -infbuf
```
- server send
```
$ ./server 127.0.0.1 1234
```
- client recv
```
$ ./client 127.0.0.1 1234
```
- ffmpeg push media into pipe
```
$ ffmpeg -re -i ~/source.mp4 -codec copy -f mpegts pipe:1 > svideopipe
$ ffmpeg -re -i ~/demo.ts -codec copy -f mpegts pipe:1 > svideopipe
```
notes: run the commands follow the oder, but run *client* and then quickly run *ffmpeg*, otherwise the connection will timeout.

## quic + video & audio streams
- create 4 pipes
```
$ mkfifo svideopipe
$ mkfifo saudiopipe
$ mkfifo cvideopipe
$ mkfifo caudiopipe
```
- ffplay initial
```
$ ffmpeg -i cvideopipe -i caudiopipe -codec copy -f mpegts - | ffplay pipe:0
```
- server send
```
$ ./server 127.0.0.1 1234
```
- client recv
```
$ ./client 127.0.0.1 1234
```
- ffmpeg push media into pipe
```
$ ffmpeg -re -i ~/demo.aac -f mpegts pipe:1 > saudiopipe
$ ffmpeg -re -i ~/demo.h265 -f mpegts pipe:1 > svideopipe
```
notes: still cannot run, some problem emerges when ffmpeg try to combine video stream and audio stream.





