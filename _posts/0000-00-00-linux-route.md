---
layout: post
title:  linux route 
date: 2017-04-23 20:11:59 +0800
categories: linux
tags: linux route
---

* content
{:toc}
本文介绍 IP Route









```bash
#查看linux IP Route 规则
caohm@caohm-ThinkPad-E450:~$ ip route
# Lan
default via 192.168.172.129 dev enp0s25  proto static  metric 100 
# Wlan
default via 100.100.100.1 dev wlp4s0  proto static  metric 600 
# rule
100.100.100.0/24 dev wlp4s0   proto kernel  scope link  src 100.100.100.119  metric 600 
169.254.0.0/16   dev enp0s25  scope link                                     metric 1000 
172.17.0.0/16    dev docker0  proto kernel  scope link  src 172.17.0.1                    linkdown 
192.168.172.0/24 dev enp0s25  proto kernel  scope link  src 192.168.172.179  metric 100 
# 删除 Lan route
caohm@caohm-ThinkPad-E450:~$ sudo ip route delete default via 192.168.172.129 dev enp0s25  proto static  metric 100 
[sudo] password for caohm: 
# 添加 route rule
caohm@caohm-ThinkPad-E450:~$ sudo ip route add 192.168.112.0/24 via 192.168.172.129 dev enp0s25 
#
caohm@caohm-ThinkPad-E450:~$ ping 192.168.112.96
PING 192.168.112.96 (192.168.112.96) 56(84) bytes of data.
64 bytes from 192.168.112.96: icmp_seq=1 ttl=63 time=0.792 ms
64 bytes from 192.168.112.96: icmp_seq=2 ttl=63 time=0.895 ms
64 bytes from 192.168.112.96: icmp_seq=3 ttl=63 time=0.519 ms
cp64 bytes from 192.168.112.96: icmp_seq=4 ttl=63 time=0.575 ms
in64 bytes from 192.168.112.96: icmp_seq=5 ttl=63 time=0.792 ms
c64 bytes from 192.168.112.96: icmp_seq=6 ttl=63 time=1.11 ms
ccc64 bytes from 192.168.112.96: icmp_seq=7 ttl=63 time=0.537 ms
c64 bytes from 192.168.112.96: icmp_seq=8 ttl=63 time=0.642 ms
^C
--- 192.168.112.96 ping statistics ---
8 packets transmitted, 8 received, 0% packet loss, time 7000ms
rtt min/avg/max/mdev = 0.519/0.733/1.119/0.196 ms
#
caohm@caohm-ThinkPad-E450:~$ ping www.baidu.com
PING www.a.shifen.com (220.181.112.244) 56(84) bytes of data.
64 bytes from 220.181.112.244: icmp_seq=4 ttl=48 time=467 ms
64 bytes from 220.181.112.244: icmp_seq=5 ttl=48 time=207 ms
64 bytes from 220.181.112.244: icmp_seq=6 ttl=48 time=223 ms
64 bytes from 220.181.112.244: icmp_seq=7 ttl=48 time=132 ms
^C
--- www.a.shifen.com ping statistics ---
7 packets transmitted, 4 received, 42% packet loss, time 6017ms
rtt min/avg/max/mdev = 132.315/257.660/467.584/125.979 ms
# 查看 IP Route
caohm@caohm-ThinkPad-E450:~$ ip route
# WLan
default via 100.100.100.1 dev wlp4s0  proto static  metric 600 

100.100.100.0/24 dev wlp4s0   proto kernel  scope link  src 100.100.100.119  metric 600 
169.254.0.0/16   dev enp0s25                scope link                       metric 1000 
172.17.0.0/16    dev docker0  proto kernel  scope link  src 172.17.0.1                    linkdown 
192.168.112.0/24 via 192.168.172.129 dev enp0s25 
192.168.172.0/24 dev enp0s25  proto kernel  scope link  src 192.168.172.179  metric 100 
caohm@caohm-ThinkPad-E450:~$ 
```

