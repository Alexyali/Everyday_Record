# Linux终端配置VPN

> 在Linux内安装VPN后可以打开谷歌网页，但是终端仍然无法连接谷歌地址。解决方法如下：

使用的VPN: lightyear

查找到VPN服务器提供的local HTTP port，例如为1196

在终端打开`~/.bashrc`

```
$ vim ~/.bashrc
```

在文件末尾添加以下内容

```
export http_proxy="http://127.0.0.1:1196"
export https_proxy="https://127.0.0.1:1196"
```

修改完，先按ESC，再输入`:wq`回车保存并退出。

接着在终端执行:

```
$ source ~/.bashrc
```

测试是否可以下载google网页，在终端输入

```
$ wget www.google.com
```

结果显示下载成功

```
--2021-01-26 14:59:31--  http://www.google.com/
Connecting to 127.0.0.1:1196... connected.
Proxy request sent, awaiting response... 200 OK
Length: unspecified [text/html]
Saving to: ‘index.html’

index.html              [ <=>                ]  13.80K  77.8KB/s    in 0.2s    
```