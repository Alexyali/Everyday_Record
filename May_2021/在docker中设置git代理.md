## 在docker中设置git代理

在docker运行脚本对应的git clone之前添加git config --global http.proxy http:

地址为本机的docker虚拟地址，可以在本机运行ifconfig查询

本机的VPN需要支持share over lan模式

