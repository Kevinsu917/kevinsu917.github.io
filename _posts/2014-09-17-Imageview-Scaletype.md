---
layout: post
title: ImageView ScaleType
description: “ImageView ScaleType"

tags: ["Android,Component"]
comments: true
---

###关于ImageView的ScaleType	
最近因为工作需要，需要显示像微信的朋友圈，一张图，多张图，显示的比例不同的效果。美术希望的要求，显示比例不能变形。尽可能显示图片的内容。	
今天关于ScaleType做个笔记。	

**ScaleType**定义了8个值：		
1. CENTER :在View中居中，但是不拉伸，View不一定完全填充		
2. CENTER_CROP :按照比例缩放图片（不变形），但是图片的显示尺寸会等于或大于View的尺寸，会裁剪图片显示，完全填充View	
3. CENTER_INSIDE :按照比例缩放图片（不变形），但是图片的尺寸等于或小于View的尺寸，完全显示图片，但是View不一定完全填充	
4. FIT_CENTER :按照比例缩放图片（不变形），然后显示在View的中间部分。	
5. FIT_END :按照比例缩放图片（不变形），然后显示在View的下面部分。	
6. FIT_START :按照比例缩放图片（不变形），然后显示在View的上面部分。	
7. FIT_XY :不按比例缩放，只为填充满View	
8. MATRIX :按照一个指定的Matrix矩阵来缩放图片	

借用两张图来看就很好理解了：
![Alt text]({{ site.url }}/images/imageView_ScaleType/pic1.jpeg)
![Alt text]({{ site.url }}/images/imageView_ScaleType/pic2.jpeg)


[原图出自](http://blog.csdn.net/xilibi2003/article/details/6628668)