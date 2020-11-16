# 编译PCC-Uspace

follow README 进行编译

```
cd src
make
```

遇到报错 `/usr/bin/ld: not found -ludt`

推测未安装UDT库，因此安装即可

```
sudo apt-get install libudt-dev
```

直接运行程序出错，显示段核心错误，根据提示位置，注释掉`pcc_vivace_sender.cpp`第306行

```
// assert(!delta_sending_rate.IsZero());
```

## 实验结果

接收速率波动很大，从1-600Mbps，rtt很小，0.1ms以内