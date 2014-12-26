---
layout: post
title: attrs.xml
description: "android/res/value/attrs.xml"

tags: ["Android"]
comments: true
---

###关于文件res/value/attrs.xml的理解:
***
其实我们在xml定义了布局,然后给布局一些参数,最后,都是为了提供给我们的.java文件中使用,最终我还是得通过获取我们在xml文件中初始化的数据,然后通过代码初始化各个View.
  
为了理解attrs.xml文件,我们首先来看一般的布局文件:  

![Alt text]({{ site.url }}/images/attrs/1.png)   

这是一个最基本的布局文件,  
在下划线1的位置,这里的结构是 <xmlns:标签名="属性的包名">     
在下划线2的位置,这里的结构是 <标签名:属性名="属性值">    

这里**标签名**就跟我们的变量名一样,后面的属性的包名其实就是存放属性的路径[也就是这个路径下的一个文件存放了各种各样的属性名和属性值].而下划线2的内容表明,在该路径下,有一个叫做layout_width的属性名,还有他有一个值叫做wrap_content.这里那个路径下面放的就是attrs.xml文件.  

也就是在http://schemas.android.com/apk/res/android这个路径下有一个attrs.xml文件,这个文件中定义了layout_width这个属性名还有wrap_content这个属性值   

**这样我们可以得出,attrs.xml文件的作用:定义属性名和定义属性值**  
接下来,我们看下attrs.xml文件的定义:  

![Alt text]({{ site.url }}/images/attrs/2.png)   

这里<declare-styleable>的标签就是定义一个组属性,而后面的name是为了在.java文件中找到对应属性用的,我们后面再说.<attr>标签就是定义属性用的,name就是属性名,format对应属性的类型.

到这里,我们就可以了解到,attrs.xml就是一个定义属性的一个文件.提供给布局文件使用.然后,就像我们开头说的,布局文件最后还是要让java文件获取到值,那么是怎么做到的呢?  

```java
TypedArray a = context.obtainStyledAttributes(attrs, R.styleable.MyView);
int textColor = a.getColor(R.styleable.MyView_textColor, 0XFFFFFFFF);
float textSize = a.getDimension(R.styleable.MyView_textSize, 36);
```  

可以通过代码发现,TypedArray是用来获取一组属性的,而"属性组名_属性名"就可以定位到某一个属性.这样就可以获取我们在布局文件中初始化的值了.

至于属性的值有什么类型之类的,[参考文章](http://blog.csdn.net/jiangwei0910410003/article/details/17006087)写得非常详细