---
title: redis-safe
layout: post
date: 2017-03-23 22:35:24 +0800
categories: redis
tags: redis
---


* content
{:toc}
Redis 安全配置






### 一、概述
Redis 是完全开源免费的，遵守BSD协议，是一个高性能的key-value数据库。

Redis 默认情况下，会绑定在 0.0.0.0:6379，这样将会将 Redis 服务暴露到公网上，如果在没有开启认证的情况下，可以导致任意用户在可以访问目标服务器的情况下未授权访问 Redis 以及读取 Redis 的数据。攻击者在未授权访问 Redis 的情况下可以利用 Redis 的相关方法，可以成功在 Redis 服务器上写入公钥，进而可以使用对应私钥直接登录目标服务器。

本文主要描述Redis 的安全加固和配置工作，增加系统安全性。架构师和系统运维工程师应该通读本文档，选择适用于自己的配置。

### 二、技术要求
#### 2.1 账号与认证（必须）：

设置访问密码，在 redis.conf 中找到 requirepass 字段，去掉其注释，并在后面填上需要的密码（Redis 客户端也需要使用此密码来访问 Redis 服务）

例如打开 /etc/redis/redis.conf 配置文件:

requirepass xxx (xxx为要改的密码)

确保密码的复杂度满足账号密码的长度、强度和定期更新的要求（大小写+数字+字母+特殊字符+长度8位以上），配置完毕后重启服务即可生效。



#### 2.2 服务运行权限最小化（必须）

修改 Redis 服务运行账号，强烈建议不要使用root帐号运行redis，一旦redis被入侵可能攻击者会获取root权限

请以较低权限账号运行 Redis 服务，并禁用该账号的登录权限。以下操作创建了一个无 home 目录权限，且无法登录的普通账号：

useradd -M -s /sbin/nologin [username]

注意：该操作需要重启 Redis 才能生效。



#### 2.3 服务精细化授权（非必须）

隐藏重要命令

Redis 无权限分离，其管理员账号和普通账号无明显区分。攻击者登录后可执行任意操作，因此需要隐藏以下重要命令：FLUSHDB, FLUSHALL, KEYS,PEXPIRE, DEL, CONFIG, SHUTDOWN, BGREWRITEAOF, BGSAVE, SAVE, SPOP, SREM, RENAME,DEBUG, EVAL。

另外，在 Redis 2.8.1 及 Redis 3.x （低于 3.0.2） 版本下存在 EVAL 沙箱逃逸漏洞，攻击者可通过该漏洞执行任意 Lua 代码。

下述配置将 config/flushdb/flushall 设置为空，即禁用该命令；也可设置为一些复杂的、难以猜测的名字。

rename-command CONFIG ""
rename-command flushall ""
rename-command flushdb ""
rename-command shutdown shotdown_test
保存后，执行 /etc/init.d/redis-server restart 重启生效。



#### 2.4 如无业务需求，禁止对公网开放访问权限

添加或修改被允许访问的IP，如果不需要直接对外提供服务，bind 127.0.0.1就行了，禁止bind 0.0.0.0！

bind 127.0.0.1

#### 2.5 如无业务需求，禁止使用默认端口设置

 redis的默认端口6379，如无业务需求，禁止使用默认端口，能够在一定程度上被扫描。
