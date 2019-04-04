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




















### 安装

#### 安装 anaconda 

1. 下载 

    [官方下载地址](https://www.anaconda.com/distribution/)<br/>
    [镜像下载地址](https://repo.continuum.io/archive/index.html)
2. 安装 
    ``` bash
    caohm@caohm-ThinkPad-E450:~$ bash Anaconda3-2018.12-Linux-x86_64.sh
    ```
3. 加入环境变量
    ```bash
    caohm@caohm-ThinkPad-E450:~$ vi .bashrc
    #添加
    export PATH=/home/caohm/anaconda3/bin:$PATH
    ```
4. 生效
    ```bash
    caohm@caohm-ThinkPad-E450:~$ source ~/.bashrc
    ```
5. 检测
    ```bash
    caohm@caohm-ThinkPad-E450:~$ conda --version
    conda 4.5.12
    #查看软件列表
    caohm@caohm-ThinkPad-E450:~$ conda list
    #安装软件
    caohm@caohm-ThinkPad-E450:~$ conda install * 
    #更新软件
    caohm@caohm-ThinkPad-E450:~$ conda update * 
    ```
3. 升级 
    ```bash
    caohm@caohm-ThinkPad-E450:~$ bash Anaconda3-2018.12-Linux-x86_64.sh -u
    ```
4. 卸载
    ```bash
    caohm@caohm-ThinkPad-E450:~$ rm -rf ~/anaconda3
    ```

#### 安装 tensorflow 

建立专门的运行环境再安装
可以专门建立一个tensorflow的运行环境，好处是：1.官网建议 2.不与其他文件混乱


1. 创建专门运行环境

    为Anaconda创建一个python3.6（或者3.5都行，随便）的环境，环境名称为tensorflow ，输入下面命令：
    ```bash
    caohm@caohm-ThinkPad-E450:~$ conda create -n tensorflow python=3.6 

    caohm@caohm-ThinkPad-E450:~$ conda info --envs
    #查看python版本
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
### Hello World








## 参考

>* [ubuntu的安装+spyder安装+tensorflow安装](https://blog.csdn.net/xiao_xian_/article/details/82592580)

----

