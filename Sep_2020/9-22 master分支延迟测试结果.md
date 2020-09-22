# 9-22 延迟测试结果

## master分支-pipe

- 时间戳位置为uyvy产生、送入pipe让server发送、client收到和ffplay播放

| No   | uyvy create | push pipe | client recv | ffplay display | delay |
| ---- | ----------- | --------- | ----------- | -------------- | ----- |
| 1    | 29887       | 30132     | 30140       | 30291          | 404   |
| 2    | 81646       | 81882     | 81901       | 82041          | 395   |
| 3    | 37846       | 38075     | 38097       | 38244          | 398   |

## fifo分支

- 时间戳位置为uyvy产生、server准备发送、client收到和ffplay播放

| No   | uyvy create | server push | client recv | ffplay display | delay |
| ---- | ----------- | ----------- | ----------- | -------------- | ----- |
| 1    | 91886       | 92109       | 92155       | 92286          | 400   |
| 2    | 39965       | 40194       | 40217       | 40366          | 401   |
| 3    | 24245       | 24471       | 24513       | 24636          | 391   |

## 时间同步

- 使用`ntpdate`让本机时间和enc同步

```
$ ntpdate -u 172.16.7.84
#22 Sep 15:40:14 ntpdate: adjust time server 172.16.7.84 offset -0.032729 sec
```

- master分支在同步前的端到端延迟为421ms, 同步后为397ms

## 结论

- pipe分支和master分支的总延迟基本一致
- 目测端到端延迟大于理论延迟150ms左右，可能是两台电脑时间不同步以及采集时间