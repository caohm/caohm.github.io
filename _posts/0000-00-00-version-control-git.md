---
title: version control git
layout: post
date:  2017-04-27 15:11:49 +0800 
categories: 版本控制
tags: git
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

1. 从已有的分支创建新的分支(如从master分支),创建一个dev分支

```
git checkout -b dev
```

2. 创建完可以查看一下,分支已经切换到dev

```
git branch
    * dev
    master
git checkout master    
```
3. 提交该分支到远程仓库

```
git push origin dev
```

4. 测试从远程获取dev

```
git pull origin dev
```

5. fork 代码跟新原代码(远程代码跟新)

```
$ git remote -v
origin  git@github.com:caohm/dubbo.git (fetch)
origin  git@github.com:caohm/dubbo.git (push)
$ git remote add upstream https://github.com/apache/incubator-dubbo.git
$ git remote -v
origin  git@github.com:caohm/dubbo.git (fetch)
origin  git@github.com:caohm/dubbo.git (push)
upstream        https://github.com/apache/incubator-dubbo.git (fetch)
upstream        https://github.com/apache/incubator-dubbo.git (push)
$ git checkout master
$ git pull upstream master

```

6. git比较两个分支的文件的差异


```bash
git diff branch1 branch2 --stat  //显示出所有有差异的文件列表

git diff branch1 branch2 文件     //显示指定文件的详细差异

git diff branch1 branch2         //显示出所有有差异的文件的详细差异

```



