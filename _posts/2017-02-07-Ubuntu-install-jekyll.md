---
layout: post
title:  "Ubuntu install jekyll"
date:   2017-02-07 22:22:08 +0800
categories: [Install]
tags: [Ubuntu]
---

* content
{:toc}


本文介绍Ubuntu install jekyll










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
