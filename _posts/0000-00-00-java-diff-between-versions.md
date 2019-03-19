---
layout: post
title: jdk各版本之间的区别 
date:   2018-06-06 20:36:11 +08:00
category: java
tags: jdk
comments: true
---

* content
{:toc}



##### JDK1.5新特性：

1. 自动装箱与拆箱：
2. 枚举
3. 静态导入，如：`import staticjava.lang.System.out`
4. 可变参数（Varargs）
5. 内省（Introspector），主要用于操作JavaBean中的属性，通过getXxx/setXxx。一般的做法是通过类Introspector来获取某个对象的BeanInfo信息，然后通过BeanInfo来获取属性的描述器（PropertyDescriptor），通过这个属性描述器就可以获取某个属性对应的getter/setter方法，然后我们就可以通过反射机制来调用这些方法。
6. 泛型(Generic)（包括通配类型/边界类型等）
7. For-Each循环8.注解9.协变返回类型：实际返回类型可以是要求的返回类型的一个子类型

##### JDK1.6新特性：

1. AWT新增加了两个类:Desktop和SystemTray，其中前者用来通过系统默认程序来执行一个操作，如使用默认浏览器浏览指定的URL,用默认邮件客户端给指定的邮箱发邮件,用默认应用程序打开或编辑文件(比如,用记事本打开以txt为后缀名的文件),用系统默认的打印机打印文档等。后者可以用来在系统托盘区创建一个托盘程序
2. 使用JAXB2来实现对象与XML之间的映射，可以将一个Java对象转变成为XML格式，反之亦然
3. StAX，一种利用拉模式解析(pull-parsing)XML文档的API。类似于SAX，也基于事件驱动模型。之所以将StAX加入到JAXP家族，是因为JDK6中的JAXB2和JAX-WS 2.0中都会用StAX。
4. 使用Compiler API，动态编译Java源文件，如JSP编译引擎就是动态的，所以修改后无需重启服务器。
5. 轻量级Http Server API，据此可以构建自己的嵌入式HttpServer,它支持Http和Https协议。
6. 插入式注解处理API(PluggableAnnotation Processing API)
7. 提供了Console类用以开发控制台程序，位于java.io包中。据此可方便与Windows下的cmd或Linux下的Terminal等交互。
8. 对脚本语言的支持如: ruby,groovy, javascript9.Common Annotations，原是J2EE 5.0规范的一部分，现在把它的一部分放到了J2SE 6.0中10.嵌入式数据库 Derby

##### JDK1.7 新特性

1. 对Java集合（Collections）的增强支持，可直接采用[]、{}的形式存入对象，采用[]的形式按照索引、键值来获取集合中的对象。如：
```
List<String>list=[“item1”,”item2”];//存
Stringitem=list[0];//直接取         
Set<String>set={“item1”,”item2”,”item3”};//存
Map<String,Integer> map={“key1”:1,”key2”:2};//存
Intvalue=map[“key1”];//取
```

2. 在Switch中可用String
3. 数值可加下划线用作分隔符（编译时自动被忽略）
4. 支持二进制数字，如：`int binary= 0b1001_1001;`
5. 简化了可变参数方法的调用
6. 调用泛型类的构造方法时，可以省去泛型参数，编译器会自动判断。
7. Boolean类型反转，空指针安全,参与位运算
8. char类型的equals方法: `booleanCharacter.equalsIgnoreCase(char ch1, char ch2)`
9. 安全的加减乘除:` Math.safeToInt(longv); Math.safeNegate(int v); Math.safeSubtract(long v1, int v2);Math.safeMultiply(int v1, int v2)……`
10. Map集合支持并发请求，注HashTable是线程安全的，Map是非线程安全的。但此处更新使得其也支持并发。另外，Map对象可这样定义：`Map map = {name:"xxx",age:18};`

##### JDK1.8新特性

