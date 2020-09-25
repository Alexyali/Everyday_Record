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

- master分支在同步前的端到端延迟为397ms, 同步后为374ms
- 使用`clockdiff`检查两台主机的时间差，时间差在10-20ms左右

```
# at local machine
$ sudo clockdiff 172.16.7.84
# host=172.16.7.84 rtt=0(0)ms/0ms delta=13ms/12ms Tue Sep 22 16:56:22 2020
```

- master分支测试端到端延迟为368ms，fifo分支测试延迟为366ms

## 结论

- pipe分支和master分支的总延迟基本一致
- 两台机器的系统时间差在30ms内，因此测试的理论端到端延迟误差也在30ms内
- 目测端到端延迟大于理论延迟100ms左右，具体原因需要进一步分析