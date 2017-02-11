---
title: ubuntu install MS-YaHei fontfs
layout: post
date:  2017-02-11 11:47:21 +0800 
categories: [Ubuntu]
tags: [fontfs]
---


* content
{:toc}
本文 记录 

####【字体下载】
YaHeiConsolas.tar：http://www.mycode.net.cn/wp-content/uploads/2015/07/YaHeiConsolas.tar.gz

####【字体安装】
``` bash
//解压压缩包
tar -zxvf YaHeiConsolas.tar.gz
//在系统目录下创建自定义字体目录
sudo mkdir -p /usr/share/fonts/vista
//复制解压出来的字体到刚才创建的目录
sudo cp YaHeiConsolas.ttf /usr/share/fonts/vista/
//修改字体权限
sudo chmod 644 /usr/share/fonts/vista/*.ttf
//进入字体目录
cd /usr/share/fonts/vista/
//刷新并安装字体
sudo mkfontscale && sudo mkfontdir && sudo fc-cache -fv
```


install Unity Tweak Tool to set fonfts



[ubuntu安装微软雅黑和Consolas字体](http://www.linuxdiyf.com/linux/21351.html)
