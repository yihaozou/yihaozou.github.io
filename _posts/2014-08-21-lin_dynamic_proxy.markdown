---
author: 邹政华
comments: true
date: 2014-8-21
published: true 
tags: Java
layout: post
title:  林信良Java学习笔记－动态代理
categories: 读书 
---

反射API中提供动态代理相关类, 可让你不必为特定接口操作特定代理对象. 使用动态代理机制，可使用一个处理者(Handler)代理多个接口的操作对象。

处理者必须操作java.lang.reflect.InvocationHandler接口,比方说我们要设计一个LoggingHandler类:



	import java.lang.reflect.*;
	import java.util.logging.*;

	public class LoggingHandler implements InvocationHandler{
		private Object target;
		public Object bind(Object target){
			this.target=target;
			return Proxy.newProxyInstance(
				target.getClass().getClassLoader(),
				target.getClass().getInterfaces(),
				this
			);
		}
	}

	public  Object invoke(Object proxy, Method method, Object[] args) throws Throwable{
		Object result=null;
		try{
			//  自定义的代码
			//  ...
			result = method.invoke(target, args);
		}catch(IllegalAccessException | IllegalArgumentException){
			log(e.toString);
		} 
	} 

主要概念时使用Proxy.newProxyInstance()方法建立代理对象,调用时必须制定类加载器, 告知要代理的接口, 以及接口上定义方法被调用时的处理者(InvocationHandler实例). Proxy.newProxyInstance方法底层会使用原生(Native)方式生成代理对象的Class实例, 并利用它来生成代理对象, 代理对象会操作指定要代理的接口。

如果操作Proxy.newProxyInstance()返回的代理对象, 在每次操作时会调用处理器(InvocationHandler实例)invoke方法, 并传入代理对象、被调用方法的method实例于参数值.可以在invoke()方法中添加日志或其他自定义的功能。

驱动程序：

	public class ProxyDemo{
		public static void main(String[] args){
			LoggingHandler loggingHandler = new LoggingHandler();
			Hello helloProxy = (Hello)loggingHandler.bind(new HelloSpeaker());
			helloProxy.hello("Justin");
		}
	}
