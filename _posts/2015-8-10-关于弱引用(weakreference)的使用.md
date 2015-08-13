---
layout: post
title: 关于弱引用
description: WeakReference

tags: ["Android"]
comments: true
---

### 关于弱引用(WeakReference)的使用
**猜想:**在遇见的时候,MainActivity中的页面是用Fragment来实现.因为四个页面,可能消耗的内存会大,导致一些时候,系统回收资源的时候,出现有些页面显示异常的问题.我认为可以通过弱引用来处理这个问题.  

因为,新的工程中,也需要用到MainActivity中嵌套ViewPager,而每个页面又是一个Fragment的情况,所以,有可能因为内存问题,把某些页面释放掉的情况.所以在此先学习关于弱引用的知识,以及顺道了解关于软引用(SoftReference).
***
总共有三种类型的引用:

1. 强引用 
2. 软引用
3. 弱引用

它们的区别主要在于垃圾回收器对于他们回收的状况.  
`强引用(Strong Reference)`:不会被垃圾回收器回收,即便出现OutOfMemory的错误.  

`软引用(Soft Reference)`:当垃圾回收器要回收的时候,不会被回收,只有当内存不足的情况下,软引用的资源才会被回收,一般用于缓存的情况,因为像缓存图片这样的资源,当然是可以尽可能的缓存多的数据,但是又不应该因为这些缓存导致奔溃.  

`弱引用(Weak Reference)`:只要垃圾回收器要回收,就能够把资源回收掉.