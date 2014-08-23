---
layout: post
title: Android Activity
description: "Android Activity"

tags: [Android Component]
comments: true
---
####Activity介绍
**Activity**是最常用的组件。常说的四大组件之一。
*（Activity、Service、Broadcast Receiver、Content Provider）*
通常，一个Activity就是一个单独的屏幕，但也有时比屏幕小，浮动在其他窗体上，它上面可以显示一些控件也可以监听并处理用户的事件做出响应。Acitivity之间通过Intent进行显性通信。也可以通过IntentFilter进行隐形通信。通过startActivity()/startActivityForResult()来激活新的Activity
1. 通过显性的Intent（新Activity的类名）
2. 通过隐形的Intent（通过 action、category、type、data的方式来match )   

Note: If multiple intent filters are compatible, the system displays a dialog so the user can pick which app to use.如果多个app同时符合，将会出现一个dialog让你选择。），当然，这些都必须在manifest file中声明。


Activity的生命周期，对于开发人员来说是极其重要的。
1. Activity通过onCreate()方法创建
2. onStart()方法启动（可见）
3. onResume()方法获取焦点
4. onPause()方法是去焦点
5. onStop()方法停止（不可见）
6. onDestroy()方法销毁
![Alt text]({{ site.url }}/images/activity/1408770906827.png)

*当启动另一个Activity时，前一个Activity将会被压入栈中（Tasks and Back Stack），当点击返回键时，重新回到前一个Activity，并将其从栈中销毁。*

创建一个新的Activity必须要继承Activity类，或者它的子类，并且实现其中的回调方法。其中两个最重要的回调方法：
* onCreate()：这个方法在初始化的时候会被调用，是用来初始化Activity中的组件，在onCreate()方法中，必须调用**setContentView()**方法，来定义你的用户界面。
* onPause()：这个方法系统调用是，表明用户准备离开当前界面（尽管不一定销毁这个Activity），可在这个方法中保存需要的数据信息，因为用户可能还会到这个Activity。可以使用**onSaveInstanceState(Bundle outState)**来保存有用的资源，当activity被系统回收资源时杀掉而不丢失。

---

####Implementing a user interface实现一个用户界面：

1. 把在资源文件XML中设计好的布局文件通过把资源文件的ID传递给setContentView()方法，来初始化一个用户界面。
2. 或者直接在代码，创建Views，插入新的Views到一个ViewGroup。通过传递根View给setContentView()方法来初始化一个用户界面。（详情参考 User Interface )

####Declaring the activity in the manifest在Manifest中定义Activity：

每一个Activity都必必须在Manifest文件中定义，例如：
```
<manifest ... >
  <application ... >
<activity android:name=".ExampleActivity" android:icon="@drawable/app_icon">
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
</activity>
   </application ... >
  ...
</manifest >
```
在<application>元素下加入<activity>元素，<activity>元素中还有很多参数，如，android：name（这个唯一一个必须声明的参数）（详情见： <activity>）
在<activity>元素下可以定义intent filters通过<intent-filter>元素，通过声明，别的application可以激活改Activity。
表示这是主入口：<action android:name="android.intent.action.MAIN" />
表示这是将会在application launcher列表中出现：<category android:name="android.intent.category.LAUNCHER" />
**Note：如果你不需要和别的应用交互，大可以不用定义intent-filter元素。**

####Starting an Activity开始一个Activity
启动一个Activity可以通过显性指定启动的Activity类，例如要启动一个名为SignInActivity的类，
Intent intent = new Intent(this, SignInActivity.class);
startActivity(intent);
或者通过隐形指定启动Activity是需要的action data category。如需要开启一个发送电子邮件的Activity，指定Action，加入需要的少量数据
Intent intent = new Intent(Intent.ACTION_SEND);
intent.putExtra(Intent.EXTRA_EMAIL, recipientArray);
startActivity(intent);
当处理完后，会回到原先的Activity。

Starting an activity for a result开始一个Activity并返回结果：

有时候​，你启动一个新的Activity并希望获取回复的结果。在这种情况下，
可以通过**startActivityForResult()**方法代替startActivity()，当完成后，会通过Intent返回一个Intent到你本身的onActivityResult()回调函数上。
例如，也许你系那个用户选取一个他们的联系人，然后你对这个联系人做一些操作，那么你可以这样做：
```
private void pickContact() {
    // Create an intent to "pick" a contact, as defined by the content provider URI
    Intent intent = new Intent(Intent.ACTION_PICK, Contacts.CONTENT_URI);
    startActivityForResult(intent, PICK_CONTACT_REQUEST);
}

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    // If the request went well (OK) and the request was PICK_CONTACT_REQUEST
    if (resultCode == Activity.RESULT_OK && requestCode == PICK_CONTACT_REQUEST) {
        // Perform a query to the contact's content provider for the contact's name
        Cursor cursor = getContentResolver().query(data.getData(),
        new String[] {Contacts.DISPLAY_NAME}, null, null, null);
        if (cursor.moveToFirst()) { // True if the cursor is not empty
            int columnIndex = cursor.getColumnIndex(Contacts.DISPLAY_NAME);
            String name = cursor.getString(columnIndex);
            // Do something with the selected contact's name...
        }
    }
}
```
 
