# Pantheon编译方法

The Pantheon contains wrappers for many popular practical and research congestion control schemes. The Pantheon enables them to run on a common interface, and has tools to benchmark and compare their performances. Pantheon tests can be run locally over emulated links using [mahimahi](http://mahimahi.mit.edu/) or over the Internet to a remote machine.

### 安装conda

conda用于创建python2.7的虚拟环境。pantheon在python2.7下运行。

安装Anaconda后无法使用conda：输入`vim ~/.bashrc` 然后在文档末尾加入

```
export PATH="/home/username/anaconda3/bin:$PATH"
```

保存退出以后在终端运行`source ~/.bashrc` 最后输入`conda --version` 查看conda版本

### Step 1

git clone pantheon，并根据README下载各个子模块。建议在子模块中去除`webrtc`和`proto-quic`。因为下载资源太耗时。

```
$ git submodule deinit third_party/proto-quic
$ git rm --cached third_party/proto-quic
$ git submodule deinit third_party/webrtc
$ git rm --cached third_party/webrtc
$ git submodule update --init --recursive
```

如果`pantheon-tunnel`等模块出现问题，也可以自己用git clone到master分支。

### Step 2

使用conda建立python2.7的虚拟环境

```
$ conda create -n pantheon python=2.7
$ conda init
$ conda activate pantheon
```

### Step 3

- 根据README安装依赖`tools/install_deps.sh` 记得先激活上一步创建的pantheon环境

这一步可能报错`sudo add-apt-repository ppa:keithw/mahimahi` 无法执行，但是不影响mahimahi安装，可以不用管。

- 然后为各个CC算法安装依赖

```
src/experiments/setup.py --install-deps --all
```

### Step 4

- 启动CC算法

```
src/experiments/setup.py [--setup] [--all | --schemes "<cc1> <cc2> ..."]
```

- 运行本地仿真实验

```
src/experiments/test.py local (--all | --schemes "<cc1> <cc2> ...") 
```

`--data-dir DIR` 是存储实验结果数据的文件夹，用于生成结果报告，必须加上

`-t run time` 设置运行时间长度，最大为60s. 后续可以看能不能修改

`--uplink-trace TRACE_DIR` 数据从发送端到接收端走的网络路径，这里写trace文件所在位置

`--downlink-trace TRACE_DIR`  同上，不过是从接收端到发送端

网络路径在`/usr/share/mahimahi/traces`中，提供了多个移动网络下的仿真路径，例如LTE和3G网络。

更多路径可以在[observatory](https://github.com/StanfordSNR/observatory)中找到。

- 生成实验报告

```
src/analysis/analyze.py --data-dir DIR
```

打开`pantheon_report.pdf`查看本次实验全部统计信息和分析结果

如果生成出错，查看是否有包没有下载。如果是，用pip install对应的包即可。例如：

```
pip install numpy matplotlib pyyaml
```

