# vscode终端和终端rust版本不一致

原因：两个终端运行rust的环境变量调用的文件位置不一致

```shell
$ which rustc
# vscode
$ /usr/bin/rustc
# ssh
$ /home/enc/.cargo/bin/rustc
```

解决方法，修改bashrc文件

```shell
$ vim ~/.bashrc
# add line
# export PATH=[path/to/.cargo/bin]:$PATH
# save and exit
$ source ~/.bashrc
```

