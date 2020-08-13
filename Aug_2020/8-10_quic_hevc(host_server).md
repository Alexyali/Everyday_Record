# quic_hevc(host_server)
- 合并host和server端程序

## 修改server
- 不从pipe中读取数据发送，而是从buffer中读取数据发送
- 因此需要使用`ev_init`并设置定时触发时间
- 结果：server把本地视频文件存入buffer，并用一个回调函数来发送

## 对接编码器
- 编码器会输出每一帧的码流，假设存在Data_Pack内，从里面解析出码流数据，存入buffer中，直接送给server发送
- 新建一个quiche类
- 在不同线程之间传递参数，将`server`的建立连接信息传入`Data_Generator`

## 下一步任务
- 学会makefile
- 学会cmakelist
- 联合编译quiche和my_host
- 在Cmakelist中添加quiche的依赖关系

## Makefile
- 基本规则
```
target [...] : [dependent ...]
[command ...]
# example
hello: main.o hello.o
	$(CC) main.o hello.o -o hello
````
- os:=$(shell uname) 获取系统信息
- SOURCE_DIR 源码目录
- BUILD_DIR = $(CURDIR)/build 当前目录下的build文件夹
- LIB_DIR 静态库
- INCLUDE_DIR 包含目录
- CFLAGS 指定头文件的路径
- LDFLAGS 指定库文件的位置
- LIBS 告诉链接器要链接哪些库文件
- CC 编译C程序的程序，默认是`cc`
- $@ 是要制作的文件的名称
- $< 引起操作的相关文件的名称
- $(dir$(shell find -name * .a)) 找到对应静态库的目录
- `-I` gcc adds include directory of header files
- `-L` gcc looks in directory for library files
- $() 表示这是一个变量名

## client/server makefile
- 依赖 quiche.h, libquiche.a, libcrypto.a, libssl.a
```
CFLAGS = -I. -Wall -Werror -pedantic -fsanitize=address -g 
# flags for gcc compiler
LDFLAGS = -L$(LIB_DIR) # indicate lib dir
INCS = -I$(INCLUDE_DIR) # indicate include dir
LIBS = $(LIB_DIR)/libquiche.a -lev -ldl -pthread

# create server
server: server.c $(INCLUDE_DIR)/quiche.h $(LIB_DIR)/libquiche.a
$(CC) $(CFLAGS) $(LDFLAGS) $< -O $@ $(INCS) $(LIBS)

# equal to below
cc -I. -Wall -Werror -pedantic -fsanitize=address -g -L../lib server.c -o server -I../include ../lib/libquiche.a -lev -ldl -pthread
```

## CMakelist
- 添加头文件目录
```
include_directories(../../../thirdparty/comm/include)
```
- 添加需要链接的库文件目录
```
link_directories("/home/server/third/lib")
```
- 添加需要链接的库文件路径
```
link_libraries(“/home/server/third/lib/libcommon.a”)
```
- 添加FLAGS
```
ADD_DEFINITIONS(-g -Wall)
```

## 修改后的CMakeList
- located in `quiche/`
```
# cmake version
cmake_minimum_required(VERSION 3.5)

# set project name
project(quiche_module)

# extra flags for gcc compiler
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -I. -Wall -Werror -pedantic -fsanitize=address -g")

# include dir of head files
include_directories(include)

# link dir of libs
link_directories(examples/build/debug)

# find Threads package
find_package(Threads REQUIRED)

# generate executable file
add_executable(server ${PROJECT_SOURCE_DIR}/examples/server.c)

# link target with libs
target_link_libraries(server
	quiche
	ev
        dl
	Threads::Threads
	)

# the same as server
add_executable(client ${PROJECT_SOURCE_DIR}/examples/client.c)
target_link_libraries(client
        quiche
        ev
        dl
        Threads::Threads
        )
```

## 8-12 todo and done
- 已经在`my_host_linux`中编译`quiche`，可以同时运行编码器和服务端
- 需要在`SERVER`类和其他类的函数之间传递参数
	- 关于静态变量和静态函数的问题
	- quiche的函数都是静态函数，无法给类的变量赋值
	- 使用信号量而不是类的成员变量在进程之间传递信息

## C++ 类
- `private`用来指定私有成员，只能在该类的成员函数内部访问
- `public`用于指定公有成员，在任何地方都可以访问
- 在类中，静态成员可以实现多个对象之间的数据共享
- 静态成员函数没有this指针，不能返回非静态成员

## 在SERVER增加conn_flag变量
- Create connection flags in class `SERVER` to deliever status, located in `Quiche_Server.h`
```
class SERVER{
public:
	static int conn_flag
	}
```
- inital `conn_flag` in `Quiche_Server.cpp`, located in the outside of functions
```
int SERVER::conn_flag = 0;
```
- set `conn_flag = 1` when `quiche_conn_is_established`
- use `SERVER::conn_flag` in `Data_Generator.cpp` to control start

## 问题
- 输出台有很多个new connection, 怀疑重复调用了很多次
- 原因：CMakeList的问题，可能没有包括ssl的库，导致quiche的tls部分出错
- 办法：修改quiche module的CMakeLists，最后的结果应该和Makefile编译的运行一致

## 8-13 测试
- 修改`target_link_libraries`中crypto, ssl的位置到quiche之前
-`ldd file..`打印共享对象依赖关系
- 在github/quiche上发布issue `#617`
- `build_cmake/CMakeFiles/client.dir/link.txt` 查看cmake的编译信息
- 问题原因：`cert.crt`和`cert.key`没有在`build_cmake`文件夹内

## my_host_linux && server
### done
- my_host_linux can compile quiche libraries
- add a new thread in main.cpp to run server
- set define  SIMULATE to control read video from file or true stream 



