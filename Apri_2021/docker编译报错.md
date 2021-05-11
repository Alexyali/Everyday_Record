# docker编译报错-proxy

问题

```shell
$ Error response from daemon: Get https://opennetlab.azurecr.io/v2/: proxyconnect tcp: dial tcp 127.0.0.1:1196: connect: connection refused
```

分析原因：docker的代理设置错误

解决方法：找到docker的服务设置，并且修改代理。重新启动docker

```shell
$ locate docker.service
$ sudo vim /etc/systemd/system/docker.service.d/http-proxy.conf
$ systemctl daemon-reload
$ sudo service docker restart
```



## docker下载异常

报错：

```
Error response from daemon: dial tcp: lookup index.docker.io: no such host
```

原因：docker找不到域名对应的ip地址

解决方法：修改DNS服务器地址

```shell
$ vim /etc/systemd/resolved.conf
# 修改域名
# DNS=8.8.8.8
$ sudo /etc/init.d/networking force-reload
$ sudo /etc/init.d/networking restart
# 重启
$ reboot
```

