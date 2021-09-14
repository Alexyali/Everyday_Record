# Docker常用命令

Docker分为镜像和容器，镜像可以看作是抽象的类，容器是镜像的实例化，只能启动运行容器。

### 镜像命令

- 下载镜像

```bash
docker pull [image]
```

- 修改镜像标签，实际上复制了一个新的镜像

```bash
docker image tag [prev_image_name] [new_image_name]
```

- 查看镜像

```bash
docker images
```

- 创建镜像

```bash
docker build [OPTIONS] PATH 
# PATH:构建执行所在的本地路径
# -f:指定要使用的Dockerfile路径
# -t:镜像的名称和标签
# 例子：在target_dir目录下，根据release_dockerfile建立镜像release_docker
docker build $(target_dir) -f $(release_dockerfile) -t $(release_docker)
```



### 容器命令

- 首次运行容器 `docker run`

```bash
docker run [OPTIONS] IMAGE [COMMAND] [ARG]
# 在后台运行(-t) 运行完释放容器(--rm) 使用终端运行(--it)
# 在/app下挂载主机地址(-v `pwd`/host/path:/container/path)
# 指定在容器的运行地址(-w /run/path)
# 命名容器(--name [name])
# 例子：以alphartc镜像新建同名容器，并运行peerconnection_serverless receiver.json
sudo docker run -d --rm -v `pwd`/examples/peerconnection/serverless/corpus:/app -w /app --name alphartc alphartc peerconnection_serverless receiver_pyinfer.json
```

- 在运行的容器中执行命令 `docker exec`

```bash
docker exec [OPTIONS] CONTAINER COMMAND [ARG]
# 例子：在容器alphartc中运行peerconnection_serverless sender_pyinfer.json
sudo docker exec alphartc peerconnection_serverless sender_pyinfer.json
```

- 查看所有存在的容器

```bash
docker ps -a
```

- 删除容器

```bash
# 删除指定容器
docker rm -f <containerid>
# 删除退出状态的容器
docker rm $(docker ps -qf status=exited)
# 删除所有未运行的容器
docker rm $(docker ps -a -q)
```

- 在容器中安装TC

```bash
$ docker run -it --name alphartc alphartc
root@id:/# apt-get update
root@id:/# apt-get install iproute2
```

