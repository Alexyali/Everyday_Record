# select函数判断socket是否可读写

需要引用头文件

```c++
#include <sys/select.h>
```

首先创建可读文件描述符集合

```c++
static fd_set read_fds;
```

假设已经创建好socket，记作`sock`，那么在每次循环中对fd_set进行初始化

```c++
FD_ZERO(&read_fds);
FD_SET(sock, &read_fds);
```

调用select函数判断状态，这里只判断sock是否可读。设置超时为非阻塞，因此需要将timeval都设为0。如果设为NULL表示阻塞状态。

```c++
while(1) {
    static timeval* t_0 = new timeval();
    t_0->tv_sec = 0;
    t_0->tv_usec = 0;
    int ret = select(sock+1, &read_fds, NULL, NULL, t_0);
}
```

如果ret=1说明sock可读，如果ret=0则说明sock中没有数据

