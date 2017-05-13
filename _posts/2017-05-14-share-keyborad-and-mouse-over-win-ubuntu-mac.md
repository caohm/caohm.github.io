---
title: share keyboard and mouse over windows|ubuntu|mac
layout: post
date: 2017-05-14 01:17:00 +0800
categories: []
tags: []
---

* content
{:toc}                                                                                                          


share keyboard and mouse over windows|ubuntu|mac








### synergy

server
Server -> Configure interactively -> Configure Server -> add one screen -> insert one computer name -> save -> save -> apply -> start
client 
client -> insert server IP -> apply -> start

#### ubuntu

##### deb

##### compile

###### need software

sudo apt-get install cmake make g++ xorg-dev libqt4-dev libcurl4-openssl-dev libavahi-compat-libdnssd-dev libssl-dev libx11-dev

###### build

``` bash
QT_SELECT=4 ./hm.sh conf -g1 # -g1 for cmake
./hm.sh build [-d] # -d for debug
./hm.sh package deb
sudo dpkg -i ./bin/synergy-master-stable-ec56ac4-Linux-x86_64.deb
./bin/synergy &
```


#### windows

##### msinstaller

- http://xiazai.xiazaiba.com/Soft/S/synergy_x64_1.7.3_XiaZaiBa.zip?pcid=37957&filename=synergy_x64_1.7.3_XiaZaiBa.zip&downloadtype=xiazaiba_seo

##### compile

###### need software

- Visual Studio 2010
- Qt 5.6 https://mirrors.tuna.tsinghua.edu.cn/qt/archive/qt/5.6/5.6.2/qt-opensource-windows-x86-mingw492-5.6.2.exe
- CMake 2.8 https://cmake.org/files/v3.8/cmake-3.8.1.zip
- Python 2.x https://www.python.org/ftp/python/2.7.13/python-2.7.13.msi
- Wix v3.8 https://cmake.org/files/v3.8/cmake-3.8.1.zip
- Bonjour SDK for Windows http://adcdownload.apple.com/Developer_Tools/bonjour_sdk_for_windows_v3.0/bonjoursdksetup.exe
- Command line git in your PATH. http://dlsw.baidu.com/sw-search-sp/soft/4e/30195/Git-2.7.2-32-bit_setup.1457942412.exe

### refrence

- https://github.com/symless/synergy/wiki/Compiling#Windows_x86






























