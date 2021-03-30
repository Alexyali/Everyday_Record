# SRS实现RTMP推流

```
    for((;;)); do \
        ./objs/ffmpeg/bin/ffmpeg -re -i ../fox.flv \
        -vcodec copy -acodec copy \
        -f flv -y rtmp://172.16.7.84/live/livestream; \
        sleep 1; \
    done
```



