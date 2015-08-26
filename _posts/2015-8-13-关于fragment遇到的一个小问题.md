---
layout: post
title: 关于fragment遇到的一个小问题
description: "Android"

tags: ["Android"]
comments: true
---

### 关于fragment遇到的一个小问题
在布局文件中,如果用<fragment>标签的话,在android:name中标记默认的Fragment时,在代码中就算是使用replace方法来替换,都会出现replace的Fragment和默认设定的fragment出现重叠的问题.  
如下图:  
![]({{ site.url }}/problem.png)  

解决的方法,使用FrameLayout作为Fragment被替换的布局容器,即可.    
![]({{ site.url }}/solve.png)  


[参考](http://www.apkbus.com/android-121899-1-35.html)