---
author: yihaozou
comments: true
date: 2013-07-21 17:09:33+00:00
layout: post
slug: '%e7%bb%a7%e6%89%bf%e5%92%8c%e4%bd%bf%e7%94%a8%e5%a3%b0%e6%98%8e'
title: 继承和使用声明
wordpress_id: 144
categories: 编程
---

（摘自Bjarne Stroustrup的《C++程序设计语言》p349)

在C++多重继承中，重载解析的使用不会跨越不同类的作用域。来自不同基类的函数之间的歧义性不能根据参数类型完成解析。

如果在不同基类中使用同样名字是一种精心策划的设计决策，或者用户希望能够基于实际参数类型做出选择，我们可以使用声明（using declaration)将这些函数引进一个公共的作用域中。举个例子。

    
    class A {
    public:
          int f(int);
          char f(char);
          //...
    };
    
    class B{
    public:
          double f(double);
          //...
    };
    
    class AB:public A,public B{
    public:
          using A::f;
          using B::f;
         char f(char)  //屏蔽A::f(char);
    };
    
    //驱动程序
    void g(AB& ab)
    {
          ab.f(1);     //A::f(int)
          ab.f('a');    //AB::f(char)
          ab.f(2.0);    //B::f(double)
          ab.f(ab);    //AB::f(AB)
    }


使用声明(using declaration)使程序员可以组合起一组来自不同基类的和来自派生类的重载函数。在派生类里声明的函数就会遮蔽在基类里那些原本可用的函数，来自基类的虚函数还是像以往一样可以覆盖。

在一个类定义里的使用声明所应用的必须是基类的成员，不能通过使用声明取用这个类、派生它的类以及他们的成员之外的某个类的成员。使用指示(using directive)不能出现在类定义里，也不能对一个类使用。

使用声明也不能用于获取对于更多信息的访问权，它只是一种使可访问信息能够以更方便的方式使用的机制。
