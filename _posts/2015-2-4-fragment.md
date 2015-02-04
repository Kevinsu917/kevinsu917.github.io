---
layout: post
title: 关于Fragment
description: fragment

tags: ["Android"]
comments: true
---

从了解fragment的过程,我分为以下的几点
****
####1.什么是Fragment
其实fragment就像一个View(当然,这种观点是错误的,因为Fragment类继承的是Object类,做这个比方只是为了更容易理解fragment).首先Fragment是从Android3.0引入的概念,为什么要引入Fragment呢,由于Android设备的屏幕越来越大,同一个应用在不同尺寸的设备商显示一样的内容,那么在大尺寸上的设备上看就是放大版的效果,没有太大的意义.大屏幕设备的功能就是能够在一个页面上,显示更多的内容.那么Fragment就提供这个功能.让在不同的尺寸的设备上通过动态管理UI,展示不同的效果.例如像QQ,小尺寸的设备,在一个页面就只展示联系人列表,点击某个联系人就跳转到聊天界面;但是在大尺寸的设备,就可以做到,左边显示联系人列表,右边显示聊天界面.

上面提到一个左边显示联系人列表,右边显示聊天界面,其实左,右两边各都是一个Fragment,这么说,就是一个Activity中可以有多个Fragment.就想Activity中可以有多个View一样.  

那么Fragment有什么特别的地方呢?  
1. Fragment和Activity一样,有自己的生命周期 [参考](http://www.cnblogs.com/purediy/p/3276545.html)    
2. Fragment与Fragment之前可以通过Activity进行间接通信  
3. 在Activity中添加Fragment的方式比较特殊  [参考](http://www.cnblogs.com/kissazi2/p/3440257.html)  
4. Fragment可以像Activity一样,加入BackStack,可以通过返回键,回到上一次显示的Fragment  
5. 多个Fragment可以同时挂载与一个Activity上面.


####2.如何使用Fragment
关于如何将fragment加入到Activity中,有两种方法.  
1. 在activity的布局文件中定义一个<fragment>元素  
2. 通过代码动态把一个fragment加入到一个已经存在的ViewGroup下   
3. 但是,一个fragment并不是非要成为一个Activity的布局,你可以用一个没有界面的fragment为activity工作.  
**可以通过3个方法来标识唯一的fragment**  
* 提供android:id属性的唯一标识id  
* 提供android:tag属性的唯一标识字符串(findFragmentByTag())  
* 如果上面两种都不使用,系统会用fragment容器的id作为唯一标识id

####3.如何管理Fragment
通过FragmentManager这个类来管理activity中的fragment,可以通过getFragmentManager()这个方法来获取实体.通过FragmentManager,你可以做以下事情:  
* 通过findFragmentById获取一个提供UI的fragment,或者通过findFragmentByTag来获取一个不提供UI的fragment.  
* 通过方法popBackStack从back stack弹出fragment  
* 注册一个back statck改变的监听器,用addOnBackStackChangedListener方法   
* 还可以通过打开一个FragmentTransaction,行程一个事务,对fragment进行操作

####4.关于Fragment的事务
事务就是把一系列的操作当成一件事情处理,例如,你可能对一个AFragment从activity中移除,添加BFragment,然后又用CFragment替换了BFragment,那么这个事务总共有3个操作,步骤:  

```
// Create new fragment and transaction
Fragment newFragment = new ExampleFragment();
FragmentTransaction transaction = getFragmentManager().beginTransaction();

// Replace whatever is in the fragment_container view with this fragment,
// and add the transaction to the back stack
transaction.replace(R.id.fragment_container, newFragment);
transaction.addToBackStack(null);

// 提交一个事务,这是必须得
transaction.commit();  
```

####5.关于Fragment与Activity的通信
通过在Fragment中定义一个接口,让Activity实现这个接口,调用onAttach方法时候,让Activity的listener传递给Fragment的listener. 

FragmentA定义一个接口:
 
```
public static class FragmentA extends ListFragment {
    ...
    // Container Activity must implement this interface
    public interface OnArticleSelectedListener {
        public void onArticleSelected(Uri articleUri);
    }
    ...
}
```

当调用onAttach方法时候,获取Activity中实现的方法:

```
public static class FragmentA extends ListFragment {
    OnArticleSelectedListener mListener;
    ...
    @Override
    public void onAttach(Activity activity) {
        super.onAttach(activity);
        try {
            mListener = (OnArticleSelectedListener) activity;
        } catch (ClassCastException e) {
            throw new ClassCastException(activity.toString() + " must implement OnArticleSelectedListener");
        }
    }
    ...
}
```

调用Activity中实现的方法,告诉Activity现在选择的是那个Item

```
public static class FragmentA extends ListFragment {
    OnArticleSelectedListener mListener;
    ...
    @Override
    public void onListItemClick(ListView l, View v, int position, long id) {
        // Append the clicked item's row ID with the content provider Uri
        Uri noteUri = ContentUris.withAppendedId(ArticleColumns.CONTENT_URI, id);
        // Send the event and Uri to the host activity
        mListener.onArticleSelected(noteUri);
    }
    ...
}
```

####6.对比Activity与Fragment的生命周期
![Alt text]({{ site.url }}/images/fragment/activity_fragment_lifecycle.png)  

