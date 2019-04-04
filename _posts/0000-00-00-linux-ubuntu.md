---
layout: post
title: Ubuntu
date: 2017-04-23 20:11:59 +0800
categories: linux
tags: linux Ubuntu
---

* content
{:toc}
本文介绍 ubuntu系统如何使用及常用命令








### 常用命令

```bash
sudo apt-get clean && sudo apt-get autoremove
sudo apt-get -f install
sudo dpkg --configure -a
#最佳卸载命令
apt-get remove packagename --purge && apt-get autoremove --purge && apt-get clean 
#卸载程序(包括配置文件)、卸载依赖、删除/var/cache/apt/archives下所有安装包
#最佳升级软件命令
apt-get update && apt-get upgrade
```




### 安装中文输入法

#### Steps

##### install chinese language

##### Ubuntu 15 later choice of `fcitx` in the "Keyboard input method system" menu. 

##### `im-config`  to select fcitx

##### Reference

- [Ubuntu Chinese Setup](http://pinyinjoe.com/linux/ubuntu-12-chinese-setup.htm)
- [Chinese Input Methods in Ubuntu 10 - 16](http://pinyinjoe.com/linux/ubuntu-10-chinese-input-pinyin-chewing.htm)
- [Chinese Fonts and Features in Ubuntu 10 - 16](http://pinyinjoe.com/linux/ubuntu-10-chinese-fonts-openoffice-language-features.htm)

### 安装字体

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