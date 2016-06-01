---
author: 邹政华
comments: true
date: 2016-05-07
published: true 
tags: java
layout: post
title: Java 9发布在即, Oracle着手清理Unsafe类 
categories: 行业 
---

 ( [原文](http://www.infoq.com/cn/news/2016/05/29)和英文原文[Oracle's OpenJDK Cleanup of "Unsafe" Implementation](https://www.infoq.com/news/2016/02/29)发布于infoq中文站.)

java 9正式版预计在2017年2季度发布，目前大部分JEP已经基本成型。其中，最关键特性或许是[JEP 261](http://openjdk.java.net/jeps/261), 该JEP实现了java平台的模块系统, 具体说明可以参见[JSR376](http://openjdk.java.net/projects/jigsaw/spec/)。 模块系统依赖于[JEP260](http://openjdk.java.net/jeps/260)（封装了大部分内部API），导致的结果是[JEP193](http://openjdk.java.net/jeps/193)定义的多个句柄会暴露sun.misc.Unsafe类的功能。此前Info[报道](http://www.infoq.com/news/2015/07/oracle-plan-remove-unsafe)过致力于解决sun.misc.Unsafe句柄问题的团队，可能的解决方案细节亦可参见[另一篇报道](http://www.infoq.com/articles/A-Post-Apocalyptic-sun.misc.Unsafe-World)。 


[Bug 8149159](https://bugs.openjdk.java.net/browse/JDK-8149159)最近被提交到JDK Bug管理系统, 建议优化和清理Unsafe类, 包括将参数检查从本地代码移入Java（简化JIT）、 sun.misc.Unsafe类和jdk.internal.misc.Unsafe类的统一、 以及本地代码的整体清理。


2月18日，Oracle工程师Mikael Vidstedt向OpenJDK开发者社区提交了两个补丁（分别针对OpenJDK和OpenJDK HotSpot VM）  


关于这两个补丁，Vidstedt总结道：

* 避免代码重复，sun.misc.Unsafe将全部实现委托给jdk.internal.misc.Unsafe，这意味着java虚拟机(特别是unsafe.cpp）不再需要关心s.m.Unsafe的实现。 
* s.m.Unsafe的委托方法通常会被内联，但是为了避免性能下降的风险，仍然添加了@ForceInline注解 
* 更新文档，指明用户应该确保Unsafe类的参数正确 
* 参数检查从Unsage.cpp移入java，简化本地代码以及允许JIT进一步优化
* 放松了特定参数的检查，比方说最近引入的U.copySwapMemory没有检查空指针。具体原因可以参考j.i.m.U.checkPointer的文档。除了U.copySwapMemory，现在Unsafe类方法也都没有对参数执行NULL检查 
* 在U.copySwapMemory类的基础上，对j.i.m.U.copyMemory增加了一个测试案例。请随时提醒我合并过来（本该如此）  

在Vidstedt看来，Usage类的清理算是“相当激进”了，值得注意的地方有：  

* Unsafe_方法以及unsafe.cpp中的其他本地方法被申明为静态方法  

* 新增unsafe.hpp代码文件，文件中移入VM其他组件的一些方法。移除部分“extern”函数声明（不要过度使用extern）  
* 对于不怎么用到的UNSAFE_LEAF，移除警告性质的注释（没有必要，只是个VM_LEAF） 
* 一些简单的leaf方法使用UNSAFE_LEAF  
* UNSAFE_ENTRY/UNSAFE_END代码块新增大括号，帮助自动缩进
* 移除未使用的Unsafe_<...>##140形式的函数和宏 
* 更新宏参数，与unsafe.cpp的宏定义保持一致
* 更换带断言的参数检查，正如前面提及，这些检查移入了j.i.m.Unsafe，移除所有s.m.Unsafe相关的代码

查看英文原文：[Oracle's OpenJDK Cleanup of "Unsafe" Implementation](https://www.infoq.com/news/2016/02/29)