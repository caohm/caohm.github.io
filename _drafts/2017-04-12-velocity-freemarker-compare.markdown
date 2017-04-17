---
title: velocity-freemarker-compare
layout: post
date: 2017-04-12 11:47:54 +0800
categories: [compare]
tags: [Velocity,FreeMarker]
---


* content
{:toc}                                                                                                          











原文：http://lishumingwm163-com.iteye.com/blog/933365

相比较 FreeMarker 而言，Velocity 更加简单、轻量级，但它的功能却没有 FreeMarker 那么强大。 

对于大部分的应用来说，使用 FreeMarker 比 Velocity 更简单，因为 Velocity 还必须编写一些自定义的toolbox类以及一遍遍重复的编写一些比较通用的模版代码，因此也就丧失了刚开始开发时更多的宝贵时间。另外使用工具类和变通的方法在模版引擎中似乎不是一个非常有效的做法。同时，Velocity 的做法使得在Velocity的模版中大量的跟 Java 对象进行交互，这违反了简单的原则，尽管你也可以将代码转入控制器中实现。当然，如果你像使用 Velocity 一样来使用 FreeMarker ，那么 FreeMarker 也可以跟 Velocity 一样简单。 

Velocity 一个优于 FreeMarker 的地方在于它有很广泛的第三方支持以及一个非常庞大的用户社区，你可以通过这个社区获得到很多的帮助，相反的 FreeMarker 在这方面要差很多。当然，也有越来越多的第三方软件开始在支持 FreeMarker 。 

下面是一些 FreeMarker 能做到的，而 Velocity 做不到的功能列表（且看着）： 

1. 日期和数字的支持 
您可以执行运算和比较，对任意数量的类型，包括任意精度类型，而不仅仅是整数。 
您可以比较和显示（格式化）日期/时间值。 

2. 国际化 
您可以格式数字区域，各种各样的内置和自定义数字格式模式。 
您可以格式日期地区和时区，各种各样的内置和定制的日期格式模式。 
标识符（变量名）可以包含非英语字母一样重音字母，阿拉伯字母，汉字等 

3. 循环处理 
您可以退出循环 
您可以访问控制变量外循环机构的内部循环 
您可以得知当前是否到了循环的结束位置 

4. 模版级别的数组处理 
您可以使用[i]的语法来访问数组元素，包括原始的和非原始的指数 
可以获取到数组的长度 

5. 宏定义 
宏调用可以通过位置或名称进行参数传递 
宏的参数可以设定默认值，在调用宏时如果没有指定该参数，则使用默认值代替 
通过 <@myMacro>body</@myMacro> 可以支持宏的嵌套 
可以通过文本表达的“宏的名称”来直接调用某个宏 
宏允许先使用再定义 
宏可以定义局部变量（新版本的Velocity也通过#local指令来实现该功能，尽管官方的文档还没有进行介绍） 

6. 命名空间 
您可以使用多个名称空间的变数。当您建立“宏库”时是非常有用的 ，因为可以防止名称冲突与申请特定变量或与其他宏变量的库。 

7. 内置与 Java 语言无关的字符串、列表、Map 的操作方法 

8. 能提示模版中的拼写错误以及其他错误 
当访问一个不存在的变量时，FreeMarker 在执行该模版时会报错，通过配置，你可以指定 FreeMarker 在碰到此类错误时是停止执行，还是忽略该错误，同时 FreeMarker 会在日志中记录此问题； 
如果您输入错误指令的名称，FreeMarker将抛出一个异常。 

9. 更高级的文本输出工具 
You can enclose a block of template in a set of tags that will cause it to apply HTML escaping or XML escaping (or any other transformation you can express as a FreeMarker expression for that matter) on all interpolations (${foo}) in the block. 
FreeMarker has transforms, which are blocks of template that when rendered, Go through a transforming filter. Built-in transforms include whitespace compressor, HTML and XML escaper. Best of all, you can implement your own transformers as well (i.e. if you generate Java source code, you can write a Java code pretty-printer transform and insert it into the template). Naturally, transforms can be nested. 
You can explicitly flush the output writer with a built-in flush-directive. 
You can stop the rendering with a built-in stop-directive. 

10. 文本处理 
支持Java的特殊字符处理，例如\b, \t, \n, \f, \r, \", \', \\，以及UNICODE的\xXXXX 
除了通常的字符串，数字，和布尔常量您可以定义列表和地图文字以及内部模板 

11. 高级的空格清除 
FreeMarker 将删除一些多余的空格、跳格、换行等字符，从而消除一些令人厌烦的明显多余的空格 
FreeMarker 也提供指令来删除多于的空格 

12. 与其他技术的集成 
提供 JSP 标签库以便在 JSP 中嵌入 FreeMarker 模版 
可以直接跟 Python 对象一起工作 

13. 更强大的XML转换功能 

14. 先进的模板元程序 
您可以捕捉到输出的任意部分范本背景变量 
您可以任意解释的范围变量，就好像它是一个模板定义 


