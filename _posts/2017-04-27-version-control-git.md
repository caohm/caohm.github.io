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
```