---
layout: post
title:  "install jekyll"
date:   2017-02-07 22:22:08 +0800
categories: [Install]
tags: [jekyll]
---

* content
{:toc}


本文介绍静态个人博客系统jekyll,如何安装jekyll






## Windows

### 安装

powershell 3.0 内部安装Chocolatey需要先安装powershell 3.0

- 下载  [Windows6.1-KB2506143-x64.msu](https://www.microsoft.com/en-us/download/confirmation.aspx?id=34595) 安装powershell 3.0
- 通过powershell 3.0 安装 Chocolatey
- 通过Chocolatey安装ruby
- 通过ruby的gem安装bundler

``` shell
C:\>gem install --local C:\rubygems-update-2.6.7.gem
C:\>update_rubygems --no-ri --no-rdoc
```
### 启动

```
C:\blogs>jekyll s --drafts
```

### refrence

> - [SSL CERTIFICATE UPDATES](http://guides.rubygems.org/ssl-certificate-update)



## Ubuntu
### 1 Needed software 

|需要安装的软件 |执行的命令 |
|---|---|
|ruby	|sudo apt-get install ruby|
|ruby-dev	|sudo apt-get install ruby-dev|
|jekyll	|sudo gem install jekyll|
|bundler	|sudo gem install bundler|
|nodejs	|sudo apt-get install nodejs|
|对于其他gems	|可以尝试bundle update更新gems包|

git、Python等都是ubuntu自带的，不用再安装了。

PS： Ruby 的gem类似于Python的pip。gem程序是基于ruby的组件或者库。

### 2 Show version

``` shell
caohm@caohm-ThinkPad-E450:~$ ruby -v
ruby 2.3.1p112 (2016-04-26) [x86_64-linux-gnu]
caohm@caohm-ThinkPad-E450:~$ gem -v
2.5.1
caohm@caohm-ThinkPad-E450:~$ nodejs -v
v4.2.6
caohm@caohm-ThinkPad-E450:~$ jekyll -v
jekyll 3.4.0
caohm@caohm-ThinkPad-E450:~$ python --version
Python 2.7.12
caohm@caohm-ThinkPad-E450:~$ git version
git version 2.7.4

```

### 3 Run jekyll project

#### Clone git project

``` shell
caohm@caohm-ThinkPad-E450:~$ git clone git@github.com:caohm/caohm.github.io.git
```

#### Update dependency 

``` shell
caohm@caohm-ThinkPad-E450:~$ bundle update
```

#### Start run your project

``` shell
 caohm@caohm-ThinkPad-E450:~$ bundle exec jekyll serve
```

### Refrence

> - [Gemfile 依赖包](http://blog.csdn.net/alada007/article/details/8035316)
> - [Gemfile 详解](http://www.w2bc.com/Article/66273)
