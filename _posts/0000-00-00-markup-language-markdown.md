---
layout: post
title: 标记语言markdown 
date:   2018-06-06 20:36:11 +08:00
category: 标记语言
tags: markdown 
comments: true
---

* content
{:toc}




# Markdown基本语法：

1：标题(`#` h1-h6)

2：空格、段落和换行(`<p><br>`)

3：超链接(`[]()` `<a>`)

4：图片(`![]()` `<img>`)和公式

5：列表(`<ul><ol><li>`)

6：代码块(``` `<pre>`)

7：着重、强调(`**` `<em><strong>`)

8：表格(`-|-|-` `<table>`)

9：转义字符(\\)

10：分隔线(`---` `<hr>`)




## 图片

### 行内式图片链接

行内式的图片链接语法如下：
   
```
![Alt text](/path/to/img.jpg)

![Alt text](/path/to/img.jpg "Optional title")
``` 

实例：

```
![CSDN图标](http://imgtech.gmw.cn/attachement/jpg/site2/20111223/f04da22d7ba7105e1d7507.jpg "这是CSDN的图标")
```

效果为：

![CSDN图标](http://imgtech.gmw.cn/attachement/jpg/site2/20111223/f04da22d7ba7105e1d7507.jpg "这是CSDN的图标")
      
   
### 参考式图片链接

参考式的图片链接语法则如下面这样：

```
![Alt text][id]

[id]: url/to/image  "Optional title"
```

例子：

还是以上面那个图片URL为例，改写成参考式链接如下：
   
```
![CSDN图标][csdn]
[csdn]: http://imgtech.gmw.cn/attachement/jpg/site2/20111223/f04da22d7ba7105e1d7507.jpg "这是CSDN的图标"
```

效果：

![CSDN图标][csdn]

[csdn]: http://imgtech.gmw.cn/attachement/jpg/site2/20111223/f04da22d7ba7105e1d7507.jpg "这是CSDN的图标"

### jekyll框架下的post图片显示

```
![图片pic1]({{ "/assets/images4post/pic1.jpg" | absolute_url }})
```









----
## 参考链接
>* [标记语言Markdown介绍以及日常使用](https://blog.csdn.net/xun527/article/details/78821095)

>* [CSDN-markdown语法之如何插入图片](https://blog.csdn.net/lanxuezaipiao/article/details/44310775)

----

