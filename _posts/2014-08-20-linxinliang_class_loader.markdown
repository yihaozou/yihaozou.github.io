---
author: 邹政华
comments: true
date: 2014-8-20
published: true 
tags: Java
layout: post
title:  林信良Java学习笔记－类加载器
categories: 读书 
---

类加载器的实际职责就是载入.class文档, JDK本身有默认的类加载器, 也可以建立自己的类加载器加入现有的加载器层级。

在文本模式下执行“java ***”指令后, java执行程序会尝试寻找JRE安装目录, 然后寻找JVM, 接着启动JVM并进行初始化动作, 然后产生Bootstrap Loader, Bootstrap Loader会产生Extended Loader, 并将Extended Loader的父加载器设为Bootstrap Loader, 接着Bootstrap Loader会产生System Loader, 并将System Loader的父加载器设为Extended Loader。


Bootstrap Loader通常由C编写; Extended Loader通常由Java编写, 如果是Oracle/Sun JDK, 实际上是对应于sun.misc.Lancher.ExtClassLoader; System Loader是由Java编写, 是加上对应于sun.misc.Launcher.AppClassLoader。


- Bootstrap类加载器-JRE/lib/rt.jar, Bootstrap类加载器负责加载rt.jar中的JDK类文件，它是所有类加载器的父加载器。Bootstrap类加载器没有任何父类加载器, 如果你调用String.class.getClassLoader(), 会返回null, 任何基于此的代码会抛出NUllPointerException异常。Bootstrap加载器被称为初始类加载器。

- Extension类加载器-JRE/lib/ext或者java.ext.dirs指向的目录, Extension将加载类的请求先委托给它的父加载器，也就是Bootstrap, 如果没有成功加载的话，再从jre/lib/ext目录下或者java.ext.dirs系统属性定义的目录下加载类。Extension加载器由sun.misc.Launcher$ExtClassLoader实现。

- Application类加载器-CLASSPATH环境变量, 由-classpath或-cp选项定义, 或者是JAR中的Manifest的classpath属性定义.System类加载器(又叫作Application类加载器)了.它负责从classpath环境变量中加载某些应用相关的类, classpath环境变量通常由-classpath或-cp命令行选项来定义, 或者是JAR中的Manifest的classpath属性. Application类加载器是Extension类加载器的子加载器. 通过sun.misc.Launcher$AppClassLoader实现.

ClassLoader可以使用loadClass()方法加载类, 使用loadClass()方法加载类时, 默认不会执行静态区块, 真正使用类建立实例时才会执行静态区块。

Java真正需要某个类时才会加载对应的.class文档，而非在程序启动就加载所有类. Bootstrap Loader,Extended Loader,System Loader在程序启动后，就无法再改变它们的搜索路径，如果在程序运行过程中，打算动态决定从其他路径加载类，就要产生新的类加载器，新的类加载器建立后，父加载器建立后，父加载器会设为System Loader.

