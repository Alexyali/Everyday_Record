# 12-8 测试Sprout和Verus算法

两者都是为带宽变化迅速的LTE网络设计

### Sprout

基于接收端的数据包到达时间，短期预测网络带宽，并计算发送多少字节数据，不会让延迟超过某个阈值的可能性超过95%。

在Pantheon下，用mahimahi仿真网络，进行测试

- 在100mbps网络下，延迟只有8ms，吞吐量只有50Mbps？
- 在72Mbps网络下，延迟12ms，吞吐量只有50Mbps 
- 在48Mbps网络下，延迟只有17ms，吞吐量只有43.7Mbps
- 在12mbps网络下，吞吐量11.98Mbps，延迟73ms
- 在Verizon_LTE网络下，吞吐量最高，延迟仅次于BBR和Vegas，对带宽的预测比较准确

### Verus

捕获端到端延迟和未完成的窗口大小的关系，并基于短期数据包延迟变化调整窗口大小

- 在Verizon-LTE下，平均吞吐量小于BBR和Sprout，延迟500ms，远大于BBR
- 在TMobile-LTE下，吞吐量利用率37%，延迟365ms
- 在12mbps下，平均吞吐量9Mbps，延迟180ms
- 在100Mbps下，平均吞吐量66Mbps，延迟257ms

