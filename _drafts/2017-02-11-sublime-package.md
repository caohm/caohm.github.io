---
title: Sublime Text 3 Install Packages
layout: post
date: 2017-02-12 01:08:12 +0800 
categories: [Install]
tags: [Ubuntu]
---


* content
{:toc}
本文介绍 Sublime 安装插件














#### 1 安装Package Controal

打开Sublime Console输入代码自动安装

``` python 
import urllib.request,os; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); open(os.path.join(ipp, pf), 'wb').write(urllib.request.urlopen( 'http://sublime.wbond.net/' + pf.replace(' ','%20')).read())
```
或下载[Package Control.sublime-package](https://sublime.wbond.net/Package%20Control.sublime-package)并复制到Installed Packages/目录,重启Sublime。

#### 2 安装常用插件

|插件        |          简介|
|---                    ---|
|ConvertToUTF8　　　　     |支持多种编码，解决中文乱码问题。|
|Bracket Highlighter　　|      用于高亮匹配括号、引号、html标签。|
|DocBlockr　　　　　　　    |可以自动生成PHPDoc风格的注释。|
|Emmet 　　　　　　　　 　| 快速编写HTML，原 Zen Coding。|
|SideBar Enhancements　　|这个插件改进了侧边栏，增加了许多功能|
|evernote　　　　　　　　|　这个是 evernote 的插件，支持 markdown 语法|
|markdown preview　　　　|markdown 预览插件|
|Vintage  |vim模式|
|sublime-jekyll   |jekyll| 

#### 3 启用 Vim 模式

在菜单栏中： Preferences -> Setting - User ，清空ignored_packages ["Vintage"]：开启vim模式

``` json
{
    "ignored_packages": [],
    "vintage_ctrl_keys": true
}
```

#### Refrence

- [Sublime Text 3 插件安装及Vim 模式设置](http://www.cnblogs.com/zuike/p/4402022.html)
- [Vintage模式](http://feliving.github.io/Sublime-Text-3-Documentation/vintage.html)