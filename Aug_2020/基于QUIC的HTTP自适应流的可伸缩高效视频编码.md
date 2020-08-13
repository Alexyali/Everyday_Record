# 基于QUIC的HTTP自适应流的可伸缩高效视频编码
`Scalable High Efficiency Video Coding based HTTP Adaptive Streaming over QUIC`  
`EPIQ’20, August 10–14, 2020`  

## ABSTRACT
- HTTP/2仍然存在头部阻塞和TCP三次握手的问题
- QUIC运行在UDP上，可以解决这些问题
- 现有的ABR算法可以针对可伸缩和不可伸缩的视频流，但是缺少同时为两种视频流设计的算法
- 我们调查了QUIC和HTTP/2在ABR算法上的性能