1. 接口的默认方法：即接口中可以声明一个非抽象的方法做为默认的实现，但只能声明一个，且在方法的返回类型前要加上“default”关键字。
2. Lambda 表达式：是对匿名比较器的简化，如：
```
         Collections.sort(names,(String a, String b) -> {       returnb.compareTo(a);});
```
对于函数体只有一行代码的，你可以去掉大括号{}以及return关键字。如：
`Collections.sort(names,(String a, String b) -> b.compareTo(a));`
或：`Collections.sort(names, (a, b) -> b.compareTo(a));`
3. 函数式接口：是指仅仅只包含一个抽象方法的接口，要加@FunctionalInterface注解
4. 使用 :: 关键字来传递方法或者构造函数引用
5. 多重注解
6. 还增加了很多与函数式接口类似的接口以及与Map相关的API等……

##### jdk1.9新特性

1. Java 平台级模块系统当启动一个模块化应用时， JVM 会验证是否所有的模块都能使用，这基于 `requires` 语句——比脆弱的类路径迈进了一大步。模块允许你更好地强制结构化封装你的应用并明确依赖。
2. Linking&nbsp;当你使用具有显式依赖关系的模块和模块化的 JDK 时，新的可能性出现了。你的应用程序模块现在将声明其对其他应用程序模块的依赖以及对其所使用的 JDK 模块的依赖。为什么不使用这些信息创建一个最小的运行时环境，其中只包含运行应用程序所需的那些模块呢？ 这可以通过 Java 9 中的新的 jlink 工具实现。你可以创建针对应用程序进行优化的最小运行时映像而不需要使用完全加载 JDK 安装版本。
3. JShell : 交互式 Java REPL许多语言已经具有交互式编程环境，Java 现在加入了这个俱乐部。您可以从控制台启动 jshell ，并直接启动输入和执行 Java 代码。 jshell 的即时反馈使它成为探索 API 和尝试语言特性的好工具。
4. 改进的 JavadocJavadoc 现在支持在 API 文档中的进行搜索。另外，Javadoc 的输出现在符合兼容 HTML5 标准。此外，你会注意到，每个 Javadoc 页面都包含有关 JDK 模块类或接口来源的信息。
5. 集合工厂方法通常，您希望在代码中创建一个集合（例如，List 或 Set ），并直接用一些元素填充它。 实例化集合，几个 “add” 调用，使得代码重复。 Java 9，添加了几种集合工厂方法：
```
Set<Integer> ints = Set.of(1,2,3);
List<String> strings = List.of("first","second");
```
除了更短和更好阅读之外，这些方法也可以避免您选择特定的集合实现。 事实上，从工厂方法返回已放入数个元素的集合实现是高度优化的。这是可能的，因为它们是不可变的：在创建后，继续添加元素到这些集合会导致 “UnsupportedOperationException” 。
6. 改进的 Stream API长期以来，Stream API 都是 Java 标准库最好的改进之一。通过这套 API 可以在集合上建立用于转换的申明管道。在 Java 9 中它会变得更好。Stream 接口中添加了 4 个新的方法：dropWhile, takeWhile, ofNullable。还有个 iterate 方法的新重载方法，可以让你提供一个 Predicate (判断条件)来指定什么时候结束迭代：`IntStream.iterate(1, i -> i < 100, i -> i + 1).forEach(System.out::println);`
第二个参数是一个 Lambda，它会在当前 IntStream 中的元素到达 100 的时候返回 true。因此这个简单的示例是向控制台打印 1 到 99。除了对 Stream 本身的扩展，Optional 和 Stream 之间的结合也得到了改进。现在可以通过 Optional 的新方法 `stram` 将一个 Optional 对象转换为一个(可能是空的) Stream 对象：
`Stream<Integer> s = Optional.of(1).stream();`
在组合复杂的 Stream 管道时，将 Optional 转换为 Stream 非常有用。
7. 私有接口方法使用 Java 9，您可以向接口添加私有辅助方法来解决此问题：
```
publicinterface MyInterface {
 
    voidnormalInterfaceMethod();
 
    defaultvoid interfaceMethodWithDefault() {  init(); }
 
    defaultvoid anotherDefaultMethod() { init(); }
 
    // This method is not part of the public API exposed by MyInterface
    privatevoid init() { System.out.println("Initializing"); }
}
```
如果您使用默认方法开发 API ，那么私有接口方法可能有助于构建其实现。
8. HTTP/2Java 9 中有新的方式来处理 HTTP 调用。这个迟到的特性用于代替老旧的 `HttpURLConnection` API，并提供对 WebSocket 和 HTTP/2 的支持。注意：新的 HttpClient API 在 Java 9 中以所谓的孵化器模块交付。也就是说，这套 API 不能保证 100% 完成。不过你可以在 Java 9 中开始使用这套 API：
```
HttpClient client = HttpClient.newHttpClient();
 
HttpRequest req =
   HttpRequest.newBuilder(URI.create("http://www.google.com"))
              .header("User-Agent","Java")
              .GET()
              .build();
 
 
HttpResponse<String> resp = client.send(req, HttpResponse.BodyHandler.asString());
```
除了这个简单的请求/响应模型之外，HttpClient 还提供了新的 API 来处理 HTTP/2 的特性，比如流和服务端推送。9. 多版本兼容 JAR
我们最后要来着重介绍的这个特性对于库的维护者而言是个特别好的消息。当一个新版本的 Java 出现的时候，你的库用户要花费数年时间才会切换到这个新的版本。这就意味着库得去向后兼容你想要支持的最老的 Java 版本 (许多情况下就是 Java 6 或者 7)。这实际上意味着未来的很长一段时间，你都不能在库中运用 Java 9 所提供的新特性。幸运的是，多版本兼容 JAR 功能能让你创建仅在特定版本的 Java 环境中运行库程序时选择使用的 class 版本：

