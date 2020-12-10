# FFmpeg 指令大全

- **使用ffmpeg将ts流转为yuv：**`ffmpeg -i in.ts out,yuv`
- **ffplay播放yuv：**`ffplay -video_size 1920x1080 out.yuv`
- **ffmpeg分割视频：**`ffmpeg -ss 00:00:00 -t 00:00:30 -i test.mp4 -vcodec copy output.mp4`
- **比较两个视频相似程度：**`ffmpeg -i test1.ts -i test2.ts -vframes 1 -lavfi psnr -f null -`  vframes表示对比的帧数，lavfi表示评价指标