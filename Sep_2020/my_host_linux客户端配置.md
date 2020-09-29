# quiche通信客户端配置

- 编码器服务器地址和密码

```
$ ssh enc@172.16.7.84
# passwd: 1
# Ipv6 addr: 2001:da8:8000:68a7:acb3:af73:c5b7:99ba
```

- 克隆`my_host_linux` 并切换到`fifo`分支

```
$ git clone http://202.120.39.171/medialab-cmic/my_host_linux.git --recursive
$ git checkout fifo
```

- 编译`quic-hevc`，成功后生成`server`和`client`可执行文件。[如有问题参考](https://github.com/Alexyali/Everyday_Record/blob/master/July_2020/how to build quiche.md)

```
$ cd deps/quic-hevc
$ git checkout hevc-0.3.0-pipe
$ cargo build --examples
$ cd examples
$ make
```

- 编译`my_host_linux`

```
$ cd ../../..
# located in my_host_linux/
$ mkdir build
$ cmake ..
$ make
```

- 假设在enc上采集视频编码发送，本机local收数据并播放，则操作如下：

```
# in enc, first step
$ ./my_host_linux

# in local, make fifo, only once
$ cd deps/quic-hevc/examples
$ mkfifo cvideopipe 

# in local, second step
$ ./ffplay cvideopipe -infbuf -probesize 32 -flags low_delay

# in local, third step
$ ./client 172.16.7.84 2345
```

