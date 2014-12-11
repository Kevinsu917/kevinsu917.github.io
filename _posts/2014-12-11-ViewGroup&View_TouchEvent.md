---
layout: post
title: ViewGroup & View & TouchEvent
description: "Touch事件传递"

tags: ["Android"]
comments: true
---
###关于View和ViewGroup,以及之间的TouchEvent的传递

***

关于Android的UI,最基础的莫过于了解**View**和**ViewGroup**的关系.最简单的来说,ViewGroup是容器(**容器里面可以装容器,也可以装单元**),View是容器中的单元(**不可以再装单元或者容器**).就会有以下的图:  
![Alt text]({{ site.url }}/images/ViewGroup&View/4.png)   

***其实ViewGroup也是继承于View***   

View派生出的直接子类有：AnalogClock,ImageView,KeyboardView, ProgressBar,SurfaceView,TextView,ViewGroup,ViewStub  

View派生出的间接子类有：AbsListView,AbsSeekBar, AbsSpinner, AbsoluteLayout, AdapterView<T extends Adapter>,AdapterViewAnimator, AdapterViewFlipper, AppWidgetHostView, AutoCompleteTextView,Button,CalendarView, CheckBox, CheckedTextView, Chronometer, CompoundButton,

ViewGroup派生出的直接子类有：AbsoluteLayout,AdapterView<T extends Adapter>,FragmentBreadCrumbs,FrameLayout,LinearLayout,RelativeLayout,SlidingDrawer

ViewGroup派生出的间接子类有：AbsListView,AbsSpinner, AdapterViewAnimator, AdapterViewFlipper, AppWidgetHostView, CalendarView, DatePicker, DialerFilter, ExpandableListView, Gallery, GestureOverlayView,GridView,HorizontalScrollView, ImageSwitcher,ListView,



在了解了View和ViewGroup的关系后,说说关于Touch事件的传递

在这里,我们需要了解三个方法:   
1. onInterceptTouchEvent()[ViewGroup才有的方法]  
2. onTouchEvent()  
3. dispatchTouchEvent()  

通过名字,我们可以知道这些方法分别是,是否拦截Touch事件方法/Touch事件方法/分配Touch事件方法. 先从简单地一个ViewGroup和一个View的情况下分析:      

![Alt text]({{ site.url }}/images/ViewGroup&View/0.png) 

当ViewGroup要消费掉Touch事件的时候,也就是黄色的那一段:    

![Alt text]({{ site.url }}/images/ViewGroup&View/1.png) 

就不会传递到MyView,而且顺序是:  

 dispatchTouchEvent -> onInterceptTouchEvent -> onTouchEvent;  

当ViewGroup不消费Touch事件,那么调用绿色的一段:  

![Alt text]({{ site.url }}/images/ViewGroup&View/2.png) 

可以看到,传到MyView的时候,调用的顺序是:  

dispatchTouchEvent -> onTouchEvent

#####再提一点
当ViewGroup的ACTION_DOWN事件拦截了,那么就不会再往下传递,   
当ViewGroup的ACTION_MOVE或者ACTION_UP事件被拦截,那么View就会收到ACTION_CANCEL事件   

****
[参考1](http://bbs.csdn.net/topics/370144745)  
[参考2](http://blog.csdn.net/android_tutor/article/details/7193090)


