---
layout: post
title: tensorflow 环境安装
date:   2019-03-19 20:36:11 +08:00
category: 机器学习
tags: 人工智能 AI 机器学习 深度学习 tensorflow
comments: true
---

* content
{:toc}

install tensorflow


### install anaconda 

#### download

[anaconda](https://www.anaconda.com/distribution/) or [anaconda](https://repo.continuum.io/archive/index.html)

#### install
```bash
caohm@caohm-ThinkPad-E450:~$ bash Anaconda3-2018.12-Linux-x86_64.sh
caohm@caohm-ThinkPad-E450:~$ vi .bashrc
add
export PATH=/home/caohm/anaconda3/bin:$PATH
:wq
caohm@caohm-ThinkPad-E450:~$ source ~/.bashrc
caohm@caohm-ThinkPad-E450:~$ conda --version
conda 4.5.12
caohm@caohm-ThinkPad-E450:~$ conda list
caohm@caohm-ThinkPad-E450:~$ conda install * 
caohm@caohm-ThinkPad-E450:~$ conda update * 
caohm@caohm-ThinkPad-E450:~/anaconda3$ python
Python 3.7.1 (default, Dec 14 2018, 19:28:38) 
[GCC 7.3.0] :: Anaconda, Inc. on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import scipy
>>> 
```

#### update 

```bash
caohm@caohm-ThinkPad-E450:~$ bash Anaconda3-2018.12-Linux-x86_64.sh -u
```

#### remove
```bash
caohm@caohm-ThinkPad-E450:~$ rm -rf ~/anaconda3
```

### install tensorflow 

建立专门的运行环境再安装
可以专门建立一个tensorflow的运行环境，好处是：1.官网建议 2.不与其他文件混乱


1. 创建专门运行环境
为Anaconda创建一个python3.6（或者3.5都行，随便）的环境，环境名称为tensorflow ，输入下面命令：
```bash
caohm@caohm-ThinkPad-E450:~$ conda create -n tensorflow python=3.6 
caohm@caohm-ThinkPad-E450:~$ conda info --envs
caohm@caohm-ThinkPad-E450:~$ python --version
```
2. 启动tensorflow的环境：
```bash
caohm@caohm-ThinkPad-E450:~$ conda activate tensorflow
```
注：当不使用tensorflow时，关闭tensorflow环境，命令为：
```bash
(tensorflow) caohm@caohm-ThinkPad-E450:~$ conda deactivate
```
3. 安装cpu版本的TensorFlow
```bash
(tensorflow) caohm@caohm-ThinkPad-E450:~$ pip install --upgrade --ignore-installed tensorflow 
```
4. 测试是否安装完成
```bash
(tensorflow) caohm@caohm-ThinkPad-E450:~$ python
>>> import tensorflow as tf
>>> tf.enable_eager_execution()
>>> tf.add(1, 2).numpy()
3
>>> hello = tf.constant('Hello, TensorFlow!')
>>> hello.numpy()
'Hello, TensorFlow!'
```


## 参考

> * [ubuntu的安装+spyder安装+tensorflow安装](https://blog.csdn.net/xiao_xian_/article/details/82592580)
----



