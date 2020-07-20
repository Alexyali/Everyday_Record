# known
- 188 bytes one packet
- size upper limit / MAX_SIZE

# question
- if the vbuffer begin at the first packet or not?

# logic
- pointer at the begin of vbuffer
- initial send_size
- while send_size < MAX_SIZE
  pointer += 188;
  send_size += 188;
  end
  pointer -= 188;
  send_size -= 188;
  
# question
- 每次从文件中读取ret大小的数据，保存到vbuffer中
- 然后传输这个ret大小的数据，分多次传输，每次发送大小小于MIN(ret,capacity)
- 问题是每次读取的数据大小不一定是按照ts数据包的长度分割
- pipe中的数据是实时编码传过来的

# solution
- 每次从pipe中读取数据存到vbuffer后，首先找到第一个完整的ts包头，从此处按ts包大小为单位循环传输
- 舍弃最后一个不完整的数据包

## TS流格式
- 固定包长度为188字节
- TS Header包含4个字节，第一个字节是同步字节，其值固定为*0x47*，1bit传输误差指示符，1bit有效载荷单元起始指示符，1bit传输优先级，
  13 bit PID包标识符，2 bit传输加扰控制，2 bit自适应字段控制和4 bit连续计数器。
  
## code
- 读入vbuffer的数据可能最后会分开一个完整的ts包
- 首先找到vbuffer最后一个不完整ts包的头部
- 然后将其存到vbuffer2
- 然后read这个包余下的数据，合并到vbuffer2末尾
- 在quiche_send完vbuffer（最后一个不完整ts包除外）数据后，再次quiche_send在vbuffer2的一个ts包
### error
- pipe broken *solved*
- read failed (read the remain ts packet from fd) 

# 7/11
## 思路
- 首先找到vbuffer里最后一个ts包的包头
- 将最后一个ts包头到文件末尾的数据存入last_vbuf
- 发送vbuffer里完整的ts数据包
- 下一次发送的时候将last_vbuf的数据和下一次vbuffer的数据一起传输

## 细节
- 读入vbuffer, 截取末尾最后一个ts包存入last_vbuf
- 首先发送上一次的数据包片段before_vbuf，然后发送vbuffer
- 将last_vbuf的数据存入before_vbuf
- 如何标识before_vbuf的长度
- broken pipe

# 7/13
- 不修改源码
- 将*MAX_BUF*设为32712(TS LEN的整数倍)
- log输出每次发送的*vbuffer*都包含完整的TS包
- ffplay播放还是会出错
- 问题可能和TS包的属性有关，即某些TS数据包（关键帧）必须一次传输
- 可以参考ffmpeg mpegts代码实现，如何将ts分包传输
- 检查了client，没有丢包，但是server一次发送的数据，client会分几次收

## solved
```
$ ffplay -infbuf 
```
- just add this ffplay cmd, do not need to edit the source code
- it means do not limit the input buffer size, read as much data as possible from the input as soon as possible. Enabled by default for realtime streams,
  where data may be dropped if not read in time.
- no error in ffplay
- however, the penalty is delay become longer...
- it is the problem that player need to solve

# 7-14
## 思路
- client每次写入完整TS包到pipe
- 确定当前收到buf的完整ts长度，去除最后一个不完整ts包
- 如果*last_buf*有剩余数据，则和本次收到的buf一起传输
- 将本次buf的剩余数据写到*last_buf*中

## 思路2
- 将*last_buf*和*buf*的数据合并保存到*temp_buf*
- 截取*temp_buf*包含最大完整TS包的长度，并写入pipe
- 将剩余的最后一个ts包存入*last_buf*



