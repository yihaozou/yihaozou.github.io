---
author: 邹政华
comments: true
date: 2013-01-13 23:48:34+00:00
layout: post
published: false
slug: '%e7%94%a8ipv6%e8%ae%bf%e9%97%aegoogle-sites'
title: 以ipv6方式访问google sites
wordpress_id: 55
categories: 编程
---

事情的起因是这样的：论文做验证实验时，需要用到Saikat Chatterjee的google站点https://sites.google.com/site/saikatchatt/softwares上面的源代码。

解决步骤：

1）首先确定网络支持ipv6：访问http://ipv6.google.com或者www.kame.net(ipv6访问乌龟是动态的）

2）获得google的ipv6地址。可以通过ping命令得到：ping www.google.com.得到google的ipv6地址为2404:6800:4005:c00::67.如果ping不通的话，可以上网查询google的ipv6地址。

3）修改hosts文件，该文件在win7下的系统路径为C:\Windows\System32\drivers\etc。在文件末尾添加

    
    #google
    
    2404:6800:4005:c00::67 sites.google.com


修改时用户权限不够系统会拒绝用户的修改行为。这时可以修改hosts文件的权限设置,具体方法为：右键点击文件》属性》安全》高级》更改权限。

4）刷新dns缓存，命令行下运行：ipconfig /flushdns
