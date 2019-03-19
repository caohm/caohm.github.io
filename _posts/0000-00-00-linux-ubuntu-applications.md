---
layout: post
title: Ubuntu下快捷方式的创建（添加应用程序到applications列表）
date: 2017-04-23 20:11:59 +0800
categories: linux
tags: linux Ubuntu
---

* content
{:toc}







## applicatios

在linux中，由于地位的特殊性，在安装应用程序的时候是区别与windows桌面操作系统的。
在作为服务器的时候往往不会启用桌面，因此想要在applications列表中查找应用程序的需求自然不复存在，但是在个人开发或者调试的时候大多使用的是linux桌面版本。
除了通过linux的软件包管理器安装的应用程序，凡是通过源码编译安装、解压二进制等形式安装的软件将无法在applications列表中被查找。
事实上这种情况我们往往是创建软连接到/usr/bin目录中。
接下来我们要简单的实现将我们自定义安装的应用程序添加到applications列表中。

### 创建快捷方式

1. 在`/usr/share/applications/`目录创建`application.desktop`文件

2. 编辑

```bash
[Desktop Entry]
Name=IntelliJ IDEA
Exec=/develop/application/bin/application.sh
Comment=IntelliJ IDEA
Icon=/develop/application/bin/application.png
Type=Application
Terminal=false
Encoding=UTF-8
```

3. 执行权限

`sudo chmod +x /usr/share/applications/application.desktop`

### application.desktop配置说明

* [Desktop Entry] 必填，表明该文件是一个Desktop Entry文件，节点意思十分明显，桌面入口。Desktop Entry实际上是一个文件标准，该标准由FreeDesktop.org制定；

1. Comment 这是应用程序的描述，通常会在搜索应用程序的界面看到这个描述（鼠标放到应用程序图标上时）；

1. Comment[zh_CN] 同样的作用，表明使用中文。同理，[]里面可以书写多国语言；

1. Exec 必填，应用程序启动的指令，可以带上参数运行，实际上这与在终端的启动指令是一样的；

1. GenericName 应用程序通用名称，他会显示在搜索结果以及非搜索结果中；

1. MimeType 可选，熟悉web开发的人员应当十分熟悉，这是表明映射；

1. Name 必填，该值定义了应用程序的名称，这个设置同理可以使用Name[en]等字样，在不同的语言环境下面，会使用对应的Name值；

1. StartupNotify 可选，该值是bool值（[true | false]），这个设置只有在Type类型是application的时候才有意义；

1. Terminal 可选，该值也是bool值（[true | false]） ，并且只有在Type为application时有意义，该值指出了相关应用程序(Exec值)是否需要在终端中运行；

1. Type 必填，这是一个枚举值，它定义了desktop文件的类型。常见的数值是Application和Link。Application表示当前文件指向一个应用程序，而Link表明当前文件指向一个URL，即链接文件；

1. Categories 可选，只有在Type类型是Application时才有意义。Categories的值表明了相关应用程序在菜单中显示的类别，关于类别的定义参见Desktop Specification Menu的具体定义，如图：


#### Refrence

>* [在linux中添加应用程序到applications列表](https://blog.csdn.net/huahuajjh/article/details/63263485)
