---
title: openwrt raspberry cluster
layout: post
date: 2017-02-18 15:21:32 +0800 
categories: RaspberryPi
tags: RaspberryPi OpenWrt 集群
---


* content
{:toc}


本文介绍 openwrt内网搭建树莓派集群。















### OpenWrt

- 二级路由OpenWrt(RG100A-AA)
- OpenWrt版本： Backfire 10.03.1-RC6
- WWan:192.168.1.200
- 密码：root/admin
- ngrok

### RaspberryPi

- IP1:192.168.1.201
- IP2:192.168.1.202
- IP3:192.168.1.203
- 密码：pi/raspberry


### openwrt技术汇总

#### openwrt 技术网站
- [openwrt](http://www.openwrt.org.cn)
- [OpenWrt FAQ](https://dev.openwrt.org.cn/wiki/faqs)





#### 编译
- [OpenWrt编译教程](http://www.openwrt.org.cn/bbs/viewthread.php?tid=17&amp;extra=page%3D1)
- [完全新手教程：openwrt编译全过程(sse)](http://www.openwrt.org.cn/bbs/viewthread.php?tid=4217)
- [直接编译出带中文的openwrt 固件 教程，适用luci 0.10下(by zjhzzyf)](http://www.openwrt.org.cn/bbs/viewthread.php?tid=4170&amp;extra=page%3D1)

#### 刷机教程
- [OpenWrt刷机教程--免拆机免TTL线](http://www.openwrt.org.cn/bbs/viewthread.php?tid=56&amp;extra=page%3D1)
- [OpenWrt TTL线刷机教程](http://www.openwrt.org.cn/bbs/forum.php?mod=viewthread&amp;tid=7953&amp;page=1&amp;extra=#pid59453)
- [[xhh2001]DB120-WG刷OPENWRT成功了(无TTL)](http://www.openwrt.org.cn/bbs/viewthread.php?tid=152&amp;extra=page%3D1)
- [DLINK2650BU刷OpenWrt 教程](http://www.openwrt.org.cn/bbs/viewthread.php?tid=147&amp;extra=page%3D2)
- [RG100A刷机+上网配置+BT设置一体化教程(作者easycx，简单明了，推荐)](http://www.openwrt.org.cn/bbs/viewthread.php?tid=386&amp;extra=page%3D1)

#### 配置教程
- [OpenWrt 快速上网配置教程](http://www.openwrt.org.cn/bbs/viewthread.php?tid=28&amp;extra=page%3D1)
- [OpenWrt 无线（wifi）设置图文教程](http://www.openwrt.org.cn/bbs/redirect.php?tid=184&amp;goto=lastpost#lastpost)
- [从外网访问和管理路由器的WEB配置方法(开启防火墙端口) ](http://www.openwrt.org.cn/bbs/viewthread.php?tid=40&amp;extra=page%3D1)
- [OpenWrt端口映射WEB配置图文教程-3步搞定](http://www.openwrt.org.cn/bbs/viewthread.php?tid=41&amp;extra=page%3D1)
- [RG100A使用OPENWRT的一点心得](http://www.openwrt.org.cn/bbs/viewthread.php?tid=31&amp;extra=page%3D1)
- [RG100A VLAN配置](http://www.openwrt.org.cn/bbs/viewthread.php?tid=37&amp;extra=page%3D1)
- [OpenWrt虚拟内存（SWAP）挂载教程](http://www.openwrt.org.cn/bbs/viewthread.php?tid=162&amp;page=1&amp;extra=#pid1301)
- [OpenWrt打印共享设置教程](http://www.openwrt.org.cn/bbs/viewthread.php?tid=199)

#### 多WAN叠加
- [ 单线多拨 宽带叠加nwan安装配置教程](http://www.openwrt.org.cn/bbs/viewthread.php?tid=2909&amp;extra=page%3D1)

#### 从优盘启动
- [从优盘启动(扩展系统到优盘)](http://www.openwrt.org.cn/bbs/viewthread.php?tid=5983&amp;extra=page%3D1)
- [利用block-extroot，让你的openwrt运行在USB设备上](http://www.openwrt.org.cn/bbs/viewthread.php?tid=268)

#### 网络共享(samba)
- [解决vista,win7不能访问samba](http://www.openwrt.org.cn/bbs/viewthread.php?tid=94&amp;extra=page%3D1)
- [openwrt下的samba设置](http://www.openwrt.org.cn/bbs/viewthread.php?tid=385&amp;extra=page%3D1)
- [[openwrt完美教程]----建立samba共享简单四步 ](http://www.openwrt.org.cn/bbs/viewthread.php?tid=4543&amp;extra=page%3D1)

#### 无线设置
- [无线客户端设置图文教程（zjpguo）](http://www.openwrt.org.cn/bbs/viewthread.php?tid=503)
- [无线客户端脱机下载(duckduck)](http://www.openwrt.org.cn/bbs/viewthread.php?tid=380&amp;extra=page%3D1)
- [0508下的WDS组建成功！(chnfn213)](http://www.openwrt.org.cn/bbs/viewthread.php?tid=480&amp;extra=page%3D1&amp;page=2)
- [openwrt wifi wep加密破解(duckduck)](http://www.openwrt.org.cn/bbs/viewthread.php?tid=472&amp;extra=page%3D1)
- [设置-8187L-客户端-万能中继-脱机-已加图](http://www.openwrt.org.cn/bbs/viewthread.php?tid=2986&amp;extra=page%3D1)
- [中国移动CMCC wifi连接方法](http://www.openwrt.org.cn/bbs/forum.php?mod=viewthread&amp;tid=7229)
- [中国电信wifi（ChinaNet）接入方法](http://www.openwrt.org.cn/bbs/forum.php?mod=viewthread&amp;tid=2943&amp;highlight=chinanet)
- [openwrt蓝牙共享实现方案](http://www.openwrt.org.cn/bbs/forum.php?mod=viewthread&amp;tid=12175&amp;extra=)

#### 3G拨号
- [RG100A backfire rc5 3G拨号设置教程](http://www.openwrt.org.cn/bbs/viewthread.php?tid=5914&amp;extra=)

#### 脱机下载
- [OpenWrt图解脱机BT(transmission-daemon)教程](http://www.openwrt.org.cn/bbs/viewthread.php?tid=51&amp;extra=page%3D1)
- [OpenWrt图解脱机电驴教程](http://www.openwrt.org.cn/bbs/viewthread.php?tid=49&amp;extra=page%3D1)
- [OpenWrt mldonkey设置教程](http://www.openwrt.org.cn/bbs/viewthread.php?tid=149&amp;extra=page%3D2)
- [RG100A-AA脱机mldonkey下载设置，有图有真相](http://www.openwrt.org.cn/bbs/viewthread.php?tid=830&amp;extra=page%3D1) 

#### 摄像头挂载
- [openwrt挂载ZC0301PL-USB摄像头教程](http://www.openwrt.org.cn/bbs/viewthread.php?tid=30&amp;extra=page%3D4)
- [openwrt免驱摄像头挂载（免驱但非301方案分享）](http://www.openwrt.org.cn/bbs/viewthread.php?tid=6105&amp;extra=)
- [OpenWrt摄像头选购](http://www.openwrt.org.cn/bbs/forum.php?mod=viewthread&amp;tid=8788&amp;extra=page%3D1)
- [OpenWrt已 成功挂载的摄像头](http://www.openwrt.org.cn/bbs/forum.php?mod=viewthread&amp;tid=3320&amp;extra=page%3D1)

#### uShare media server和客户端
- [OpenWrt uShare upnp媒体服务器应用教程](http://www.openwrt.org.cn/bbs/viewthread.php?tid=75&amp;extra=page%3D1)
- [uShare客户端XBMC简单介绍（强烈推荐XBMC）](http://www.openwrt.com.cn/bbs/redirect.php?tid=561&amp;goto=lastpost)

#### 网站与数据库
- [自动安装配置Nginx+php-cgi+sqlite脚本~](http://www.openwrt.org.cn/bbs/viewthread.php?tid=5934&amp;extra=page%3D1)
- [openwrt 下的mysql安装](http://www.piaojy.com/index.php/openwrt-%E4%B8%8B%E7%9A%84mysql%E5%AE%89%E8%A3%85.html)
- [ 路由上建立博客，包成功](http://www.openwrt.org.cn/bbs/viewthread.php?tid=4992&amp;extra=page%3D1)

#### 认证客户端
- [openwrt上使用锐捷认证共享上网方法](http://www.openwrt.org.cn/bbs/viewthread.php?tid=2612&amp;extra=page%3D1)

#### IPv6
- [openwrt下单一ipv6地址做网关（附mentohust、njitclient）](http://www.openwrt.org.cn/bbs/forum.php?mod=viewthread&amp;tid=7116)

#### VPN相关
- [N2N VPN设置方法](http://www.openwrt.org.cn/bbs/forum.php?mod=viewthread&amp;tid=3495)

#### 开发
- [openwrt交叉编译之Hello World教程](http://www.openwrt.org.cn/bbs/viewthread.php?tid=3577&amp;extra=&amp;page=1)
- [NFS设置(by tjpm)](http://www.openwrt.org.cn/bbs/forum.php?mod=viewthread&amp;tid=638)

#### 计划任务
- [openwrt crontab(计划任务)设置教程](http://www.openwrt.org.cn/bbs/forum.php?mod=viewthread&amp;tid=15)

#### 其他
- [Openwrt 使用Msmtp+mutt发邮件+139邮箱免费短信提醒](http://www.openwrt.org.cn/bbs/forum.php?mod=viewthread&amp;tid=879&amp;extra=&amp;page=1)</td>




#### Refrence

[OpenWrt系列教程汇总](http://www.openwrt.org.cn/bbs/thread-60-1-1.html)



