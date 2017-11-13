---
title: version control git
layout: post
date:  2017-04-27 15:11:49 +0800 
categories: [版本控制]
tags: [git]
---


* content
{:toc}


本文介绍版本控制-Git以及Git使用是常见问题










### install

### commands

### using

```
:~$ git clone git@github.com:github/project.git 
:~$ cd prject
:~$ git branch dev
:~$ git checkout dev
:~$ vi README.md
:~$ git status
:~$ git add README.md
:~$ git status
:~$ git commit -m "add readme"
:~$ git push --set-upstream origin dev
:~$ git checkout master
:~$ git rest --hard
:~$ git merge dev
:~$ 

git branch -d <branchname>
 git push origin :<branchname>

```

1,从已有的分支创建新的分支(如从master分支),创建一个dev分支
git checkout -b dev

2,创建完可以查看一下,分支已经切换到dev
git branch
    * dev
    master
3,提交该分支到远程仓库
git push origin dev
4,测试从远程获取dev
git pull origin dev

