## CCMakefile

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
