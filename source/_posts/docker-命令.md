---
title: "docker 命令"
date: 2022-07-24 20:56:05
---
### 启动容器

- 启动容器并启动bash（交互方式）:

```plaintext
$docker run -i -t <image_name/continar_id> /bin/bash
```

- 启动容器以后台方式运行(更通用的方式）：

```plaintext
$docker run -d -it  image_name
```

ps：这里的 image_name 包含了tag：hello.demo.kdemo:v1.0

### 附着到容器

- 附着到正在运行的容器

```plaintext
docker attach <id、container_name>
```

- 进入正在运行的容器内部，同时运行bash(比attach更好用)

```plaintext
docker exec -t -i <id/container_name>  /bin/bash
```

ps：docker exec是如此的有用，以至于我们通常是将其封装为一个脚本，放到全局可调用的地方，比如，可以写成一个indocker.sh：

```plaintext
$cat indocker.sh 
docker exec -t -i $1 /bin/bash
# 查看需要附着的容器id
$docker ps | less -S
CONTAINER ID        IMAGE                                                 
9cf7b563f689        hello.demo.kdemo:v160525.202747

$./indocker.sh 9cf7b563f689 
```

### 查看容器日志

- 查看容器日志

```plaintext
docker logs <id/container_name>
```

- 实时查看日志输出

```plaintext
docker logs -f <id/container_name> (类似 tail -f) (带上时间戳-t）
```

### 查看容器

- 列出当前所有正在运行的container

```plaintext
$docker ps
```

- 用一行列出所有正在运行的container（容器多的时候非常清晰）

```plaintext
$docker ps | less -S
```

- 列出所有的container

```plaintext
$docker ps -a  
```

- 列出最近一次启动的container

```plaintext
$docker ps -l 
```

- 显示一个运行的容器里面的进程信息

```plaintext
$docker top Name/ID  
```

- 查看容器内部详情细节：

```plaintext
$docker inspect <id/container_name>
```

- 在容器中安装新的程序

```plaintext
$docker run image_name apt-get install -y app_name  
```

> Note： 在执行apt-get 命令的时候，要带上-y参数。如果不指定-y参数的话，apt-get命令会进入交互模式，需要用户输入命令来进行确认，但在docker环境中是无法响应这种交互的。apt-get 命令执行完毕之后，容器就会停止，但对容器的改动不会丢失。

- 从容器里面拷贝文件/目录到本地一个路径

```plaintext
$docker cp Name:/container_path to_path  
$docker cp ID:/container_path to_path
```

- 保存对容器的修改（commit） 当你对某一个容器做了修改之后（通过在容器中运行某一个命令），可以把对容器的修改保存下来，这样下次可以从保存后的最新状态运行该容器。

```plaintext
$docker commit ID new_image_name  
```

> Note： image相当于类，container相当于实例，不过可以动态给实例安装新软件，然后把这个container用commit命令固化成一个image。

- 删除单个容器

```plaintext
$docker rm Name/ID 
```

> -f, –force=false(强制删除); -l, –link=false Remove the specified link and not the underlying container; -v, –volumes=false Remove the volumes associated to the container

- 删除所有容器

```plaintext
$docker rm `docker ps -a -q`  
```

- 停止、启动、杀死、重启一个容器

```plaintext
$docker stop Name/ID  
$docker start Name/ID  
$docker kill Name/ID  
$docker restart name/ID
```

## 操作Image

- 列出镜像

```plaintext
$sudo docker images
```

> -a, –all=false Show all images; –no-trunc=false Don’t truncate output; -q, –quiet=false Only show numeric IDs

- 从dockerhub检索image

```plaintext
$docker search image_name
```

- 下载image

```plaintext
$docker pull image_name
```

- 删除一个或者多个镜像;

```plaintext
$docker rmi image_name  
```

> -f, –force=false Force; –no-prune=false Do not delete untagged parents

- 显示一个镜像的历史;

```plaintext
$docker history image_name
```

- 发布docker镜像

```plaintext
$docker push new_image_name
```

ps:要发布到私有Registry中的镜像，在镜像命名中需要带上Registry的域名（如果非80端口，同时需要带上端口号）比如：

```plaintext
$docker push dockerhub.yourdomain.com:443/hello.demo.kdemo:v1.0
```

- 拉取docker镜像

```plaintext
$docker pull image_name
```

## 网络操作

- 查看docker0的网络(宿主机上操作)

```plaintext
$ip a show docker0
```

- 查看容器的IP地址

```plaintext
$docker inspect -f '{{ .NetworkSettings.IPAddress }}' <id、container_name>
```

附着到容器内部查看其内部ip：

```plaintext
$ip a show eth0
```

### 构建容器：

使用当前目录的 Dockerfile 创建镜像，标签为 runoob/ubuntu:v1。

```plaintext
docker build -t runoob/ubuntu:v1 .
```

使用URL **github.com/creack/docker-firefox** 的 Dockerfile 创建镜像。

```plaintext
docker build github.com/creack/docker-firefox
```

也可以通过 -f Dockerfile 文件的位置：

```plaintext
$ docker build -f /path/to/a/Dockerfile .
```

在 Docker 守护进程执行 Dockerfile 中的指令前，首先会对 Dockerfile 进行语法检查，有语法错误时会返回：

```plaintext
$ docker build -t test/myapp .
Sending build context to Docker daemon 2.048 kB
Error response from daemon: Unknown instruction: RUNCMD
```
