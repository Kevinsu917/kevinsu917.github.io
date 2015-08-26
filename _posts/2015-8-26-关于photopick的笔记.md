---
layout: post
title: 关于PhotoPick的笔记
description: "Android"

tags: ["Android"]
comments: true
---

### 关于PhotoPick的笔记
在遇见的时候,我自己也实现过一个照片选择的控件,虽然也能正常使用,但是总感觉滑动起来没有微信中的那个那么顺畅.特别是在加载图片的时候,图片少的时候感觉不出来,图片一多,差距就出来了.这次看了Coding安卓客户端的,发现他的和微信的流畅度都很高,所以借用了他的控件.通过代码查看,慢的原因主要有几点:  

1. 在用LoadManager的时候,他加载的图片直接通过CursorAdapter来展示图片,而我是通过把图片的路径找出来,放到一个ArrayList再通过BaseAdapter来加载
2. 在使用ImageLoader加载图片的时候,我使用的存本地的图片的尺寸过大,导致加载图片缓慢

以上两点,是我做的控件,在加载图片的时候缓慢的主要原因.另外,通过阅读他的代码,发现了很多非常不错的地方.例如,通过用不同的Adapter来区分有无拍照的情况.

**PhotoPickActivity**:用来显示选择图片的界面  
**PhotoPickDetailActivity**:用来显示单张图片的界面

![图片]({{ site.url }}/pic1.png)  
