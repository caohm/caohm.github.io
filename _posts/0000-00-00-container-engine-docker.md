---
title: Docker
layout: post
date: 2017-03-28 16:48:57 +0800
categories: 容器
tags: docker k8s
---

* content
{:toc}
# 简介

2017年的3月1号之后，Docker的版本命名开始发生变化，同时将CE版本和EE版本进行分开。

区别如下：

[Docker社区版（CE）](https://www.docker.com/community-edition)：为了开发人员或小团队创建基于容器的应用,与团队成员分享和自动化的开发管道。
docker-ce提供了简单的安装和快速的安装，以便可以立即开始开发。docker-ce集成和优化，基础设施。（免费）

Docker企业版（EE）：专为企业的发展和IT团队建立谁。docker-ee为企业提供最安全的容器平台，以应用为中心的平台。（付费）








#### hosts

设置hosts

#### JAVA_HOME
```
export JAVA_HOME=/export/servers/jdk1.6.0_25
export JAVA_BIN=/export/servers/jdk1.6.0_25/bin
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export  JAVA_OPTS="-Djava.library.path=/usr/local/lib -server -Xms1024m -Xmx2048m -XX:MaxPermSize=256m -Djava.awt.headless=true -Dsun.net.client.defaultReadTimeout=60000 -Djmagick.systemclassloader=no -Dnetworkaddress.cache.ttl=300 -Dsun.net.inetaddr.ttl=300"
export JAVA_HOME JAVA_BIN PATH CLASSPATH JAVA_OPTS
export LANG=zh_CN.UTF-8
export LC_CTYPE=zh_CN.UTF-8
export LC_COLLATE=zh_CN.UTF-8
```

#### 常用命令

```
docker images

docker rm trade-erp

docker rmi d296cdd735ce

docker rmi : 删除本地一个或多少镜像。
docker rmi image_id image_id2 image_id3
docker rmi -f image_id #删除相同ID的镜像
docker rmi repository:tag

docker cp e:\jdk d296cdd735ce:/jdk

docker commit d296cdd735ce

docker run -i -t --name trade-erp  d296cdd735cf /bin/bash

caohm@caohm-ThinkPad-E450:~$ docker exec -it `(docker ps | grep kafkadocker_kafka| awk '{print $1}')` /bin/bash

 ```

- zookeeper

```
docker run -i -t -–name zookeeper -d zookeeper:latest
# 后台运行-d不能与–rm同时使用
docker logs -f zookeeper

docker run -i -t –link --name zookeeper zookeeper zkCli.sh -server zookeeper

> -p 11211:11211 这个即是默认情况下，绑定主机所有网卡（0.0.0.0）的11211端口到容器的11211端口上
> -p 127.0.0.1:11211:11211 只绑定localhost这个接口的11211端口
> -p 127.0.0.1::5000
> -p 127.0.0.1:80:8080

> -v /tmp/docker:/tmp/docker

> docker run --name nginx_test \
> -v /tmp/docker:/usr/share/nginx/html:ro \
> -p 80:80 -d \
> nginx:1.7.6
#在主机的/tmp/docker下建立index.html，就可以通过http://localhost:80/或http://host-ip:80访问了。

> docker commit <container> [repo:tag]
#只能提交正在运行的container，即通过docker ps可以看见的容器，
```


```

C:\Users\Ram>docker-machine rm default
C:\Users\Ram>docker-machine create --driver virtualbox default

```


```

sudo docker -d


docker save centos > centos.docker
docker load < centos.docker

```


1
打开终端。你将使用“apt-get”命令，这是用于管理已安装程序的通用命令。在卸载程序时，你可能需要输入管理员密码。
当你输入密码时，密码将不会被显示。完成输入后按回车即可。
2
浏览已安装的程序。要查看已安装的软件包列表，请输入以下命令。请注意你希望卸载的软件包的名称。

dpkg --list
3
卸载程序和所有配置文件。在终端中输入以下命令，把<programname>替换成你希望完全移除的程序：

sudo apt-get --purge remove <programname>
4
只卸载程序。如果你移除程序但保留配置文件，请输入以下命令：

sudo apt-get remove <programname>



#### 宿主机同网段

```
#安装pipework
git clone https://github.com/jpetazzo/pipework
cp ~/pipework/pipework /usr/local/bin/
#启动Docker容器。
docker run -itd --name test1 ubuntu /bin/bash
#配置容器网络，并连到网桥br0上。网关在IP地址后面加@指定。
#若主机环境中存在dhcp服务器，也可以通过dhcp的方式获取IP
#pipework br0 test1 dhcp
pipework br0 test1 192.168.191.100/24@192.168.191.254
#将主机eth0桥接到br0上，并把eth0的IP配置在br0上。这里由于是远程操作，中间网络会断掉，所以放在一条命令中执行。
ip addr add 192.168.191.100/24 dev br0; \
    ip addr del 192.168.191.100/24 dev wlp4s0; \
    brctl addif br0 wlp4s0; \
    ip route del default; \
    ip route add default gw 192.168.191.1 dev br0



```

#### Docker commands

官网传送门：

>* [Docker run reference](https://docs.docker.com/engine/reference/run/)
>* [Docker commands](https://docs.docker.com/engine/reference/commandline/)

首先，当然是配置命令自动补全，只需要把一个文件用curl下载copy到特定路径即可，具体操作参考[Command-line Completion](https://docs.docker.com/compose/completion/)

其实docker有很完备的命令帮助提示，对哪个指令不清楚，只需要在后面加`--help`就能看到帮助说明。例如：

- 输入`docker --help`可以看到所有可执行的命令。
- 随便挑一个，比如`run`命令，则输入`docker run --help`又能看到`run`的相关帮助了。

常用命令：

- 查看本地images:

```
docker images
```

- 运行image

```
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
//i.e.
docker run image
docker run -it image /bin/bash
```

常用的一些参数：

- `--rm`:container退出后自动删除
- `-i`和`-t`常常一起用，`-it`:以超级管理员权限打开一个命令行窗口
- `-d`: 后台运行container
- `--name`:给container命名


- 查看当前container

```
docker ps -a
```

- 删除所有状态的container

```
docker rm $(docker ps -a -q)
```

- 通过另外的tty查看已经运行的容器

```
docker exec -it ${container_id} /bin/bash
```

- 查看容器的信息

```
docker inspect ${container_id}
```


另外， 在以上指令中，容器名和容器的container_id都是可以使用的，如果用户没有指定容器名，docker会默认给每个容器分配一个比较友好的随机名称，像fervent_perlman,high_galileo等等



#### Dockerfile

官网传送门：

>* [Dockerfile reference](https://docs.docker.com/engine/reference/builder/)
>* [Best practices for writing Dockerfiles](https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/)

感觉文档里说了很全了，这里稍微提几个容易困惑的点

1.exec form vs shell form

在`CMD`和`ENTRYPOINT`都涉及到着两种形式(`CMD`多一种完全作为参数的形式)，例如:

- `CMD ["executable","param1","param2"]`(exec形式,推荐)
- `CMD command param1 param2` (shell形式)

至于两种形式的区别，官方的几点说明挺详细的，主要就是变量替换，脚本环境等方面有差别：

>* Note: If CMD is used to provide default arguments for the ENTRYPOINT instruction, both the CMD and ENTRYPOINT instructions should be specified with the JSON array format.
>* Note: The exec form is parsed as a JSON array, which means that you must use double-quotes (“) around words not single-quotes (‘).
>* Note: Unlike the shell form, the exec form does not invoke a command shell. This means that normal shell processing does not happen. For example, CMD [ "echo", "$HOME" ] will not do variable substitution on $HOME. If you want shell processing then either use the shell form or execute a shell directly, for example: CMD [ "sh", "-c", "echo $HOME" ].

2.ENTRYPOINT vs CMD

读完官方的[Understand how CMD and ENTRYPOINT interact
](https://docs.docker.com/engine/reference/builder/#/understand-how-cmd-and-entrypoint-interact)，觉得这两者特别相似，对这两者有什么区别和联系还是有些困惑，阅读下面这篇文章：

> [Dockerfile: ENTRYPOINT vs CMD](https://www.ctl.io/developers/blog/post/dockerfile-entrypoint-vs-cmd/)

**简而言之，ENTRYPOINT更像一个写死的可执行指令，CMD更像默认的一个可选项。**

一个image只做一个单一的用途，就像一个可执行的命令时，建议使用ENTRYPOINT，把CMD作为默认参数(第三种形式`CMD ["param1","param2"] (as default parameters to ENTRYPOINT)`)。因为一般而言，ENTRYPOINT是不被覆盖的(除非在run时显式使用--entrypoit),而CMD是defaults的选项，从前文的run命令格式`docker run [OPTIONS] IMAGE [COMMAND] [ARG...]`可知，用户可以在运行images时输入自己的COMMAND来覆盖默认的CMD。


3.ADD vs COPY

这两个好像都是把东西从host拷贝到docker的container里,官方比较如下：

> Although ADD and COPY are functionally similar, generally speaking, COPY is preferred. That’s because it’s more transparent than ADD. COPY only supports the basic copying of local files into the container, while ADD has some features (like local-only tar extraction and remote URL support) that are not immediately obvious. Consequently, the best use for ADD is local tar file auto-extraction into the image, as in ADD rootfs.tar.xz /.

简单来说，主要就两点区别：

- COPY只能把本地文件拷贝到container里；ADD还支持从远程拷贝(remote URL support)
- ADD可以自动解压本地压缩文件

官方建议用COPY(preferred)

参考链接

>* [Reference - ADD or COPY](https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/#/add-or-copy)
>* [Stackoverflow - Docker COPY vs ADD](http://stackoverflow.com/questions/24958140/docker-copy-vs-add)



----

systemctl start docker


#### 安装docker-compose
https://docs.docker.com/compose/install/#alternative-install-options





#### 
docker_cli
``` shell
docker inspect container_name
```



















1、下载镜像
这里使用了wurstmeister/kafka和wurstmeister/zookeeper这两个版本的镜像
[html] view plain copy print?
docker pull wurstmeister/zookeeper  
[html] view plain copy print?
docker pull wurstmeister/kafka  
在命令中运行docker images验证两个镜像已经安装完毕

2、启动
1、启动zookeeper
[html] view plain copy print?
docker run -d --name zookeeper -p 2181 -t wurstmeister/zookeeper  
2、启动kafka
[html] view plain copy print?
docker run --name kafka -e HOST_IP=localhost -e KAFKA_ADVERTISED_PORT=9092 -e KAFKA_BROKER_ID=1 -e ZK=zk -p 9092 --link zookeeper:zk -t wurstmeister/kafka  
可以通过docker ps查看启动状态

3、测试发送消息
执行Docker ps，找到kafka的Container ID，进入容器内部：
[html] view plain copy print?
docker exec -it ${CONTAINER ID} /bin/bash   
进入kafka默认目录
[html] view plain copy print?
cd opt/kafka_2.11-0.10.1.1/   
下面就是跟一般的kafka没什么区别了
创建一个主题：
[html] view plain copy print?
bin/kafka-topics.sh --create --zookeeper zookeeper:2181 --replication-factor 1 --partitions 1 --topic mykafka  
运行一个消息生产者，指定topic为刚刚创建的主题
[html] view plain copy print?
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic mykafka  
运行一个消费者，指定同样的主题
[html] view plain copy print?
bin/kafka-console-consumer.sh --zookeeper zookeeper:2181 --topic mykafka --from-beginning  
这时在生产者输入测试消息，在消费者就可以接收消息了







## MySql 

### start a mysql instance
```bash
docker run --rm --name mysql-server --network host -p 3306 -e MYSQL_ROOT_PASSWORD=123456 -it mysql:5 --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci
```

### connecting via mysql-client
```bash
docker run --rm --name mysql-client --network bridge                                     -it mysql:5 mysql -hcaohm-ThinkPad-E450 -P3306 -uroot -p123456
```

## Redis

### start a redis instance
```bash
docker run --rm --name redis-server --network host -p 6379 -it redis:5
```
### connecting via redis-cli
```bash
docker run --rm --name redis-cli    --network bridge       -it redis:5 redis-cli -h caohm-ThinkPad-E450 -p 6379
```

## RabbitMQ
### start a RabbitMQ instance
```bash
docker run --rm --name rabbit-server --network host -h rabbit-server                            -it rabbitmq:3.7
```
### connecting via rabbitmqctl
```bash
docker run --rm --name rabbit-cli    --network bridge -e RABBITMQ_NODENAME=rabbit@rabbit-server -it rabbitmq:3.7 bash
/# rabbitmqctl list_users
```


#

### docker info
```bash
caohm@caohm-ThinkPad-E450:~$ docker info
Containers: 1
 Running: 1
 Paused: 0
 Stopped: 0
Images: 84
Server Version: 18.09.4
Storage Driver: overlay2
 Backing Filesystem: extfs
 Supports d_type: true
 Native Overlay Diff: true
Logging Driver: json-file
Cgroup Driver: cgroupfs
Plugins:
 Volume: local
 Network: bridge host macvlan null overlay
 Log: awslogs fluentd gcplogs gelf journald json-file local logentries splunk syslog
Swarm: inactive
Runtimes: runc
Default Runtime: runc
Init Binary: docker-init
containerd version: bb71b10fd8f58240ca47fbb579b9d1028eea7c84
runc version: 2b18fe1d885ee5083ef9f0838fee39b62d653e30
init version: fec3683
Security Options:
 apparmor
 seccomp
  Profile: default
Kernel Version: 4.18.0-17-generic
Operating System: Ubuntu 18.04.2 LTS
OSType: linux
Architecture: x86_64
CPUs: 4
Total Memory: 7.538GiB
Name: caohm-ThinkPad-E450
ID: DW3C:4HTD:7ZLR:BHYG:SOMV:CMN2:O4FC:YSKC:GZRS:OVVN:LJX2:IDZT
Docker Root Dir: /var/lib/docker
Debug Mode (client): false
Debug Mode (server): false
Registry: https://index.docker.io/v1/
Labels:
Experimental: false
Insecure Registries:
 172.20.29.2:5000
 registry.cn-shenzhen.aliyuncs.com
 127.0.0.0/8
Registry Mirrors:
 http://172.20.29.2:5000/
Live Restore Enabled: false
Product License: Community Engine

WARNING: No swap limit support
```

### list all images
```bash
caohm@caohm-ThinkPad-E450:~$ docker image ls
```

### list all containers
```bash
caohm@caohm-ThinkPad-E450:~$ docker container ls --all
```

### remove all containers
```bash
caohm@caohm-ThinkPad-E450:~$ docker container ls -aq |xargs docker container rm 
# or
caohm@caohm-ThinkPad-E450:~$ docker container rm $(docker container ls -a -q)
```

### commonds
```bash
docker build -t friendlyhello .  # Create image using this directory's Dockerfile
docker run -p 4000:80 friendlyhello  # Run "friendlyhello" mapping port 4000 to 80
docker run -d -p 4000:80 friendlyhello         # Same thing, but in detached mode
docker container ls                                # List all running containers
docker container ls -a             # List all containers, even those not running
docker container stop <hash>           # Gracefully stop the specified container
docker container kill <hash>         # Force shutdown of the specified container
docker container rm <hash>        # Remove specified container from this machine
docker container rm $(docker container ls -a -q)         # Remove all containers
docker image ls -a                             # List all images on this machine
docker image rm <image id>            # Remove specified image from this machine
docker image rm $(docker image ls -a -q)   # Remove all images from this machine
docker login             # Log in this CLI session using your Docker credentials
docker tag <image> username/repository:tag  # Tag <image> for upload to registry
docker push username/repository:tag            # Upload tagged image to registry
docker run username/repository:tag                   # Run image from a registry
```

