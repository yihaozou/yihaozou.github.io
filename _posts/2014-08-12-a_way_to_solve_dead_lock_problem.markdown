---
author: yihaozou
comments: true
date: 2014-08-12 08:53:44+00:00
layout: post
slug: '%e4%b8%80%e7%a7%8d%e8%bf%9b%e7%a8%8b%e6%ad%bb%e9%94%81%e8%a7%a3%e5%86%b3%e6%96%b9%e6%a1%88'
title: 一种进程死锁场景及解决方案
wordpress_id: 496
categories: 编程
---

在网络服务器使用进程池模式来响应客户请求时，多个进程在监听同一描述字时调用了accept。但是system V内核以库函数形式实现accept函数，并非原子操作，如果我们仍然用多个进程同时accpt套接字描述符的话，某个子进程的accept就会返回EPROTO错误，表示协议错。解决问题的方法是在应用进程调用accpt前后设置某种形式的锁（lock），这样每次只有一个子进程阻塞在accpt调用，其他进程则阻塞在试图获取锁权限上。

我们在设计ARE进程管理框架时同样使用了进程池模式，也使用了信号量方式用来保证accept操作的原子性。但是实际运行以后经常会出现无法响应客户请求的情况。经排查后发现，问题出现在信号处理函数上。

    
    ppm_sig_handler_install(SIGHUP, worker_suicide);
    ppm_sig_handler_install(SIGTERM, worker_suicide);


worker_suicide为信号处理函数，接收到SIGHUP和SIGTERM信号后，终止子进程。但是进程终止后却没有释放信号量。于是其他的子进程一直阻塞在等待获取信号量上。

解决方案非常简单，在信号处理函数worker_suicide中添加锁释放代码。

    
    struct sembuf sop;
    sop.sem_op=1;
    sop.semflg=0;
    semop(mutex_id, &sop, 1);
