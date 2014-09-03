---
layout: post
title: Android Content Provider
description: "Android Content Provider"

tags: ["Android"]
comments: true
---

###Android Content Provider
Content Provider，从字面解释来说，就是信息提供商。属于Android四大组件之一。

Content Provider的意义在于，application之间数据的共享。我开发的应用，一般来说，只对我的应用公开。要做到数据共享，就需要实现ContentProvider的一些接口，通过调用已经实现的接口，来获取相应的数据。当然，对方需要获取该权限。

Content Provider就像”数据库"一样，连查询的语句都很相似。所以，你大可以把它当做数据库来看待。

Note：你不需要关注数据来源本地，还是来源于web上。

* 通过认识Contact Provider来了解Content Provider
* 更进一步了解Content Provider的基本要求
* 如何自定义自己应用的Content Provider
* Calendar Provider
* Storage Access Framework


####Contact Provider
首先，我们通过联系人Provider的例子来了认识Provider。Contact Provider就是Contact Application这个Android内置应用提供给我们获取它数据的Provider。

![Alt text]({{ site.url }}/images/contentProvider/chart1.png)
![Alt text]({{ site.url }}/images/contentProvider/chart2.png)


Contact Provider把联系人分为三个表来管理：    
1. ContactsContract.Contacts表。用保存不同的联系人用户。    
2. ContactsContract.RawContacts表。用于区分不同账户下保存的数据，如这个联系人存于google账户、或者小米账户、或者是手机等等。通过用户的account和type来区分。   
3. ContactsContract.Data表。主要保存像电话号码、Email、地址等等的数据，这个表包括了RawContacts的数据。

**Note:**     
ContactsContract.Contacts表的Contacts._ID与ContactsContract.RawContacts表的RawContacts.CONTACT_ID相关联。     
而ContactsContract.RawContacts表的RawContacts._ID与ContactsContract.Data表的Data.RAW_CONTACT_ID相关联。

*其它表可以通过查找数据库来确定，还有每个表都有哪些字段*
可以参考[文章](http://wenku.baidu.com/view/105199828762caaedd33d4d8),写得非常详细。