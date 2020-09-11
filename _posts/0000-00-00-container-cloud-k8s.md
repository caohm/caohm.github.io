---
title: kubernetes(k8s)
layout: post
date: 2019-03-26 16:48:57 +0800
categories: 容器云
tags: docker k8s
---

* content
{:toc}
本文 记录kubernetes






## k8s之Headless Service
1. Headless Service
see: [Headless services](https://kubernetes.io/docs/concepts/services-networking/service/#headless-services)

headless service 需要将 spec.clusterIP 设置成 None。

因为没有ClusterIP，kube-proxy 并不处理此类服务，因为没有load balancing或 proxy 代理设置，在访问服务的时候回返回后端的全部的Pods IP地址，主要用于开发者自己根据pods进行负载均衡器的开发(设置了selector)。


## 更换证书
1） 修改系统时间 <8月>
date -s ""

2) 确认错误重现
kubectl get nodes

3) 备份/etc/kubernetes目录
cp -r /etc/kubernetes /root/0301

4) 续期证书
kubeadm alpha certs renew all --config=/root/kubeadm-config.yaml

5）删除/etc/kubernetes中的所有conf文件
rm /etc/kubernetes/*.conf

6)重新生成配置文件
kubeadm init phase kubeconfig all --config=/root/kubeadm-config.yaml

7）重启docker及kubelet
service restart kubelet
service restart docker

8) 拷贝/etc/kubernetes/admin.conf到/root/.kube/config
/root/.kube/config可以先备份
mv /root/.kube/config /root/.kube/config.0301
cp /etc/kubernetes/admin.conf /root/.kube/config



# 参考文档
----
 *[k8s 中文社区](https://www.kubernetes.org.cn/k8s)<br/>
*[图解 Kubernetes 架构](https://www.hi-linux.com/posts/48037.html)
*[kubeadm源码分析（内含kubernetes离线包，三步安装）](https://www.kubernetes.org.cn/4015.html)
