---
layout: post
title: 关于AndroidManifest文件中的process的属性
description: process

tags: ["Android"]
comments: true
---

### 关于AndroidManifest文件中的process的属性

****
由于Android中对内存使用做了限制.每个单独的进程可以使用的内存大小一般为24M的内存.(不同的机型限制有些不同,16MB,24MB,32MB....).在之前的公司中,曾经因为一些比较耗内存的操作,例如照相,选择照片...导致内存不足,系统回收资源导致各式各样的问题.

原来可以通过AndroidManifest中的process属性,给某些Activity在一个新的进程中打开.

```
<activity
    android:name=".common.photopick.PhotoPickActivity"
    android:label="@string/app_name"
    android:process=":pickimage"
    android:screenOrientation="portrait"
    android:theme="@style/TranslateTheme" />
```
    
一般也会用在Service组件中使用.  
如果被设置的进程名是以一个冒号开头的，则这个新的进程对于这个应用来说是私有的，当它被需要或者这个服务需要在新进程中运行的时候，这个新进程将会被创建。如果这个进程的名字是以小写字符开头的，则这个服务将运行在一个以这个名字命名的全局的进程中，当然前提是它有相应的权限。这将允许在不同应用中的各种组件可以共享一个进程，从而减少资源的占用。