## PCC-RL编译过程

新建虚拟环境

- 需要预先安装anaconda
- `conda create -n [env] python==3.7`  
- `conda activate [env]`

安装相关依赖

- gym: `pip install gym`
- tensorflow: `pip install tensorflow==1.15`
- stable_baselines: 
  - prerequisites: `sudo apt-get update && sudo apt-get install cmake libopenmpi-dev python3-dev zlib1g-dev`  
  - stable release: `pip install stable-baselines[mpi]`  
- 