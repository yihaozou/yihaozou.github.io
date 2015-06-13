---
author: 邹政华
comments: true
date: 2013-04-28 04:28:47+00:00
layout: post
published: false
slug: lamp%e9%85%8d%e7%bd%ae
title: LAMP配置（ubuntu 11.10）
wordpress_id: 86
categories: 编程
---

**apache2安装及配置**：

    
  sudo apt-get install apache2;


测试是否安装成功：浏览器中输入"http://127.0.0.1"或"http://localhost/"后页面显示“It works!"

**php5安装配置**

    
sudo apt-get install php5;


测试是否安装成功：新建测试文件"/var/www/testphp.php",在测试文件里加入

    
     <html>
     <head>
     <title>world</title>
     </head>
     <body>
     <?php echo "hello,world!"?>
     </body>
     </html


然后在浏览器中输入"127.0.0.1/testphp.php"如果显示的页面内容为"hello,world"。说明php安装成功。

**mysql安装配置**

	sudo apt-get install mysql-server; 。



 #注：local host 指的是主机号；"123"是用户laohua的密码。
  
	select,insert,delete,update on *.*  to laohua @localhost Identified by "123" ;

    sudo apt-get install libapache2-mod-auth-mysql;  
	sudo apt-get install php5-mysql;   
	sudo /etc.init.d/apache2 restart;
