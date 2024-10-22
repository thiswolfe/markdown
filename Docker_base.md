---
title: Docker的使用
description: Docker是一个开源的应用容器引擎，让开发者可以打包他们的应用以及依赖包到一个可抑制的容器中，然后发布到任何流行的Linux机器上，也可以实现虚拟化。容器完全使用沙盒机制，相互之间不会存在任何接口。几乎没有性能开销，可以很容易的在机器和数据中心运行。最重要的是，他们不依赖于任何语言、框架或者包装系统。
tag:
 - docker
categories:
 - tools
---

Docker 包括三个基本概念:

- **镜像（Image）**：Docker 镜像（Image），就相当于是一个 root 文件系统。比如官方镜像 ubuntu:16.04 就包含了完整的一套 Ubuntu16.04 最小系统的 root 文件系统。
- **容器（Container）**：镜像（Image）和容器（Container）的关系，就像是面向对象程序设计中的类和实例一样，镜像是静态的定义，容器是镜像运行时的实体。容器可以被创建、启动、停止、删除、暂停等。
- **仓库（Repository）**：仓库可看成一个代码控制中心，用来保存镜像。

Docker 使用客户端-服务器 (C/S) 架构模式，使用远程API来管理和创建Docker容器。

Docker 容器通过 Docker 镜像来创建。

容器与镜像的关系类似于面向对象编程中的对象与类。

其他一些说明

**Docker Machine**，是一个简化Docker安装的命令行工具，通过一个简单的命令行即可在相应的平台上安装Docker，比如VirtualBox、 Digital Ocean、Microsoft Azure。

