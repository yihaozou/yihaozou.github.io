---
author: 邹政华
comments: true
date: 2014-11-09 02:04:39+00:00
layout: post
published: false
slug: '%e4%b8%80%e6%ae%b5%e8%b5%8f%e5%bf%83%e6%82%a6%e7%9b%ae%e7%9a%84%e4%bb%a3%e7%a0%81'
title: 一段赏心悦目的代码
wordpress_id: 569
categorie: 编程
---

首先羞涩地承认这段代码是我自己写的，用来实现pow（x,n)。代码中值得注意的地方有两点：

一、判断n为负数时，将n取反然后递归。但是这个时候最小整数和最大整数的绝对值之差为1，需要单独考虑；

二、考虑到效率问题，将n想像成二进制的形式如11010，那么pow(x,n)可以表示成x^16*x^8*x^2。每次x右移位一位，判断末位是否为1，来决定是否乘以相应的算子。因为C／C++中移位运算更快，所以程序运行表现也会更好一些。_
_

    
    <em id="__mceDel"><em id="__mceDel"> double pow(double x, int n) {
         double result = 1.0;
         if(n == 0)
             return 1;
         if(n < 0)
         {
              if(n == INT_MIN)
                 return 1/(pow(x,INT_MAX)*x)；
             return 1/(pow(x,-n))；
         }
         for(; n>0;x*=x, n >>= 1)
         {
             if(n & 1 > 0)
             result *= x;
         ｝
         return result;
     }
    </em></em>
