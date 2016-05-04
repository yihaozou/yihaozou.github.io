---
author: 邹政华
comments: true
date: 2015-04-28
published: false 
tags: web
layout: post
title: struts标签<s:if>的一个错误使用
categories: 编程 
---

在一个循环列表中判断，变量前要加#

	  <s:iterator value="page.list" id="task">
			<tr>
	
	         	<td align="left">
					<s:if test="#task.status == 1">待报价</s:if>
	         	    <s:elseif test="#task.status == 2">已报价、待支付</s:elseif>
	         	    <s:elseif test="#task.status == 3">已支付、待上传</s:elseif>
	       		</td>     
			</tr>
	   </s:iterator>


单条数据判断，前面不需要加#，否则会失效。

	<s:if test="proxyOrderDetail.status == 1">待报价</s:if>
