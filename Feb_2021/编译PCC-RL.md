# 编译PCC-RL

项目地址：[PCC-RL](https://github.com/PCCproject/PCC-RL.git)

- 新建虚拟环境，并激活环境

```shell
$ conda create -n gym_env python=3.6
$ conda activate gym_env
```

- 安装依赖

```
$ pip install gym
$ pip install tensorflow==1.15
$ sudo apt-get update && sudo apt-get install cmake libopenmpi-dev python3-dev zlib1g-dev
$ pip install stable-baselines[mpi]
```

- 运行项目的脚本

```shell
$ cd src/gym
$ python stable_slove.py
```

