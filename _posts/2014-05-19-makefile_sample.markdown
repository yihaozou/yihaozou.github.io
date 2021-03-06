---
author: 邹政华
comments: true
published: false
date: 2014-05-19 06:28:11+00:00
layout: post
slug: '%e4%b8%80%e7%a7%8dmakefile%e7%bc%96%e5%86%99%e8%8c%83%e4%be%8b'
title: 一种makefile编写范例
wordpress_id: 395
categories: 编程
---

makefile的编写应该考虑到代码移植的可能性，将可能发生改变的参数放在makefile文件起始部位定义成可配置的变量，从而减少修改makefile带来的工作量。

    
    CC = gcc
    QFLAG =
    MACROS = -D_OS_LINUX_
    CCFLAGS= -m32
    INCL = ./
    C20 = $(CC)  $(QFLAG)  $(MACROS) ${CCFLAGS} -c $? -I$(INCL)
    O2E =  $(CC) ${MACROS} ${CCFLAGS} -O $@ $? -ldl


代码中的编译器定义成CC（考虑到了UNIX用户的习惯），C2O是将源文件编译成目标文件，O2E是将目标文件连接成可执行文件或库文件。使用这些定义成变量的编译参数来编译源代码，可以减少编写makefile文件时敲击键盘的次数，同时提高makefile文件的可读性和移植性。下面给出一个实际项目的编译例子。

    
    all: ppmmonitor
    ppmmonitor: ppm_monitor.o snapshot.o ppm_loex.o
                $(O2E) -lncurses
    ppm_monitor.o: ppm_socket.c ppm_log_ex.c snapshot.c ppm_monitor.c 
                $(C2O)
    #此处省略snapshot.o和ppm_log_ex.o的编译


可以看出编写出的makefile文件非常的简洁。如果要移植到AIX系统，只需要将CC定义成cc，QFLAG定义成qcpluscmt,同时注释掉MACROS即可。另外，如果代码文件较多，并且需要在不同系统间迁移，也可以使用gnu的autotools工具来打包源代码，生成安装脚本。
