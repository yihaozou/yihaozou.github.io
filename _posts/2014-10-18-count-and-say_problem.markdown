---
author: 邹政华
comments: true
date: 2014-10-18 12:16:34+00:00
layout: post
published: false 
slug: count-and-say%e9%97%ae%e9%a2%98
title: count and say问题
wordpress_id: 555
categories: 编程
---

问题描述：count-and-say序列指的是这样一个序列：1，11，21，1211，111221，312211，12112221，其中1读为1个1，所以第二个数是11；11读为2个1，所以第三个数为21；21读为1个2，1个1，所以第四个数为1211.以此类推。现在要求解该序列的第n个数？（可用字符串表示）

问题分析：该序列中的每个数都必须由前一个数得出，因此要求第n个数，还是要从第一个数开始，迭代n－1次，逐个求出该序列的前n个值。因为由当前值求下一个值的方法都是一致的，因此问题简化为由当前值求下一个值的方法，这个可以通过遍历字符串完成。实现方法见https://github.com/yihaozou/algo/blob/master/count_and_say.c


