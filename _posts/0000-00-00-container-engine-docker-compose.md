---
title: Docker compose
layout: post
date: 2017-03-28 16:48:57 +0800
categories: 容器
tags: docker-compose k8s
---

* content
{:toc}
Compose项目来源于之前的fig项目，使用python语言编写,与docker/swarm配合度很高。

Compose 是 Docker 容器进行编排的工具，定义和运行多容器的应用，可以一条命令启动多个容器，使用Docker Compose不再需要使用shell脚本来启动容器。 

Compose 通过一个配置文件来管理多个Docker容器，在配置文件中，所有的容器通过services来定义，然后使用docker-compose脚本来启动，停止和重启应用，和应用中的服务以及所有依赖服务的容器，非常适合组合使用多个容器进行开发的场景。

docker-compose默认的模板文件是 docker-compose.yml，其中定义的每个服务都必须通过 image 指令指定镜像或 build 指令（需要 Dockerfile）来自动构建。












映射端口的标签。

使用HOST:CONTAINER格式或者只是指定容器的端口，宿主机会随机映射端口。

ports:
 - "3000"
 - "8000:8000"
 - "49100:22"





# 参考

* [Docker(四)----Docker-Compose 详解](https://blog.csdn.net/u011781521/article/details/80464826)
* [Docker-compose常用命令](https://www.cnblogs.com/moxiaoan/p/9299404.html)