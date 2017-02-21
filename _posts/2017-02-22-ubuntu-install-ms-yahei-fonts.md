---
title: ubuntu install fonts
layout: post
date:  2017-02-11 11:47:21 +0800 
categories: [Install]
tags: [Ubuntu,font]
---

* content
{:toc}

本文介绍 Ubuntu安装中文字体。














#### Commond install
``` bash
# 文泉驿微黑、文鼎楷体、上海宋字体库
sudo apt-get install ttf-wqy-zenhei ttf-wqy-microhei fonts-arphic-ukai fonts-arphic-uming
```

#### Manual install 
``` bash
wget http://www.mycode.net.cn/wp-content/uploads/2015/07/YaHeiConsolas.tar.gz
#解压压缩包
tar -zxvf YaHeiConsolas.tar.gz
#在系统目录下创建自定义字体目录
sudo mkdir -p /usr/share/fonts/vista
#复制解压出来的字体到刚才创建的目录
sudo cp YaHeiConsolas.ttf /usr/share/fonts/vista/
#修改字体权限
sudo chmod 644 /usr/share/fonts/vista/*.ttf
#进入字体目录
cd /usr/share/fonts/vista/
#刷新并安装字体
sudo mkfontscale && sudo mkfontdir && sudo fc-cache -fv
```


#### Download "unity-tweak-tool" To Set Fonts
``` bash
apt-get install unity-tweak-tool 
```

#### Edit language Conf
``` bash
sudo vi /etc/fonts/conf.d/69-language-selector-zh-cn.conf 
```

#### Refrence
- [ubuntu安装微软雅黑和Consolas字体](http://www.linuxdiyf.com/linux/21351.html)
- [Debian中文字体安装](http://edyfox.codecarver.org/html/debian_testing_chinese.html)