# demo_reno

## select_packet
- enumerate() 将列表组合为一个索引序列，返回索引和对应的元素
- for idx, item in enumerate(packet_queue) #枚举queue里的数据包item和序号idx
- 穷尽队列里所有数据包，返回最佳的包序号，符合is_better(packet)的要求

### is_better
- 首先优先判断当前块是否符合ddl要求，如果是，选择当前块
- 其次如果当前块创建时间和其他块的不一样，则判断当前块是否创建时间更接近当前时刻，如果是，选择当前块
- 如果不满足ddl要求而且两个块的创建时间相同，如果当前块更紧急(delta_t * ddl更大)，返回当前块