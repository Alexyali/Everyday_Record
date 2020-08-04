# srt encoder

## srt_congestion_init()
- 初始化参数
- s_cwnd_gain = 1.3, s_current_state = STATE_KEEP
- s_enc_ctx = NULL

## update_rtt(int rtt)
- 每次调用该函数，将`rtt`存入长度为6的数组s_rtt_array
- 每当数组满了之后，选择最小值作为s_rtt_min

## update_maxbw(long maxbw)
- 将maxbw存入长度为6的数组
- 每当数组填满，选择最大值作为s_bw_max
- 计算带宽平均值s_avg_bw

## srt_bitrate_get_state(int inflight)
- 定义两个很大的正负常数INCR和DECR
- 计算bdp和inflight
- 当前状态 = 1.3 * bdp - inflight
- 将当前状态存入长度为6的数组 
- 如果数组未满，则返回KEEP状态；如果满了则对数组的状态求和，作为final_state
- 如果final_state > INCR, 则返回INCR状态，因为此时的inflight数据少于期望值
- 否则如果final_state < DECR, 返回DECR状态，inflight数据超过bdp太多，需要降低速率
- 如果都不满足，则返回KEEP状态

## reset_vencode_bitrate()
- 每隔500us触发一次
- 如果当前状态为INCR
	- s_current_bitrate = s_enc_ctx->bit_rate * INCR_RATION
	- 如果s_current_bitrate < s_video_bitrate * 1.3，则更新s_enc_ctx的参数，并增大参数值
- 如果当前状态为DECR
	- s_current_bitrate = s_enc_ctx->bit_rate * DECR_RATION
	- 如果s_current_bitrate > s_video_bitrate * 0.4，则降低s_enc_ctx的相应参数

## congestion_ctrl(AVFormatContext * s)
- 每隔固定时间触发，300ms
- 从srt中读取inflight, bw和rtt
- 调用`update_rtt`, `update_maxbw`
- s_congestion_state = srt_bitrate_get_state(inflight)