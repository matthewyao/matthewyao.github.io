---
layout:     post
title:      Spring两种代理方式初探
discription: Spring有两种代理方式：CGLIB代理和JDK代理，两种方式各有区别，在实际应用中也需要注意区分。
date:       2016-09-13 23:19:00
catalog:    true
tags:       [Spring, Java, AOP, 代理模式, ]
---


#Spring两种代理方式初探

## 什么是代理
> 代理模式是Java开发中一种常用的设计模式，为其他对象提供一种代理以控制对这个对象的访问。在某些情况下，一个对象不适合或者不能直接引用另一个对象，而代理对象可以在客户端和目标对象之间起到中介的作用
- 代理模式分为静态代理、动态代理：
- **静态代理**是由程序员创建或工具生成代理类的源码，再编译代理类。所谓静态也就是在程序运行前就已经存在代理类的字节码文件，代理类和委托类的关系在运行前就确定了。
- **动态代理**是在实现阶段不用关心代理类，而在运行阶段才指定哪一个对象。

## JDK动态代理实例
   动态代理类克服了proxy需要继承专一的interface接口，并且要实现相应的method的缺陷。从JDK 1.3以来，Java 语言通过java.lang.reflex库提供的三个类直接支持代理：

```
 java.lang.reflect.Proxy
 java.lang.reflect.InvocationHandler
 Method
```

Proxy类在运行时动态创建代理对象，这也是dynamic proxy的由来，下面是类图，其中最重要的是newProxyInstance,这个方法中，指明了将要代理的类的加载器，业务类接口，以及代理类要执行动作的调用处理器（InvokeHandler)，其定义如下：


```
public static Object newProxyInstance(ClassLoader loader,Class<?>[] interfaces,
	InvocationHandler h) throws IllegalArgumentException;
```

> Returns an instance of a proxy class for the specified interfaces that dispatches method invocations to the specified invocation handler.

- 参数说明：
- ClassLoader loader：类加载器
- Class<?>[] interfaces：得到全部的接口
- InvocationHandler h：得到InvocationHandler接口的子类实例

当系统有了一个代理对象之后，对原方法的调用会首先被分派到一个调用处理器（Invocation Handler)。
InvocationHandler 接口如下图所示：

```
Object invoke(Object proxy,Method method,Object[] args) throws Throwable;
```
> Processes a method invocation on a proxy instance and returns the result. This method will be invoked on an invocation handler when a method is invoked on a proxy instance that it is associated with.

实例：
接口类：

```
package com.mahoutchina.pattern.proxy.dynamicproxy;

public interface BookFacade {
	public void addBook();
	public void deleteBook();
}
```
业务逻辑类：
```
package com.mahoutchina.pattern.proxy.dynamicproxy;

public class BookFacadeImpl implements BookFacade {
	@Override
	public void addBook() {
		System.out.println("add book logic is running。。。");
	}
	@Override
	public void deleteBook() {
		System.out.println("delete book logic is running。。。");
	}
}
```

动态代理类：

```
package com.mahoutchina.pattern.proxy.dynamicproxy;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;


public class BookFacadeProxy implements InvocationHandler {
	private Object target;

	public Object bind(Object target) {
		this.target = target;
		// 取得代理对象
		return Proxy.newProxyInstance(target.getClass().getClassLoader(),
				target.getClass().getInterfaces(), this);
	}

	@Override
	public Object invoke(Object proxy, Method method, Object[] args)
			throws Throwable {
		Object result=null;
		System.out.println("Proxy start...");
		System.out.println("method name:"+method.getName());
		result=method.invoke(target, args);
		System.out.println("Proxy end...");
		return result;
	}

}
```

测试类：

```
package com.mahoutchina.pattern.proxy.dynamicproxy;

public class TestProxy {
	public static void main(String[] args) {
		BookFacadeProxy proxy = new BookFacadeProxy();
		BookFacade bookProxy = (BookFacade) proxy.bind(new BookFacadeImpl());
		bookProxy.addBook();
		bookProxy.deleteBook();
	}
}
```

## CGLib 简介

JDK的动态代理机制只能代理实现了接口的类，而不能实现接口的类就不能实现JDK的动态代理，cglib是针对类来实现代理的，他的原理是对指定的目标类生成一个子类，并覆盖其中方法实现增强，但因为采用的是继承，**所以不能对final修饰的类进行代理**。

实例：
业务类：

```
package net.battier.dao;

public interface BookFacade {
	public void addBook();
}
```

```
package net.battier.dao.impl;

/**
 * 这个是没有实现接口的实现类
 */
public class BookFacadeImpl1 {
	public void addBook() {
		System.out.println("增加图书的普通方法...");
	}
}
```

代理类：

```
package net.battier.proxy;
import java.lang.reflect.Method;
import net.sf.cglib.proxy.Enhancer;
import net.sf.cglib.proxy.MethodInterceptor;
import net.sf.cglib.proxy.MethodProxy;

//使用cglib动态代理
public class BookFacadeCglib implements MethodInterceptor {
	private Object target;

	//创建代理对象
	public Object getInstance(Object target) {
		this.target = target;
		Enhancer enhancer = new Enhancer();
		enhancer.setSuperclass(this.target.getClass());
		// 回调方法
		enhancer.setCallback(this);
		// 创建代理对象
		return enhancer.create();
	}

	@Override
	// 回调方法
	public Object intercept(Object obj, Method method, Object[] args,
			MethodProxy proxy) throws Throwable {
		System.out.println("事物开始");
		proxy.invokeSuper(obj, args);
		System.out.println("事物结束");
		return null;
	}
}
```

测试：

```
package net.battier.test;

import net.battier.dao.impl.BookFacadeImpl1;
import net.battier.proxy.BookFacadeCglib;

public class TestCglib {

	public static void main(String[] args) {
		BookFacadeCglib cglib=new BookFacadeCglib();
		BookFacadeImpl1 bookCglib=(BookFacadeImpl1)cglib.getInstance(new BookFacadeImpl1());
		bookCglib.addBook();
	}
}
```