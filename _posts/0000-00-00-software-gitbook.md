---
title: gitbook
layout: post
date: 2017-11-16 15:18:09 +0800
categories: [gitbook]
tags: [gitbook]
---


* content
{:toc}                                                                                                          











### gitbook
gitbook是一个用于发布书籍的平台。

gitbook提供了一个简单的命令行工具gitbook用来编译和预览的书籍.


#### 其中有如下特点，
用git进行版本管理和发布工具，可以托管在github上进行多人协助；
以Markdown轻量级的标记语法进行编写的基础；
用nodejs进行构建部署（gitbook），并且可以发布到gitbook官网上；
可以快速转制各种格式流通的电子书格式：PDF, ePub, mobi（Amazon专属格式），或者也可以生成一个线上阅读网站；
### 安装

1.安nodejs环境，请到nodejs官网进行下载安装
2.
```
~$ sudo apt install nodejs-legacy
~$ node -v
v4.2.6
~$ sudo npm install -g gitbook-cli
~$ gitbook -V
CLI version: 2.3.2
GitBook version: 3.2.3

```

从github上克隆源码git clone https://github.com/looly/elasticsearch-definitive-guide-cn.git
进入源码目录，cd elasticsearch-definitive-guide-cn
生成mobi格式 gitbook pdf gitbook mobi 生成mobi格式，此格式为Kindle专属格式
复制mobi格式的文件到kindle，OK！搞定！enjoy it！
