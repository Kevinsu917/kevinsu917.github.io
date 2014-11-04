---
layout: post
title: ScollView嵌套EditText导致的冲突[转载]
description: “ScollView嵌套EditText"

tags: ["Android,IAround"]
comments: true
---  

###ScollView嵌套EditText导致的冲突[转载]

EditText设置maxLines之后，文本行数超过maxLines，会网上折叠，上下滑动可以浏览所有文本。

若EditText外层有scrollView，在EditText上下滑动，不会像正常情况那样上，只会滑动了scrollView。

####前提是ScrollView嵌套TextView
**解决办法是：**

//处于TextView的点击域时,直接消费touch事件,不传递给ScrollView
//etTextContent是EditText

```
etTextContent.setOnTouchListener(new View.OnTouchListener() {
			public boolean onTouch(View arg0, MotionEvent arg1) {
				// TODO Auto-generated method stub
				arg0.getParent().requestDisallowInterceptTouchEvent(true);
				switch (arg1.getAction() & MotionEvent.ACTION_MASK) {
				case MotionEvent.ACTION_UP:
					arg0.getParent().requestDisallowInterceptTouchEvent(false);
					break;
				}
				return false;
			}
		});	
```

其实就是当Touch的区域是EditText的时候,把SrollView的响应处理事件的功能关闭,只接受EditText的事件.

但这样就会有一个问题,当EditText不需要滑动的时候,也不能滑动外面的ScrollView,暂时还没找到好方法处理EditText是否超出maxLines的情况.

[原文链接](http://blog.csdn.net/tao_zi7890/article/details/30479911)