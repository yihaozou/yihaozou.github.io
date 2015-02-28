---
author: yihaozou
comments: true
date: 2014-10-18 16:05:50+00:00
layout: post
published: false 
slug: reverse-integer%e9%97%ae%e9%a2%98
title: reverse integer问题
wordpress_id: 558
categories: 编程
---

问题描述：给定整数，求该整数的各个位逆序后得到的整数，如由123得到321，-123得到-321.这里要考虑到逆序后是否越界，以及原整数末尾的0逆序后不应该出现在逆序后得到的整数首位等。

思路分析：把重新排序后的各个位乘以对应的位阶在把它们的和加起来即可。如果123变为321后，321的值可由式子（3*100+2*10+1）得到，而在转换以前计算式子应该是（3+2*10+1*100）。代码实现托管在https://github.com/yihaozou/algo/blob/master/reverse_integer.cc