velocity是较早出现的用于代替jsp的模板语言 
优点： 
1、不能编写java代码，可以实现严格的mvc分离 
2、性能良好，据说比jsp性能还要好些 
3、使用表达式语言，据说jsp的表达式语言就是学velocity的 
缺点： 
1、不是官方标准 
2、用户群体和第三方标签库没有jsp多。 
3、对jsp标签支持不够好 

freemarker 
优点： 
1、不能编写java代码，可以实现严格的mvc分离 
2、性能非常不错 
3、对jsp标签支持良好 
4、内置大量常用功能，使用非常方便 
5、宏定义（类似jsp标签）非常方便 
6、使用表达式语言 
缺点： 
1、不是官方标准 
2、用户群体和第三方标签库没有jsp多 

选择freemarker的原因： 
1、性能。velocity应该是最好的，其次是jsp，普通的页面freemarker性能最差（虽然只是几毫秒到十几毫秒的差距）。但是在复杂页面上（包含大量判断、日期金额格式化）的页面上，freemarker的性能比使用tag和el的jsp好。 
2、宏定义比jsp tag方便 
3、内置大量常用功能。比如html过滤，日期金额格式化等等，使用非常方便 
4、支持jsp标签 
5、可以实现严格的mvc分离 


FreeMarker的优点和缺点 

一、 FreeMarker简介 
FreeMarker是一个用Java语言编写的模板引擎，它基于模板来生成文本输出。 
所谓模板，就是一份已经写好了基本内容，有着固定格式的文档，其中空出或者用占位符标识的内容，由使用者来填充，不同的使用者给出的数据是不同的。在模板中的占位符，在模板运行时，由模板引擎来解析模板，并采用动态数据替换占位符部分的内容。 
FreeMarker与Web容器无关，即在Web运行时，它并不知道Servlet或HTTP。它不仅可以用作表现层的实现技术，而且还可以用于生成XML，JSP或Java 文等。 


二、 FreeMarker的优点和缺点 
(一) FreeMarker的优点 
1.使用FreeMarker的优点之一，可以彻底的分离表现层和业务逻辑。 
使用JSP 开发过程中在页面中大量的存在业务逻辑的代码，使得页面内容凌乱，在后期大量的修改维护过程中就变得非常困难。 
FreeMarker根本不支持Java脚本代码。 
FreeMarker的原理就是：模板+数据模型=输出，模板只负责数据在页面中的表现，不涉及任何的逻辑代码，而所有的逻辑都是由数据模型来处理的。用户最终看到的输出是模板和数据模型合并后创建的。 
      
2.使用FreeMarker的优点之二，可以提高开发效率。 
在我们以往的开发中，使用的都是JSP 页面来展示数据的，即所谓的表现层。我们都知道，JSP在第一次执行的时候需要转换成Servlet类，开发阶段进行功能调适时，需要频繁的修改JSP，每次修改都要编译和转换，那么试想一天中我们浪费在程序编译的时间有多少。相对于JSP来说，FreeMarker模板技术不存在编译和转换的问题，所以就不会存在上述问题。而且开发过程中，我们在不必在等待界面设计开发人员完成页面原形后，我们再来开发程序。

3.使用FreeMarker的优点之三，使得开发过程中的人员分工更加明确。 
以往用JSP展现数据时，作为程序员的我们，并不熟悉界面设计技术，反之界面开发人员，也并不熟悉程序语言。对两者而言，交替性的工作本身就有难度。有时候稍有不慎，可能会将某个页面元素删除或去掉了某个程序符号，使得页面走样或程序错误，这样就需要双方相互沟通协作，解决出现的问题。有时候因为项目中的时间，任务量等因素的存在，可能这个工作就由一个人来完成，这样就可能加大某一方开发人员的工作量。 
使用FreeMarker后，作为界面开发人员，只专心创建HTML文件、图像以及Web页面的其他可视化方面，不用理会数据；而程序开发人员则专注于系统实现，负责为页面准备要显示的数据。 


(二)FreeMarker的缺点 
1.使用FreeMarker的缺点之一，应用FreeMarker模板技术，在修改模板后，可能会看到已经过期的数据。如：生成静态的HTML页面后，如果一旦模板改变，而没有及时更新模板生成的HTML页面的话，用户看到的就是过期的数据。 
2.使用FreeMarker的缺点之二，FreeMarker模板技术在应用过程中，FreeMarker中的变量必须要赋值，如果不赋值，那么就会抛出异常。想避免错误就要应用if/elseif/else 指令进行判段，如果对每一个变量都判断的话，那么则反而增加了编程的麻烦。 
3.使用FreeMarker的缺点之三，FreeMarker的map限定key必须是string，其他数据类型无法操作 
4.使用FreeMarker的缺点之四，FreeMarker不支持集群应用。为了编成的方便性，把序列化的东西都放到了Session中，如Session，request等，在开发的过程中确实方便，但如果将应用放到集群中，就会出现错误。