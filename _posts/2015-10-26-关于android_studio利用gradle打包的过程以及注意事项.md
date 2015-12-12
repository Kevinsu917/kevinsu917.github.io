---
layout: post
title: Gradle打包
description: "Gradle打包"

tags: ["Android"]
comments: true
---

### 关于Android Studio利用Gradle打包的过程以及注意事项

终于到了项目要打包的时候了,没试过自己打包,而且还是用gradle打包,不过早听说了gradle这个强大的构建工具,简化了打包的很多步骤,特别是在打渠道.

##### 问题1:
在我们直接通过run运行后,会在outputs/apk下面生成两个apk包,分别是**app-debug-unaligned.apk**和**app-debug.apk**,一开始,我还错误的认为unaligned是未签名的包!其实根本不是,signed才是签名的意思.aligned是联合的意思.通过查找资料发现,其实这个unaligned.apk只是一个中间产物.

1. 生成unaligned APK
2. unaligned APK变成aligned,并生成aligned APK

其实这是一个什么过程呢?  
其实这是android通过一个叫[zipalign](http://developer.android.com/intl/zh-cn/tools/help/zipalign.html)的归档工具,目的是为了把所有未压缩的数据(例如图片)通过一种特别的关系归档起来,减少程序运行时的内存消耗.

所以aligned版本的包就是通过unaligned输出的文件,通过zipalign工具,再处理一下,就生成我们要的apk包

##### 问题2:
现在我们打出来的包还是debug包,我需要的是打出能够发布到各个渠道的release包,这个时候,就是我们要对我们的包进行签名的过程了.   
**Note:android gradle插件定义了两种apk的类型,debug和release**  

如何签名,其实就是修改build.gradle文件,当然如果我们对gradle中语言的语法不熟悉的话,那么对于写起来确实有些困难.其实我们可以通过Android Studio中的设置来进行签名打包(实质上还是改动了build.gradle文件).

通过下图,我们可以看到一个android工程的构建和运行过程:  
![](http://developer.android.com/images/build-simplified.png)  
通过,编译->打包生成各种二进制文件以及资源文件,最后通过签名,安装到设备或者模拟器上面.但是我们可能会问,我们在运行的时候,根本没有对debug进行签名啊.其实debug的生成也是有签名的,只是用默认在用户的目录下的.android/目录下,有一个debug.keystore的文件.而且是没有密码的.

回归到我们如何用自定义的keystore签名.(以Android Studio为例子)  

1. Build -> Generate Signed APK -> 新建or选择 keystore -> 输入各种密码 一直next  
2. BuildType 选择release,就是对release版本进行签名
3. 这个时候再看右侧的Gradle Projects,里面有很多task.   
![Alt text]({{ site.url }}/images/gradle_pic.png)  
其中就有专门对release打包的task  

具体的签名方式可以阅读[官方文档](http://developer.android.com/intl/zh-cn/tools/publishing/app-signing.html)

##### 问题3:
gradle另外一个强大之处,就是多渠道打包.当然Ant也可以做到,只是实在太复杂了.  
对于多渠道打包,只需要修改两个文件: AndroidManifest.xml 和 app的build.gradle.  
通常,我们都是通过友盟统计来修改不同的渠道.  
**AndroidManifest.xml:**  

```
<meta-data
	android:name="UMENG_CHANNEL"
	android:value="${VALUE}" />
```

**build.gradle:**  

```
android {
    compileSdkVersion 21
    buildToolsVersion "21.0.1"
   	sourceSets {
        main {
            jniLibs.srcDirs = ['libs']
        }
    }
    #以下就是需要修改的地方
    defaultConfig {
        manifestPlaceholders = [VALUE: "1"]
    }
    productFlavors {
        c360 {
            signingConfig signingConfigs.releaseConfig
            manifestPlaceholders = [VALUE: "4"]
        }
        tencent {
            signingConfig signingConfigs.releaseConfig
            manifestPlaceholders = [VALUE: "5"]
        }
        xiaomi {
            signingConfig signingConfigs.releaseConfig
            manifestPlaceholders = [VALUE: "6"]
        }
        baidu {
            signingConfig signingConfigs.releaseConfig
            manifestPlaceholders = [VALUE: "7"]
        }
    }
}
```

productFlavors位置的代码就是,改变不同渠道的渠道号.

##### 问题4:
另外一个是混淆的问题,混淆就是和eclipse一样,修改proguard-rules.pro这个文件即可.具体需要通过一篇完整的来详述