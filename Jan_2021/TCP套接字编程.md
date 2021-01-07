# TCP套接字编程

TCP是可靠的面向连接的传输层协议。

本文参考链接：[CSDN](https://blog.csdn.net/weixin_44472033/article/details/111769576)

## Server编程

1. 创建TCP连接套接字，`AF_INET`表示ipv4协议，`SOCK_STREAM`表示采用TCP协议

```c++
int sockfd = socket(AF_INET, SOCK_STREAM, IPPROTO_TCP);
```

2. 将连接套接字和IP，端口绑定

```c++
struct sockaddr_in addr;
addr.sin_family = AF_INET;
addr.sin_port = htons(9000);
addr.sin_addr.s_addr = inet_addr("0.0.0.0");
int ret = bind(sockfd, (struct sockaddr*)&addr, sizeof(addr));
```

3. 套接字开始监听

```c++
ret = listen(sockfd, 5);
```

4. 套接字accept，等待连接，并为新连接创建可以读写的新的套接字

```c++
int newsockfd = accept(sockfd, NULL, NULL);
```

5. 使用新的套接字读写数据

```c++
ret = recv(newsockfd, buf, sizeof(buf) - 1, 0);
ret = send(newsockfd, buf, strlen(buf), 0);
```

6. 关闭两个套接字

```c++
close(newsockfd);
close(sockfd);
```

## Client编程

1. 创建客户端套接字，不需要绑定

```
int sockfd = socket(AF_INET, SOCK_STREAM, IPPROTO_TCP);
```

2. 连接到指定IP和端口号的对方主机

```
struct sockaddr_in dest_addr;
dest_addr.sin_family = AF_INET;
dest_addr.sin_port = htons(9000);
dest_addr.sin_addr.s_addr = inet_addr("127.0.0.1");
int ret = connect(sockfd, (struct sockaddr*)&dest_addr, sizeof(dest_addr));
```

3. 连接成功后，还是用同一个`sockfd`进行数据读写

```
ret = send(sockfd, buf, strlen(buf), 0);
ret = recv(sockfd, buf, sizeof(buf) - 1, 0);
```

4. 关闭连接

```
close(sockfd);
```

## 总结

TCP协议和UDP协议相比，server端需要先建立连接才能传输数据。因此server端需要用到两个套接字，一个用于监听端口，另一个用于传输数据。两个套接字通过`accept`函数联系在一起。使用套接字发送数据时，就不需要指定对端的地址和端口信息。

TCP协议的client端相比UDP协议多了一个`connect`的过程，需要建立连接才能读写数据。不过client只需要一个套接字就可以完成数据发送和接收的工作。

