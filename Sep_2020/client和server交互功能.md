# client和server交互功能

- 预备工作：在enc上建立自己的文件夹，并且下载my_host_linux. 切换到fifo分支进行更改。需要自己在gitlab上fork实验室

```
# login to enc
$ ssh enc@172.16.7.84
$ make your folder
$ mkdir <folder_name>
$ cd <folder_name>
$ git clone 
```



- 系统整体流程：run *my_host_linux* in server side-> run *ffplay* in client side -> run *client*. 在客户端可以看到即时播放的视频。此时在client对应的终端键入字母，server端可以收到并输出cleint发送的信息。

```
# at enc side
$ ./my_host_linux 
# at client side
$ ./ffplay -i cvideopipe
$ ./client 172.16.7.84 2345
# type one word to client
# server side(my_host_linux) will output the semphore
```

- 当my_host_linux的Send_Server线程成功收到client的数据时，将该数据保存到Send_Server的静态成员变量`uint8_t * client_message`中，并传递给主线程，在线程结束后输出该信息。