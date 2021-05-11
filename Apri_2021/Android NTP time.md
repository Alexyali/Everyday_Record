# Android NTP time

### 进入到设备终端并修改NTP服务器地址

需要安装adb并且将安卓手机以USB调试模式连接到电脑

```shell
$ adb shell settings put global ntp_server <new-ntp-server>
    
# test
$ adb shell settings get global ntp_server
> 172.16.7.84
```

但是好像并不能对齐时间

### 安装ClockSync

参考：[TimeServer](https://www.timeservers.net/sync/ntp/android#:~:text=Android%20operating%20systems%20also%20have%20a%20built-in%20feature,the%20basic%20time%20synchronization%20feature%20works%20under%20Android.)

可以设置NTP服务器并打印服务器和终端的时间戳，并且计算时间偏差，精确到1毫秒。

但是需要安装app，无法嵌入到项目的代码内。

### Github项目TrueTime

地址：https://github.com/instacart/truetime-android.git 

可以在`App.java`里面设置ntp地址，并打印本地真实时间戳和对齐NTP服务器后的时间戳，精确到1毫秒。也可以打印时间偏移量，精确到1毫秒。

优点是可以直接在java程序里面调用。