```
multirelease.jar
├── META-INF
│   └── versions
│       └── 9
│           └── multirelease
│               └── Helper.class
├── multirelease
    ├── Helper.class
    └── Main.class
```

在上述场景中， multirelease.jar 可以在 Java 9 中使用, 不过 Helper 这个类使用的不是顶层的 multirelease.Helper 这个 class, 而是处在“META-INF/versions/9”下面的这个。这是特别为 Java 9 准备的 class 版本，可以运用 Java 9 所提供的特性和库。同时，在早期的 Java 诸版本中使用这个 JAR 也是能运行的，因为较老版本的 Java 只会看到顶层的这个 Helper 类。

##### JDK 10 的主要功能包括：

* 一个局部变量类型推断，通过增强语言特性将类型推断扩展到局部变量，目的是减少与编码相关的“仪式”，同时保持对静态类型的安全承诺。
* 一个干净的垃圾收集器接口，用来改善垃圾收集器源代码之间的隔离效果，这样可以为HotSpot 虚拟机中的内部垃圾收集代码提供更好的模块化功能，也可以更容易向 HotSpot 添加新的垃圾收集器。
* 并行、完整的 G1 垃圾收集器，通过实现并行性来改善最坏情况下的延迟问题。
* 启用 HotSpot 将对象堆分配给用户指定的备用内存设备（如 NVDIMM 内存模块），这个特性也侧面预示了未来的系统可能会采用异构的内存架构。
* 在 Linux / x64 平台上以实验性方式启用基于 Java 的即时编译器（https://www.infoworld.com/article/3187868/application-development/oracles-java-on-java-experiment-picks-up-steam.html）。
* 将 JDK 的多个存储库合并成一个，简化开发。目前的代码库被分解成了多个库，容易出现源代码的管理问题。
* 应用程序数据共享，通过跨进程共享通用类的元数据，减少空间占用及启动时长。
* 线程本地握手，不执行全局 VM 安全点也能对线程执行回调，同时实现单线程停止回调。
* JDK 提供了一组默认证书，开源 Java SE 的 CA程序，对开发人员更具吸引力。