####Managing the Activity Lifecycle 管理Activity生命周期
通过管理Activity的生命周期实现回调函数对于发展一个强壮和灵活的应用来说是至关重要的，生命周期间接的影响着它本身的任务、以及与其他Activity和栈的联系。
一个Activity存在三个最重要的状态：
1. Resumed（重新开始，重新获得）
    该Activity处于屏幕的最上层同时获得焦点。

2. Paused（暂停）
    另外一个Activity处于最上层同时获得焦点，但这一个Activity仍然可见，这种情况是，另一个Activity处于最高层，且可见区域没有占全屏，或者有透明部分。一个暂停Activity是仍然活着的（这个Activity对象依然占用内存，它保存着所有状态和信息，但不能被window manager操作），但是如果当设备内存使用率非常低，会被系统杀掉。

3. Stopped（停止）
    这个Activity已经完全被另一个Activity掩盖，一个Stopped的Activity也是活着的（这个Activity对象依然占用内存，它保存着所有状态和信息，但不能被window manager操作），但是不在可见，同时当内存需要，也会被杀掉。

**Note:如果一个Activity处于Paused或者Stopped状态下，系统可以退出它，调用finish()方法。当再次启用是，又是一个完全新的Activity（不保存任何信息）。**

Activity中的CallBack方法
* onCreate()     在Activity首次被启动是调用。执行之后，不可被系统杀掉。 （下一部调用）-->onStart()
* onRestart()    在Activity被Stopped后，再次激活时使用，执行之后，不可被系统杀掉。-->onStart()
* onStart()        在Activity变为可见之前调用，执行之后，不可被系统杀掉。 -->onResume() 或者 -->onStop()
* onResume()  在Activity与用户交互前被调用，执行之后，不可被系统杀掉。 -->onPaused()
* onPause()     在开启另一个Activity时，经常用于提交未保存的数据、停止动画和其他消耗cpu的行为，只有执行后，另一个Activity才能启动。执行之后，可被系统杀掉。 -->onResume() or -->onStop()
* onStop()        在Activity不再可见是被调用，另一个Activity激活且覆盖它。执行之后，可被系统杀掉。 -->onRestart() or -->onDestroy()
* onDestroy()   销毁前调用。

####Saving activity state 保存Activity的状态

在另外一个Activity启动到最高层时，之前的Activity依然处于存活状态，仍然持有内存当Activity处于pasued或者stopped状态，之前的信息依然保存，当返回原来的Activity时，之前的改变还是在那里。
但是，如果是系统因为需要资源而销毁你之前的Activity，当返回原来的Activity时，就是通过重新创建来启动之前那个Activity，那么，之前的数据将不存在，这样的情况，不是用户所希望的。所以，我们必须把重要的信息在Activity被销毁前，保存下来。
我们可以通过onSaveInstanceState()方法：
系统会在Activity被销毁时调用onSaveInstanceState()，系统会传给这个方法一个参数 Bundle，通过这个参数可以给我们保存数据。当原来的Activity被reCreate时，传递给onCreate()和onRestoreInstanceState()方法。
通过这个两个方法其中之一，你可以恢复到之前的状态，如果没有状态信息需要恢复，那么将传递一个null值（例如第一次创建）。
![Alt text]({{ site.url }}/images/activity/1408771756500.png)

注意：在销毁你的Activity前不一定会执行onSaveInstanceState()方法，因为有可能根本没必要保存状态（用户自己通过返回键退出，显性的想退出应用），如果系统调用onSaveInstanceState()方法,那么会在onStop()方法之前甚至会在onPause()。
但是即使你不做任何事，也不实现onSaveInstanceState()，一些Activity的状态还是会被Activity默认继承的onSaveInstanceState()方法所恢复。具体来说，每个View在布局中都会调用对应的onSaveInstanceState()方法，任何可见的UI改变都会自动保存并在下次创建是恢复。例如，EditText部件保存了字符，这里只需要你为部件提供唯一的ID属性，如果不存在唯一的ID，那么系统不会保存它的状态。
注意：onSaveInstanceState()方法不能保证一定被调用，所以你应该只用来记录短暂的状态，而不应该用来存放持久数据，你应该用onPause()方法来存放持久数据。


####Activity的生命周期：

![Alt text]({{ site.url }}/images/activity/1408771715943.png)
