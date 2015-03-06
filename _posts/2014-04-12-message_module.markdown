---
author: 邹政华
comments: true
date: 2014-04-12 11:30:43+00:00
layout: post
slug: '%e7%9f%ad%e4%bf%a1%e6%a8%a1%e5%9d%97%e6%a6%82%e8%a6%81%e8%ae%be%e8%ae%a1%ef%bc%88%e7%ae%80%e5%8c%96%ef%bc%89'
title: 短信模块概要设计（简化）
wordpress_id: 344
categories: 编程
---

（短信模块的功能：向公司各产品提供接口，各产品通过短信模块向客户发送短信）

**数据库设计：**
<table >
<tbody >
<tr >

<td width="151" valign="top" >表中文名称
</td>

<td width="180" valign="top" >表英文名称
</td>

<td width="236" valign="top" >描述
</td>
</tr>
<tr >

<td width="151" valign="top" >短信配置参数表
</td>

<td width="180" valign="top" >XXXXX
</td>

<td width="236" valign="top" > 包含各项配置参数，如服务器ip、端口，调用的脚本，用户名，密码（加密后），短信重发次数等
</td>
</tr>
<tr >

<td width="151" valign="top" >短信入参表
</td>

<td width="180" valign="top" >XXXXX
</td>

<td width="236" valign="top" > 包含短信内容，手机号，发送时间日期，发送机器的ip等信息
</td>
</tr>
<tr >

<td width="151" valign="top" >短信发布明细表
</td>

<td width="180" valign="top" >XXXXX
</td>

<td width="236" valign="top" > 记录短信发送结果等信息
</td>
</tr>
</tbody>
</table>
**流程设计：**


![短信流程图](/images/2014/2014-04-12-short_message_flow.jpg)

**外部接口设计**

短信模块对外提供报文和文件两种接口。使用报文方式（流程图中的TCP/IP)发送单条短信，只需要使用短信入参组件向短信入参表插入短信内容即可。使用文件方式（流程图中的FTP）发送短信内容时，需要在短信入参表中指定文件发送脚本（由system函数调用），短信文件格式参照给定的例子，单个文件可包含5000条短信机收件人信息。
