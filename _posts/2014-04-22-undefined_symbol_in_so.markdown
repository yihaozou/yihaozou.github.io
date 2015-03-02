---
author: 邹政华
comments: true
date: 2014-04-22 13:08:41+00:00
layout: post
slug: '%e5%8a%a8%e6%80%81%e9%93%be%e6%8e%a5%e5%ba%93%e4%b8%ad%e7%9a%84%e6%9c%aa%e5%ae%9a%e4%b9%89%e7%ac%a6%e5%8f%b7'
title: 动态链接库中的未定义符号
wordpress_id: 356
categories: 编程
---

由于一时疏忽，将c语言源码中的标准库函数strcmp写成了strcpm，然后使用xlc编译器编译成动态链接库so文件。在这个过程中没有报错。后来使用dlopen打开动态链接库so文件时，出现dlerr错误，具体为“Symbol strcpm was referenced from module apcomp.so, but a runtime definition was not found"。将源代码中的strcpm改成strcmp后重新编译，动态链接库文件正常工作。

究其原因，是编译器在编译动态链接库时，并不检查符号的依赖项，也就是说如果如果引用了某符号却没有链接其对应的库，编译器仍然会编译通过。

但是在加载动态链接库时会因为出现未定义符号而失败。编译器在生成可执行文件时，会严格检查符号是否都有定义。（还有一个与主题无关的问题，如果链接了一个未使用的库，其中的符号也会全部链接过来）
