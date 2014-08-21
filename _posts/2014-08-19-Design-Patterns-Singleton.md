---
layout: post
title: Design Patterns Singleton
description: "单例模式"

tags: [Design Patterns]
comments: true
---
###单例模式-Singleton
---
**单例模式**用于全局可以调用，只有一个实例的情况，例如，一个程序只有一个配置文件，其他类都可以访问其中的配置参数。一般单例模式是用得比较多，也因为比较容易使用。

普通的单例模式：
因为不能让Client能有通过构造方法来获取实例，所有需要把构造函数私有化。

```java
		class Singleton
			{
				private Singleton instance;
				private Singleton(){}
				public Singleton getInstance()
				{
					if(instance == null)
					{
						instance = new Singleton();
					}
					return instance;
				}
			
			}
```


但是，普通的单例模式，在面对多线程的操作下，就会出现问题。
当多个线程同时调用getInstance方法是，就很有可能获取多个实例，这违背了单例模式的意图。所以，这个时候我们就需要加锁。

```java
		class Singleton
		{
			private Singleton instance;
			private static readonly object lockHelper = new Object();*锁实例*
			private Singleton(){}
			public Singleton getInstance()
			{		
				lock(lockHelper)
				{
					if(instance == null)
					{
						instance = new Singleton();
					}
					return instance;
				}
			}
		}
```


这样就可以避免同时进入初始化的过程，不会再获取多个实例，但是，这又导致每次调用getInstance方法的时候
需要锁上该方法，影响效率，所以再在外面加一个判断，来避免这个问题。

```java
	 class Singleton
		{
			private Singleton instance;
			private static readonly object lockHelper = new Object();*锁实例*
			private Singleton(){}
			public Singleton getInstance()
			{		
				if(instance == null)
				{
					lock(lockHelper)
					{
						if(instance == null)
						{
							instance = new Singleton();
						}
						return instance;
					}
				}
			}
		
		}
```

这样，就只会在第一次初始化的时候加锁。

当然也可以把初始化直接放变量生成的时候。

> private Singleton instance = new Singleton();