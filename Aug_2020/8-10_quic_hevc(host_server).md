# quic_hevc(host_server)
- 合并host和server端程序

## 修改server
- 不从pipe中读取数据发送，而是从buffer中读取数据发送
- 因此需要使用`ev_init`并设置定时触发时间
- 结果：server把本地视频文件存入buffer，并用一个回调函数来发送

## 对接编码器
- 编码器会输出每一帧的码流，假设存在Data_Pack内，从里面解析出码流数据，存入buffer中，直接送给server发送

## 下一步任务
- 学会makefile
- 学会cmakelist
- 联合编译quiche和my_host
- 在Cmakelist中添加quiche的依赖关系

## Makefile
- os:=$(shell uname) 获取系统信息
- SOURCE_DIR 源码目录
- BUILD_DIR = $(CURDIR)/build 当前目录下的build文件夹
- LIB_DIR 静态库
- INCLUDE_DIR 包含目录