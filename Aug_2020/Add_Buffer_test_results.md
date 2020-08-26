# 8-25 add Buffer测试结果

## 第一阶段

- 增加了`Buffer.cpp`和`Buffer.h`. 创建了一个Buffer类，可以存入和取出指定大小的视频数据
- 测试方法：注释掉`server`线程相关的代码，`my_host_linux`仍然采用`stream_collector`的线程。不同的是，从FIFO中get的数据put到`Buffer`对象中，再从`Buffer`中get数据，存入文件。测试结果为可以正常运行，最后写入的文件数据也是完整的。而且没有内存泄漏。
- 测试了`Buffer->get`数据量减半，但是put数据不变，结果是`put too much data into buffer`，因为数据没有及时取出；同理测试`Buffer->put`数据量减半，get数据不变，结果是`get too much data from buffer`. 因为数据取的太多了。

## 第二阶段

- Stream_Collector将数据写入buffer, Server从buffer中读取数据发送
- 首先在`main.cpp`中新建对象`buffer`，初始化后，将buffer作为Stream_Collector的输出，Server的输入
- 在Stream_Collector中put数据到output，也就是指向buffer的对象指针
- 在Server中，从`input`读入buffer指针，存入`vpipe`。在`fifo_cb`中用get从`vpipe`中获取数据存入`vbuffer`，然后使用`stream_send`将其发送
- 问题：`fifo_cb`get数据的时候，提示`buffer is empty`然后就卡死了，解决方法是修改`get`的函数逻辑，设置退出条件 