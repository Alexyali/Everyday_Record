# 10-20 network test

在WIFI下进行测试。

## ping网络RTT和丢包率

- 使用ping对`172.16.7.84`进行网络探测。结果显示网络无丢包。但是RTT范围从1.6ms到1747ms，而且标准差为339ms。

- 视频帧传输延迟的范围波动很大，为0-120ms

## iperf 网络吞吐量测试

服务端启动`iperf -s`

客户端启动`iperf -c 172.16.7.84`

- `-n [K|M|G]`指定每次发送包的字节数
- `-i` 每次报告的时间间隔
- `-t` 传输数据包的总时间

## ufw 防火墙设置

- `sudo ufw enable` 开启ufw服务
- `sudo ufw disable`关闭ufw服务
- `sudo ufw status`查看防火墙状态
- `sudo ufw alllow <port>`

## tc 流量控制

- `sudo tc qdisc show dev eno2` 显示网络情况
- `sudo tc qdisc del dev eno2 root` 删除网络限制
- `sudo tc qdisc add dev eno2 root netem loss 10%` 增加10%丢包率

## ntp 同步设置

- 目标是客户端（本机）同步服务器时间
- `sudo apt install ntp` 安装ntp
- 修改client端ntp配置文件。`sudo vim /etc/ntp.conf`进入vim界面后注释掉linux自带时间同步

```
#linux自带的时间同步，需要注释掉
#pool 0.ubuntu.pool.ntp.org iburst
#pool 1.ubuntu.pool.ntp.org iburst
#pool 2.ubuntu.pool.ntp.org iburst
#pool 3.ubuntu.pool.ntp.org iburst

# Use Ubuntu's ntp server as a fallback.
#pool ntp.ubuntu.com
server 172.16.7.84
```

- 执行完修改后退出保存，然后重启ntp服务`service ntp restart`
- 在client上执行`ntp -p` 查看ntp服务是否配置完成