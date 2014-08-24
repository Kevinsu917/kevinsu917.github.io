---
layout: post
title: Android Toasts
description: "Android Toasts"

tags: ["Android"]
comments: true
---
###Toasts
Toast提供的是一个小的弹窗作为反馈。在经过定义的时间后消失。
效果如图：![Alt text]({{ site.url }}/images/toast/1408859959755.png)

Toast对象由一个makeText()的方法来初始化。带有三个参数，第一个参数是应用的Context，第二个参数是提示的字符串，第三个参数是显示的时长。返回一个Toast对象，通过show()方法显示。例如：
```java
Context context = getApplicationContext();
CharSequence text = "Hello toast!";
int duration = Toast.LENGTH_SHORT;

Toast toast = Toast.makeText(context, text, duration);
toast.show();
```

---
设置Toast显示的位置。可以通过:
> toast.setGravity(Gravity.TOP|Gravity.LEFT, 0, 0);

####创建自定义的ToastView
可以把自定义的布局文件通过setView(View)方法设置给toast。
布局文件toast_layout.xml

```java
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@+id/toast_layout_root"
              android:orientation="horizontal"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent"
              android:padding="8dp"
              android:background="#DAAA"
              >
    <ImageView android:src="@drawable/droid"
               android:layout_width="wrap_content"
               android:layout_height="wrap_content"
               android:layout_marginRight="8dp"
               />
    <TextView android:id="@+id/text"
              android:layout_width="wrap_content"
              android:layout_height="wrap_content"
              android:textColor="#FFF"
              />
</LinearLayout>
```

代码：

```java
LayoutInflater inflater = getLayoutInflater();
View layout = inflater.inflate(R.layout.custom_toast,
                               (ViewGroup) findViewById(R.id.toast_layout_root));

TextView text = (TextView) layout.findViewById(R.id.text);
text.setText("This is a custom toast");

Toast toast = new Toast(getApplicationContext());
toast.setGravity(Gravity.CENTER_VERTICAL, 0, 0);
toast.setDuration(Toast.LENGTH_LONG);
toast.setView(layout);
toast.show();
```

先把xml渲染成一个view，再把View设置给toast。
**Note：通常不用new的构建方式来实例化，除非你想用自己的布局文件。**