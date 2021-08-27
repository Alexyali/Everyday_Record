# pantheon平台LTE网络下测试

测试各算法在动态LTE网络下的性能。测试指标包括吞吐量和延迟，观察吞吐量和实际带宽的符合程度。

数据所在文件夹`pantheon/result/vary_LTE`

在pantheon文件夹下打开终端，执行如下命令

```
# test congestion control in LTE emulate network, each tests for 5 times, and lasts 60s
$ src/experiments/test.py local --schemes "vegas copa bbr sprout cubic pcc vivace" --random-order --uplink-trace tests/traces/TMobile-LTE-short.up  --downlink-trace tests/traces/TMobile-LTE-short.down --data-dir result/compare/LTE -t 60 --run-times 5
$ src/analysis/analyze.py --data-dir result/compare/LTE
```



|             | 吞吐量 /Mbps | 延迟/ms   | 符合程度     |
| ----------- | ------------ | --------- | ------------ |
| BBR         | 5.49         | **41.57** | 较符合       |
| Sprout      | **6.49**     | 108       | **非常符合** |
| CUBIC       | 6.57         | 5164      | 一般符合     |
| PCC Allegro | 2.10         | 274       | 不符合       |
| PCC Vivace  | 2.30         | 1725      | 不符合       |
| Vegas       | 4.24         | 22.95     | 一般符合     |
| Copa        | 6.11         | 299       | 非常符合     |

各算法吞吐量和延迟的汇总图如下，测试环境为模拟的移动LTE网络

![1128_LTE_all](https://github.com/Alexyali/Everyday_Record/blob/master/Nov_2020/1128_LTE_all.png)

在仿真LTE网络基础上加入3%的丢包率，再次实验，结果显示Srpout和BBR的表现较好，保证了高吞吐量的同时延迟也在100ms以下。

![1128_LTE_loss](https://github.com/Alexyali/Everyday_Record/blob/master/Nov_2020/1128_LTE_loss.png)

Sprout带宽测试结果如下图。结果显示该算法可以很快跟踪网络的带宽变化。

![1128_LTE_Sprout](https://github.com/Alexyali/Everyday_Record/blob/master/Nov_2020/1128_LTE_Sprout.png)

