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

###2015.1.20新增

因为最近实现类似微信的表情,长按某个表情,会弹出一个窗口,且显示该表情的动态图,滑动到另外一个表情时,显示另外一个表情的动图.  

这里其实是一个ScrollView中嵌套GridView,然后长按GridView中某个Item(ImageView)时,通过一个Popwindow去显示Item中的动图.这里上下滑动的时候,会导致和ScrollView的滑动出现冲突.这里解决其实很简单,只要在onInterceptTouchEvent方法(拦截取消)就好了

对于TouchEvent的更多理解:  
之前讲得都是点击试讲向下传递的问题,那么下层的View对点击事件处理之后,那么上层的View如何获得点击事件呢?

在这里:我们假设A为外层的View,B为嵌套在A中的内层View.  (都是GroupView)

1. 如果B的onTouch方法返回true,也就是消费了点击事件.  
那么B的onTouchEvent方法就不会被调用.  

2. 如果在A不拦截点击事件(onInterceptTouchEvent事件返回false).且B不消费点击事件(onTouch和onTouchEvent都返回false)的情况下.  
那么A才会调用onTouchEvent事件,否则不会.  

**总结:**View通过dispatchTouchEvent方法向下传递touch事件,(ViewGroup)通过onInterceptTouchEvent来拦截,通过onTouch和onTouchEvent来判断是否消费,如果不消费向上传递.


问题1: 我们在实现onTouchListener中onTouch事件和onTouchEvent方法的区别?

在我的理解,onTouchEvent是在View中实现的,在实现这个View的时候,通用的touch事件由我们来控制.但是这时候别人用你这个View的时候,不一定能够满足他的需求,所以就需要通过实现onTouchListener中的onTouch方法来实现特殊的需求

[StackOverFlow 上的一个解释](http://stackoverflow.com/questions/5002049/ontouchevent-vs-ontouch)

[验证的Demo](https://github.com/Kevinsu917/TouchEventDemo/)


