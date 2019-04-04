---
title: kubernetes(k8s) 单机部署
layout: post
date: 2019-03-26 16:48:57 +0800
categories: 容器云
tags: docker k8s
---

* content
{:toc}
本文 记录kubernetes






# 软件安装

sudo apt-get install etcd

sudo -s

apt-get update && apt-get install -y apt-transport-https

curl https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | apt-key add -

cat <<EOF >/etc/apt/sources.list.d/kubernetes.list

deb [arch=amd64] https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main

EOF  

apt-get update

apt-get install -y kubelet kubeadm kubectl










# 参考文档
----
 *[单机部署k8s + docker](https://blog.csdn.net/nc_hzh302/article/details/79747150)
 *[K8S入门(二) 单机部署](https://www.jianshu.com/p/06c9e01f7379)
 *[（kubernetes）k8s入门、单机版安装、kuberctl指令、k8s服务实例](https://blog.csdn.net/qq_34701586/article/details/78732470)
 *[Kubernetes国内镜像、下载安装包和拉取gcr.io镜像](https://blog.csdn.net/nklinsirui/article/details/80581286#debian-ubuntu)


