# UDP套接字编程

套接字可以理解为应用层与传输层进行交互的API接口。因为应用层几乎不能控制传输层，只能将要发送或接收的数据托付传输层处理。采用套接字的API编程就可以完成网络数据的传输。

UDP协议是无连接的不可靠传输。

本文参考链接：[CSDN](https://blog.csdn.net/weixin_44472033/article/details/111769525)

## Server编程

1. 创建服务端的套接字，用于监听端口和发送数据

```c++
int sockfd = socket(AF_INET, SOCK_DGRAM, IPPROTO_UDP);
```

2. 设置监听的端口号，并将其和套接字绑定。这样可以收到发送到该端口的所有数据包

```c++
struct sockaddr_in addr;
addr.sin_family = AF_INET;
addr.sin_port = htons(6000);
addr.sin_addr.s_addr = inet_addr("0.0.0.0");
int ret = bind(sockfd, (struct sockaddr*)&addr, sizeof(addr));
```

3. 利用recvfrom接收数据包和客户端地址

```c++
char buf[1024] = { 0 };
struct sockaddr_in cli_addr;
socklen_t cli_len = sizeof(cli_addr);
recvfrom(sockfd, buf, sizeof(buf) - 1, 0, (sockaddr*)&cli_addr, &cli_len);
```

4. 用sendto将数据发送到客户端

```c++
std::cin >> buf;
sendto(sockfd, buf, strlen(buf), 0, (sockaddr*)&cli_addr, cli_len);
```

5. 关闭连接

```c++
close(sockfd);
```

## Client编程

1. 创建客户端的套接字，不用绑定地址和端口，系统会自动完成

```
int sockfd = socket(AF_INET, SOCK_DGRAM, IPPROTO_UDP);
```

2. 指定发送的地址和端口，并使用sendto发送数据

```c++
// 指定服务端ipv4地址和端口号
struct sockaddr_in svr_addr; 
svr_addr.sin_family = AF_INET;
svr_addr.sin_port = htons(6000);
svr_addr.sin_addr.s_addr = inet_addr("127.0.0.1"); 
socklen_t svr_len = sizeof(svr_addr);
// 输入字符串并发送
char buf[1024] = { 0 };
std::cin >> buf;
sendto(sockfd, buf, strlen(buf), 0, (sockaddr*)&svr_addr, svr_len);
```

3. 接收服务端应答信息

```c++
memset(buf, '\0', sizeof(buf));
recvfrom(sockfd, buf, sizeof(buf) - 1, 0, NULL, NULL);
```

4. 关闭连接

```c++
close(sockfd);
```

## 总结

server和client都需要创建套接字。不同的是server的套接字需要绑定地址和端口，因为它需要一直监听端口是否收到数据。

client在调用sendto发送数据时，需要指定server的地址和端口号。初始化`sockaddr_in`变量设置相关信息。

server在调用recvfrom接收数据时，可以解析client的地址信息，用于发送响应信息。也需要定义`sockaddr_in`变量。
