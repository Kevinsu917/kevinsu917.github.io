---
layout: post
title: 关于Intent和IntentFilter
description: Base Knowlege Review

tags: ["Base Knowlege Review"]
comments: true
---


在官方文档中,已经描述的非常清楚了,这里,只是记录一下我认为基础,重要的部分.

首先Intent的作用就是提供给组件之间通信的一个消息传递对象.

所以主要是为了启动组件的时候,传递消息:

1. Activity
2. Service
3. Broadcast

在启动以上三种组件的时候,带上Intent对象,因为Intent中可以携带而外的信息,所以当启动新的组件的时候,可以获取Intent中的信息,起到消息传递的功能.当然不只这个功能.请看下面的图:

![]({{ site.url }}/images/base_review/intent.png)

从图中可以看出,通过startActivity启动新的Activity,其实是先通过系统去过滤,然后系统分析Intent,知道你到底要开启哪个Activity.当然你可能只想打开某个网站,那么系统就会去找能打开某个网站的app供你选择.这就引出了Intent的显式和隐式两种类型.

Intent 分为两种类型：

* 显式 Intent ：按名称（完全限定类名）指定要启动的组件。通常，您会在自己的应用中使用显式 Intent 来启动组件，这是因为您知道要启动的 Activity 或服务的类名。例如，启动新 Activity 以响应用户操作，或者启动服务以在后台下载文件。

* 隐式 Intent ：不会指定特定的组件，而是声明要执行的常规操作，从而允许其他应用中的组件来处理它。例如，如需在地图上向用户显示位置，则可以使用隐式 Intent，请求另一具有此功能的应用在地图上显示指定的位置。

**Note:为了确保应用的安全性,不要通过隐式 Intent的方式启动Service.**

Intent中包括了ComponentName, Action, Data, Category, Extra, Flag这些信息.

* ComponentName:用于显式启动组件使用.
* Action:定义了你想要做的操作,例如系统本身定义的ACTION_VIEW,表示你想查看某些东西.
* Data:用来保存操作对应的数据,可能你想查看的是图片,那么就打开查看图片的app.数据由URI和MIME类型来定义
* Category:一个包含应处理 Intent 组件类型的附加信息的字符串,常见的有CATEGORY_LAUNCHER,表示Activity是用来启动使用的.CATEGORY_BROWSABLE表示通过浏览器打开.
* Extra:用来存放额外的数据.可以是多种类型,通过key-value的形式来存取.系统也定义了一些Key
* Flag:用来表示Activity对应的启动方式以及存放在哪个task


通过隐式Intent启动组件,有可能有多种选择的可能,所以当出现多个app可以使用的时候,系统会以列表的形式提供给用户选择.注意一点,既然有多个app可以使用,那么也可能一个app选择都没有,这个时候就需要判断,否则会导致奔溃.

以下代码表示如果有多个选择的时候,必须提供选择列表,即使用户已经选择了默认使用哪个app打开.
另外就是通过resolveActivity方法判断是否有可以处理的activity.
```
Intent sendIntent = new Intent(Intent.ACTION_SEND);
String title = getResources().getString(R.string.chooser_title);
Intent chooser = Intent.createChooser(sendIntent, title);
if (sendIntent.resolveActivity(getPackageManager()) != null) {
    startActivity(chooser);
}
```

显式Intent在启动组件的没什么好说.隐式Intent通过对应在AndroidManifest文件中对Activity定义&lt;intent-filter&gt;中的属性匹配,来达到启动Activity的目的,匹配限制请查看官方文档.

**注意如果不想让别的应用启动,可以在AndroidManifest中设置exported属性为false**

**注意： 对于所有 Activity，您必须在清单文件中声明 Intent 过滤器。但是，广播接收器的过滤器可以通过调用 registerReceiver() 动态注册。稍后，您可以使用 unregisterReceiver() 注销该接收器。这样一来，应用便可仅在应用运行时的某一指定时间段内侦听特定的广播。**


#### 还有PendingIntent

PendingIntent对象是Intent的包装器,用于授权外部应用使用的Intent,就想它从您应用本身的进程中执行一样.

PendingIntent的主要用例包括：

* 声明用户使用您的通知执行操作时所要执行的 Intent（Android 系统的 NotificationManager 执行 Intent）。
* 声明用户使用您的 应用小工具执行操作时要执行的 Intent（主屏幕应用执行 Intent）。
* 声明未来某一特定时间要执行的 Intent（Android 系统的 AlarmManager 执行 Intent）。

PendingIntent有三个方法,分别是封装Activity-Intent,Broadcast-Intent,Service-Intent的方法.方便应用外部执行Intent.


[官方文档](https://developer.android.com/guide/components/intents-filters.html#Resolution)
