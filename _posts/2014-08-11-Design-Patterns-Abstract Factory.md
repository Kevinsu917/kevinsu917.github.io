---
layout: post
title: Design Patterns Abstract Factory
description: "抽象工厂模式"

tags: [Design Patterns]
comments: true
---
###抽象工厂-Abstract Factory
**要点：**
1. 用工厂方法提供对象
2. 提供的是一组对象

**目的：**  
*客户对如何生成对象的过程不可见

抽象工厂模式可以向客户端提供一个接口，使得客户在不必要指定产品具体类型的情况下，   
创建多个产品族中的产品对象。   
抽象工厂模式面对的问题是多产品等级接口的系统设计。  
有两个重要的概念需要理清：**产品族** 和 **产品等级**  
**产品族：**是指位于不同产品等级接口中，功能相关的产品组成的家族。比如AMD的CPU和AMD的主板组成一个家族。Intel的CPU和Intel的主板，又组成另外一个家族。   
**产品等级：**而两个家族的CPU同为一个等级，两个家族的主板同为一个等级。  
![Alt text]({{ site.url }}/images/dp/partten_chart4.png)

---

Client只需要通过调用工厂里面的方法就可以创建一个族的一系列对象。而不需要知道Window和ScrollBar是怎么创建的。当然只能创建一个族的对象。（Client不会同时得到PMWindow+MotifScroolBar这样一组不能协作的对象。）
![Alt text]({{ site.url }}/images/dp/partten_chart5.png)

--- 
**相关实现：**  
>[javax.xml.parsers.DocumentBuilderFactory#newInstance()][DocumentBuilderFactory]
[javax.xml.transform.TransformerFactory#newInstance()][TransformerFactory]
[javax.xml.xpath.XPathFactory#newInstance()][XPathFactory]

[DocumentBuilderFactory]: http://docs.oracle.com/javase/6/docs/api/javax/xml/parsers/DocumentBuilderFactory.html#newInstance%28%29
[TransformerFactory]: http://docs.oracle.com/javase/6/docs/api/javax/xml/transform/TransformerFactory.html#newInstance%28%29
[XPathFactory]: http://docs.oracle.com/javase/6/docs/api/javax/xml/xpath/XPathFactory.html#newInstance%28%29