**Docker Registry**，Docker 仓库用来保存镜像，可以理解为代码控制中的代码仓库。Docker Hub [https://hub.docker.com/](https://hub.docker.com/) 提供了庞大的镜像集合供使用。一个 Docker Registry 中可以包含多个仓库（Repository）；每个仓库可以包含多个标签（Tag）；每个标签对应一个镜像。

**Docker Host**，一个物理或者虚拟的机器用于执行 Docker 守护进程和容器。

通常，一个仓库会包含同一个软件不同版本的镜像，而标签就常用于对应该软件的各个版本。我们可以通过 **<仓库名>:<标签>** 的格式来指定具体是这个软件哪个版本的镜像。如果不给出标签，将以 **latest** 作为默认标签。



# 1、拉取镜像

	sudo docker pull centos:centos7


需要用sudo权限，把centos7的镜像pull到本地

# 2、查看容器和镜像

### 2.1、查看容器

	sudo docker ps -a       # 查看本地所有容器，包括已经停止运行的容器
	sudo docker ps          # 查看正在运行的容器


显示字段介绍

- **CONTAINER ID：**容器 ID
- **IMAGE：**使用的镜像
- **COMMAND：**启动容器时后，容器运行的命令
- **CREATED：**容器的创建时间
- **STATUS：**容器状态
- **PORTS：**实际运行端口，若有指定运行端口则会显示指定的端口和默认运行端口，以及连接类型( tcp / udp )
- **NAMES：**容器名字
- **SIZE：**容器全部文件的总大小，也会显示容器大小

	docker inspect [运行的容器ID]   # 查看运行的容器的IP等信息


### 2.2、查看镜像

	sudo docker images

查看本地所有镜像，显示的选项说明:

- **REPOSITORY：**表示镜像的仓库源
- **TAG：**镜像的标签
- **IMAGE ID：**镜像ID
- **CREATED：**镜像创建时间
- **SIZE：**镜像大小

# 3、使用镜像

### 3.1 运行镜像

	sudo docker run [REPOSITORY]:[TAG] /bin/bash
	sudo docker run [IMAGE ID] /bin/bash
	sudo docker run -itd centos:centos7 /bin/bash

- **i**：交互式操作。
- **t**： 终端。
- **d**：后台运行。不使用-d选项，可直接进入操作，退出exit时容器立即释放

### 3.2 使用镜像

	sudo docker exec -it [REPOSITORY]:[TAG] /bin/bash
	sudo docker exec -it [IMAGE ID] /bin/bash
	sudo docker exec -it centos:centos7 /bin/bash

使用 exec 进入镜像，exit 退出时，不会关闭容器

	sudo docker attach [REPOSITORY]:[TAG]
	sudo docker attach [IMAGE ID]

使用 attach 进入镜像，exit 退出时，会关闭容器

### 3.3 退出镜像

进入镜像之后，在命令行输入 `exit` 回车之后就会退出镜像


### 3.4 关闭容器

先找到正在运行的容器ID，`sudo docker ps` 然后获取容器ID

	sudo docker kill [容器ID]
	sudo docker kill 9ce04ed78545

### 3.5 运行已经关闭的容器

	sudo docker start -ia [容器ID]
	sudo docker start -ia 9ce04ed78545


# 4、保存镜像到文件

### 4.1 创建新的镜像

	sudo docker commit [正在运行的容器ID] [新的镜像名]:[新的镜像tag]
	sudo docker commit 9a73028a1c467 mycentos:001


之后会生成新的一个image

### 4.2 查看镜像列表

	sudo docker images


找到新生成的那个image，然后打包到文件

### 4.3 打包到文件

	sudo docker save -o [保存文件名] [镜像名称]:[镜像tag]
	sudo docker save -o mycentos.image.tar mycentos:001

保存文件后缀对于保存文件的内容和格式好像没影响

# 5、加载本地镜像

	sudo docker load -i [镜像文件路径]
	sudo docker load -i ./mycentos.image.tar


通过 `sudo docker images` 可以查看到加载的本地镜像

然后根据正常的步骤使用即可

# 6、删除容器

先用 `sudo docker ps -a` 获取到所有容器的列表，然后通过容器ID删除指定容器


	sudo docker rm [容器ID]
	sudo docker rm 9ce04ed78545 e3f6feba27bb


# 7、删除镜像

先用 `sudo docker images` 或者 `sudo docker image ls` 获取到所有镜像的列表，然后通过镜像ID删除指定镜像


	sudo docker rmi [镜像ID]
	sudo docker rmi 9ce04ed78545 e3f6feba27bb


# 8、传送文件

获取容器ID，`sudo docker ps -a` , 根据容器ID传送接收文件

把本地文件拷贝到指定容器中的位置

	sudo docker cp [本地文件] [容器ID]:[容器内目录]
	sudo docker cp test.txt 79410eed1c51:/home/user

把容器中的文件拷贝到本地指定位置

	sudo docker cp [容器ID]:[容器内目录] [本地位置]
	sudo docker cp 79410eed1c51:/home/user test.txt

# 附加信息

# 1、docker run 更多解释

docker run ：创建一个新的容器并运行一个命令`docker run [OPTIONS] IMAGE [COMMAND] [ARG...]`语法 OPTIONS 说明：


	-a stdin: 指定标准输入输出内容类型，可选 STDIN/STDOUT/STDERR 三项；
	-d: 后台运行容器，并返回容器ID；
	-i: 以交互模式运行容器，通常与 -t 同时使用；
	-P: 随机端口映射，容器内部端口随机映射到主机的端口
	-p: 指定端口映射，格式为：主机(宿主)端口:容器端口
	-t: 为容器重新分配一个伪输入终端，通常与 -i 同时使用；
	--name="nginx-lb": 为容器指定一个名称；
	--dns 8.8.8.8: 指定容器使用的DNS服务器，默认和宿主一致；
	--dns-search example.com: 指定容器DNS搜索域名，默认和宿主一致；
	-h "mars": 指定容器的hostname；
	-e username="ritchie": 设置环境变量；
	--env-file=[]: 从指定文件读入环境变量；
	--cpuset="0-2" or --cpuset="0,1,2": 绑定容器到指定CPU运行；
	-m :设置容器使用内存最大值；
	--net="bridge": 指定容器的网络连接类型，支持 bridge/host/none/container: 四种类型；
	--link=[]: 添加链接到另一个容器；
	--expose=[]: 开放一个端口或一组端口；
	--volume , -v: 绑定一个卷

	-p支持以下几种绑定格式：
	// 绑定宿主机IP及端口
	ip:hostPort:containerPort
	// 绑定宿主机IP
	ip::containerPort
	// 绑定宿主机端口
	hostPort:containerPort


# Docker 疑难解答

# 1、Docker 磁盘空间不足解决办法

修改默认镜像存储目录的软连接

检查空间，一般是这个位置 /var/lib/docker/ 的空间不足

`du -sh /var/lib/docker/`

### 1.1 停止docker

	sudo systemctl stop docker


### 1.2 创建目录

创建目录 /data/docker/lib

	sudo mkdir -p /data/docker/lib


找一个磁盘剩余空间比较多的位置

### 1.3 迁移文件

迁移 /var/lib/docker 目录下面的文件到 /data/docker/lib迁移后的完成docker路径：/data/docker/lib

	sudo rsync -avz /var/lib/docker/ /data/docker/lib/

迁移完成之后，检查目标目录下的文件是否与源目录一致

### 1.4 修改配置文件

方法一：编辑文件 vi /usr/lib/systemd/system/docker.service 修改为如下内容

	[Service]
	ExecStart=/usr/bin/dockerd --gragh=/data/docker/lib

方法二：编辑 /etc/docker/daemon.json 根节点添加如下参数

	{
	 ...
	 "graph":"/data/docker/lib"
	 ...
	}


### 1.5 重启服务

	sudo systemctl daemon-reload
	sudo systemctl restart docker

### 1.6 查看docker信息

	sudo docker info


检查结果中的 Docker Root Dir 是否为设置的 gragh 路径


	[root@localhost ~]# docker info
	...
	Docker Root Dir: "/data/docker/lib"
	Debug Mode (client): false
	Debug Mode (server): false
	Registry: https://index.docker.io/v1/
	...


### 1.7 查看images信息


	sudo docker images


如果images不完整，检查gragh的路径是否合理

可选择：成功之后可以删除原来的docker存储镜像的位置 /var/lib/docker