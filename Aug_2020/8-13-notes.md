# 8-13
- 解决了my_linux_host和quiche联合编译的问题
- 解决了运行时tls error的问题，在build下加入cert.key
- todo：逐文件或者逐行commit，然后push forked
- todo：构建信号量或者线程锁在不同进程传递信息，保证server和client建立连接后，data generater才开始工作