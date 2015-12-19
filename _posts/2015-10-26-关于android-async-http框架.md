---
layout: post
title: 关于android-async-http框架
description: "android-async-http"

tags: ["Android"]
comments: true
---

**简介:**  
这个框架是基于Apache的HttpClient库开发的一个异步回调的Http Client.所有的请求都不是在UI线程上的.但是所有回调的逻辑都是通过Android Handler来传递,你也可以用于Service或者background thread,这个库将自动识别context.

**特性:**  

* Http请求是非UI线程的
* 通过线程池来请求,节约资源的使用
* 多媒体的文件上传下载,并且没有额外的第三方库(FileAsyncHttpResponseHandler)
* JSON流的上传
* 只有90kb的大小
* 自动重新请求的机制
* cookie的保存
* 支持SAX的解析(SaxAsyncHttpResponseHandler)

**使用:**  
首先初始化一个AsyncHttpClient对象.

```
private static AsyncHttpClient client = new AsyncHttpClient();
```

**AsyncHttpClient**有异步的GET,POST,PUT,DELETE方法,同时还可以加入额外的请求参数(RequestParams).通过实现ResponseHandlerInterface这个接口类,实现onSuccess和onFailure方法来获得异步的结果返回.

实现ResponseHandlerInterface接口的类有:  

1. AsyncHttpResponseHandler 用于拦截和处理响应
2. BaseJsonHttpResponseHandler 用于json请求
3. FileAsyncHttpResponseHandler 文件下载
4. TextHttpResponseHandler 文本类型的Http请求


下载的例子:  

```
AsyncHttpClient client = new AsyncHttpClient();
client.get("https://example.com/file.png", new FileAsyncHttpResponseHandler(/* Context */ this) {
    @Override
    public void onSuccess(int statusCode, Header[] headers, File response) {
        // Do something with the file `response`
    }
});
```

[**官方blog**](https://loopj.com/android-async-http/)  
[**官方文档**](https://loopj.com/android-async-http/doc/com/loopj/android/http/package-summary.html)  
[**参考1**](http://www.cnblogs.com/angeldevil/p/3729808.html)  
[**参考2**](http://blog.csdn.net/jdsjlzx/article/details/44700965)  