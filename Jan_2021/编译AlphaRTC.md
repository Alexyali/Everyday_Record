# 编译AlphaRTC

系统：Linux 18.04

需要终端支持vpn，可以连接到google

需要在python2的环境下执行，这里用的是conda创建好的环境

```shell
conda activate [python2]
```

需要提前git clone [depot_tools](https://chromium.googlesource.com/chromium/tools/depot_tools.git)并在`~/.bashrc`中配置环境变量

```shell
vim ~/.bashrc
# add a line in .bashrc
# export PATH="/path/to/depot_tools:$PATH"
# esc with wq
source ~/.bashrc
```

用git下载AlphaRTC，这里直接编译，没有采用docker。因为还要设置docker代理比较麻烦

```shell
git clone https://github.com/OpenNetLab/AlphaRTC.git
cd AlphaRTC
gclient sync
```

如果碰到找不到`tools_webrtc`的问题，在`AlphaRTC/`下就可以找到，拷贝文件夹到`src/`即可

```shell
cp -r tools_webrtc/ src/  
```

如果碰到git出错的问题，在终端进入问题所在的目录并执行`git stash`

生成build规则

```shell
gn gen out/Default
```

编译

```shell
ninja -C out/Default peerconnection_serverless
```

## DEMO

1. Copy the provided corpus to a new directory

```shell
cp -r examples/peerconnection/serverless/corpus/* /path/to/your/runtime
```

2. Copy the essential dynanmic libraries and add them to searching directory

```shell
cp modules/third_party/onnxinfer/lib/*.so /path/to/your/dll
cp modules/third_party/onnxinfer/lib/libonnxruntime.so.1.5.2 /path/to/your/dll
```

3. Start the receiver and the sender in two terminal, 

```shell
# open recv terminal
cd /path/to/your/runtime
export LD_LIBRARY_PATH=/path/to/your/dll:$LD_LIBRARY_PATH
/path/to/alphartc/out/Default/peerconnection_serverless ./receiver.json

# open send terminal
export LD_LIBRARY_PATH=/path/to/your/dll:$LD_LIBRARY_PATH
/path/to/alphartc/out/Default/peerconnection_serverless ./sender.json
```

After these commands terminates, you will get `outvideo.yuv` and `outaudio.wav`.