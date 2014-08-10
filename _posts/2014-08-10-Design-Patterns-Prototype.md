---
layout: post
title: Design Patterns Prototype
description: "原型模式"

tags: [Design Patterns]
comments: true
---
###原型模式-Prototype
原型模式是比较好理解的一种设计模式。
从字面意思上理解，也就是给定一个原型，可以Clone出一模一样的实例。并且在内存中存在两份。
原型模式在Clone的过程中，并不通过构造函数。
![Alt text]({{ site.url }}/images/dp/partten_chart3.png)

一模一样之所以带引号，原因是存在**浅拷贝**和**深拷贝**的问题。   
在Java中Object类就很好的运用到了原型模式。Object中有一个clone的方法。子类继承了Object后，并且需要实现Cloneable接口，就可以通过调用clone方法来实现拷贝。   
**clone方法只会对java中的8种基本类型以及它们的封装类型、另外还有String类型会进行深拷贝。**    
而像数组、引用对象、容器对象等都不会进行深拷贝。举个例子，当a2是通过a1拷贝过来，a2中的非深拷贝字段会因为a1的修改而修改。所以需要另行拷贝。


> [参考]（http://home.cnblogs.com/group/topic/13210.html）