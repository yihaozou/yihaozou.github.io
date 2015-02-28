---
author: yihaozou
comments: true
date: 2014-05-11 04:40:10+00:00
layout: post
slug: c%e8%af%ad%e8%a8%80%e4%b8%ad%e6%95%b0%e6%8d%ae%e6%95%b0%e6%8d%ae%e7%b1%bb%e5%9e%8b%e7%9a%84%e5%ad%97%e8%8a%82%e6%95%b0
title: 信息表示与处理
wordpress_id: 382
categories: 编程
---

**C语言中数据类型的字节数**
<table cellpadding="0" width="398" cellspacing="0" border="0" > 
<tbody >
<tr >

<td width="120" height="18" >C声明
</td>

<td width="139" >32位机器（字节）
</td>

<td width="139" >64位机器（字节）
</td>
</tr>
<tr >

<td height="18" >char
</td>

<td >1
</td>

<td >1
</td>
</tr>
<tr >

<td height="18" >short int
</td>

<td >2
</td>

<td >2
</td>
</tr>
<tr >

<td height="18" >int
</td>

<td >4
</td>

<td >4
</td>
</tr>
<tr >

<td height="18" >long int
</td>

<td >4
</td>

<td >8
</td>
</tr>
<tr >

<td height="18" >long long int
</td>

<td >8
</td>

<td >8
</td>
</tr>
<tr >

<td height="18" >char *
</td>

<td >4
</td>

<td >8
</td>
</tr>
<tr >

<td height="18" >float
</td>

<td >4
</td>

<td >4
</td>
</tr>
<tr >

<td height="18" >double
</td>

<td >8
</td>

<td >8
</td>
</tr>
</tbody>
</table>
**整型和无符号整形的比较**
<table cellpadding="0" width="479" cellspacing="0" border="0" > 
<tbody >
<tr >

<td width="224" height="18" >表达式（后缀U表示无符号整型）
</td>

<td width="122" >比较类型
</td>

<td width="133" >结果
</td>
</tr>
<tr >

<td height="18" >0==0U
</td>

<td >无符号
</td>

<td >1
</td>
</tr>
<tr >

<td height="18" >-1<0
</td>

<td >有符号
</td>

<td >1
</td>
</tr>
<tr >

<td height="18" >-1<0U
</td>

<td >无符号
</td>

<td >0
</td>
</tr>
<tr >

<td height="18" >2147483647>-2147483647-1
</td>

<td >有符号
</td>

<td >1
</td>
</tr>
<tr >

<td height="18" >2147483647U>-2147483647-1
</td>

<td >无符号
</td>

<td >0
</td>
</tr>
<tr >

<td height="18" >2147483647>(int)2147483647U
</td>

<td >有符号
</td>

<td >1
</td>
</tr>
<tr >

<td height="18" >-1>-2
</td>

<td >有符号
</td>

<td >1
</td>
</tr>
<tr >

<td height="18" >(unsigned)-1>-2
</td>

<td >无符号
</td>

<td >1
</td>
</tr>
</tbody>
</table>
**整型和无符号整型隐式转换带来的安全风险**
<table cellpadding="0" width="598" cellspacing="0" border="0" > 
<tbody >
<tr >

<td width="416" height="18" >函数定义
</td>

<td width="182" >安全风险
</td>
</tr>
<tr >

<td width="416" height="72" >int strlonger(char *s, char *t){
return strlen(s)-strlen(t)>0
}
</td>

<td width="182" >strlen返回值size_t是定义成unsigned int。如果s的长度小于t，仍然会返回1
</td>
</tr>
<tr >

<td width="416" height="126" >#define KSIZE 1024
char kbuf[KSIZE];
int copy_from_kernel(void *user_dest, int maxlen){
int len=ksize <maxlen?ksize:maxlen;
memcpy(user_dest, kbuf, len);
return len;
}
</td>

<td width="182" >memcpy的参数为size_t,定义为unsigned int。函数原型中定义的maxlen为整型。若程序员恶意传入负数，会引发非法地址错误
</td>
</tr>
</tbody>
</table>
