# srs
- 服务器地址
```
ssh enc@172.16.7.84
# pswd: 1
```
- 启动srs
```
./objs/srs -c conf/srs.conf
```
- 退出srs, `ctrl + c`,或如下代码
```
ps -ef|grep srs
kill -9 <pid>
```
- RTMP推流
```
    for((;;)); do \
        ffmpeg -re -i ./doc/source.200kbps.768x320.flv \
        -vcodec copy -acodec copy \
        -f flv -y rtmp://172.16.7.84/live/livestream; \
        sleep 1; \
    done
```
