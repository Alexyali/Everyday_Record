# 在Pantheon中加入UDT CC算法

运行`src/experiments/setup.py --setup --schemes "<cc>"`时报错

```
OSError: [Errno 13] Permission denied
```

原因是`./wrapper/udt_cc.py`并不是可执行文件

解决方法：在终端输入命令，将py文件变为可执行文件。

```
chmod a+x ./src/wrapper/cc.py
```

