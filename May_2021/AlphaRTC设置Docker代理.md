# AlphaRTC设置Docker代理

- 无法git clone depot_tools

解决方法：在`Dockerfile.complie`对应的git命令前加入git config代理设置

VPN软件需要开启`share over LAN`选项

```dockerfile
# 172.17.0.1为宿主机对应的docker ip地址
RUN git config --global http.proxy http://172.17.0.1:8081 \ 
&& git config --global https.proxy https://172.17.0.1:8081  \
&& git clone https://chromium.googlesource.com/chromium/tools/depot_tools.git ${DEPOT_TOOLS}
```

- 在docker run命令中无法运行gclient sync

解决方法：在`Dockerfile.complie`和`Dockerfile.release`中加入环境变量设置

```dockerfile
FROM ubuntu:18.04
# 添加HTTP代理地址，宿主机的ipv4地址，端口为VPN接口
ENV HTTP_PROXY "http://192.168.95.128:8081"
ENV HTTPS_PROXY "http://192.168.95.128:8081"

RUN apt-get update && apt-get install -y \
    git curl wget python libglib2.0-dev clang
```

