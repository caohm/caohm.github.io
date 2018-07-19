---
title: redis install
layout: post
date: 2017-03-23 22:35:24 +0800
categories: redis
tags: redis
---


* content
{:toc}
记录redis 使用命令与redis存储设计








使用自行部署维护的原生纯内存版redis用来测试比我们维护的测试环境jimdb实例更灵活，支持在本机进行测试，可控，可避免公用集群key发送冲突，方便排查问题。

客户端需要升级到开鉴权的版本

使用方法及操作步骤如下：

第一步、使用以下配置(jimurl)：

注意：使用新测试环境一定要更换为下面的jimurl，不然cfs无法识别

<bean id="jimClient" class="com.jim.cli.ReloadableJimClientFactoryBean">
<property name="jimUrl" value="jim://2581598003362223711/80000008" />
</bean>



以下两个集群，任选一个即可。

jim://2581598003362223711/80000008 （一个实例）

jim://2573677068023585415/80000017 （两个实例）



第二步、在客户端所在的主机上配置hosts。

<自己安装redis实例所在的IP，切忌不要填写192.168.171.31> redis.local，安装好实例之后需要设置实例密码，config set requirepass 2581598003362223711，开启鉴权需要

192.168.171.31 cfs.jim.jd.local

例如，在本机部署了redis实例，就需要在客户端所在主机的hosts里添加

127.0.0.1 redis.local

192.168.171.31 cfs.jim.jd.local

如果在测试环境192.168.100.100上部署了redis实例，就需要在客户端所在主机的hosts里添加

192.168.100.100 redis.local (选80000008集群配置)

192.168.100.100 redis1.local (选80000017集群配置)

192.168.100.100 redis2.local (选80000017集群配置)

192.168.171.31 cfs.jim.jd.local



第三步、部署redis实例（这一步如果存在困难或疑问，可自行百度或google解决），自己部署实例端口一定要跟我们集群上的对应：

(redis.local:6379，redis1.local:6395，redis2.local:6402)

下载jimdb-4.0.12.tar.gz，并将该包放到/export/servers下并进行tar -xzvf jimdb-4.0.12.tar.gz解压。
解压完之后进行解压目录中，把redis.conf的配置文件中的${port}改为6379
新建几个目录，mkdir -p /var/log/jimdb; mkdir -p /opt/jimdb/data
启动redis实例，到解压目录的src下：
nohup redis-server ../redis.conf &
启动之后使用ip和port连上去，redis-cli -h ip -p port 设置密码
config set requirepass 2581598003362223711


注意 代码中加上 configClient 这个bean

<bean id="configClient" class="com.jim.cli.config.client.ConfigLongPollingClientFactoryBean">

<property name="serviceEndpoint" value="http://cfs.jim.local"></property>

</bean>
