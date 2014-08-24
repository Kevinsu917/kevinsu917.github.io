---
layout: post
title: Design Patterns Adapter
description: "适配器模式"

tags: ["Design Patterns"]
comments: true
---
###Adapter-适配器模式
讲到适配器模式，先上两张图表明适配器想做得是什么。
![Alt text]({{ site.url }}/images/dp/1408865303102.png)![Alt text]({{ site.url }}/images/dp/1408865317850.png)![Alt text](./1408865333259.png)
![Alt text]({{ site.url }}/images/dp/1408865463006.png)

这个产品号称全球通用转换插座。也就是，你带着它，到那个国家去，不管人家的插座标准是怎样，都能帮你转过去，想想都带感啊。

---
下面说正题。适配器做得事情就是，你手机只有usb接口，在电脑上可以充电，但是，墙上就充不了了，这个时候怎么办。
1.  在墙上搞个usb接口的插座。   
2.  搞个转接口，一头可以插usb，一头插墙。  

GOF介绍了两种实现的方式：**对象适配器模式**和**类适配器模式**。Java、C#等语言不支持多重继承。  
* 对象适配器:  
    ![Alt text]({{ site.url }}/images/dp/1408866837858.png)

```java
interface class Target{

	public void request();
}

class Adaptee{

	public void SpecificRequest()
	{
	}
}

class Adapter implement Target{
	private Adaptee adaptee = new Adaptee();
	
	public void request()
	{
		adaptee.SpecificRequest();
	}
}
```

* 类适配器模式   
    ![Alt text]({{ site.url }}/images/dp/1408867498562.png)   
    就是Adapter通过继承Target和Adaptee后，Adapter就用了Target的方法，也有了Adaptee的方法，那么，当用户想用Adapter的方式的时候，重新定义request方法，让request方法调用specificRequest方法。


JDK里面的适配器模式应用：
•java.util.Arrays#asList()
•java.io.InputStreamReader(InputStream)
•java.io.OutputStreamWriter(OutputStream)
•javax.xml.bind.annotation.adapters.XmlAdapter#marshal()
•javax.xml.bind.annotation.adapters.XmlAdapter#unmarshal()
