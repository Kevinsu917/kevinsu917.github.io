---
layout: post
title: Android PhoneInfo
description: "Android PhoneInfo"

tags: ["Android"]
comments: true
---

###设备信息
我们经常需要获取设备的某些信息，例如手机的制式是GSM、WCDMA还是CDMA；或者手机的分辨率是不是1280 * 720；又或者当前手机用的网络是否wifi之类的。

Google提供了android.telephony这个包，主要用来获取设备的硬件信息。其中有很多类，只挑常用到的说，其他的用到再去查API。[Telephony](http://developer.android.com/reference/android/telephony/package-summary.html)

最常用到的一个类[**TelephonyManager**](http://developer.android.com/reference/android/telephony/TelephonyManager.html)


####设备基础信息
```java
	public void getPhoneInfo()
	{
		TelephonyManager manager = ( TelephonyManager ) mContext.getSystemService( Context.TELEPHONY_SERVICE );
		String countryISO = manager.getSimCountryIso( );//SIM卡所处城市
		String deviceID = manager.getDeviceId( );//设备唯一标识ID，也就是IMEI码，或者CDMA手机的ESN或 MEID码
		String lineNumber = manager.getLine1Number( );//设备的电话号码
		String networkCon = manager.getNetworkCountryIso( );//网络所处城市
		int networkType = manager.getNetworkType( );//网络的类型，GPRS、EDGE、LTE
		int phoneType = manager.getPhoneType( );//返回手机用于传输语言的无线类型。比如GSM，CDMA
		String simSNumber = manager.getSimSerialNumber( );//SIM卡序列号
		int simState = manager.getSimState( );//SIM卡的状态
		String subscriberID = manager.getSubscriberId( );//例如返回IMSI国际移动用户识别码，可以判断哪国的运营商
		boolean isRoaming = manager.isNetworkRoaming( );//是否处于漫游
		
		
		String androidVSStr = Build.VERSION.RELEASE;//发布固件的版本，如：4.2.2；2.3.6
		int sdkCode = Build.VERSION.SDK_INT;//SDK版本如：11对应的就是HONEYCOMB（android 3.0）
		String phoneVesion = Build.MODEL;//手机型号
		String brand =android.os.Build.BRAND;//手机品牌
		String device = android.os.Build.DEVICE;//固件的版本代号
		
		String language = Locale.getDefault( ).getLanguage( );//手机当前设置语言
		String country = Locale.getDefault( ).getCountry( );//手机设置的语言所在的城市
		
		
		String phoneManufacturer = android.os.Build.MANUFACTURER;//手机生产商
		String opreatorName = manager.getNetworkOperatorName( );//手机运营商
		
	}
```

---

####设备屏幕信息
关于获取屏幕的分辨率，有以下两种方法：
在这里，我们顺便梳理一下Android的长度单位。dp、sp、px、in、pt、mm。用得比较多的也就前面3种
1. px(pixels，像素)：例如屏幕的分辨率为320*480。密度大了，那么单位面积上的像素点就会更多。那么屏幕大小相同下，分辨率越高的屏幕，你设置的一个12px的看起来就会更小     
2. dp、dip(dip-Density Independent Pixel,设备独立像素)，一般xml中的长度都用dp。    
3. 这里重点说一下，dp到底与屏幕的密度是否有关系？答案是有关系的。Adroid开发者文档中已经明确说明【An abstract unit that is based on the physical density of the screen】      
4. sp(Scale-independent Pixels),这个和dp一样。通常用于字体的设置，会受用户的字体大小影响。也就是说，即会受到屏幕密度的影响，又会受到用户的设置影响。          
5. pt(Points 磅) 1pt＝1/72英寸            
6. mm(Millimeters 毫米)
7. in(Inches 英尺)    

**Note:dp和px转换：pxValue = (int)(dpVaule * scale + 0.5f)   **       

```java
	private void getScreenPixel()
	{
		Display dis = ((Activity)mContext).getWindowManager().getDefaultDisplay();
		int screenWidth = dis.getWidth(); //分辨率:宽(单位：像素 768px)
		int screenHeight = dis.getHeight( ); //分辨率：高(单位：像素 1280px)
	}
```

```java
	private void getScreenInfo()
	{
		DisplayMetrics dm = new DisplayMetrics( );
		dm = mContext.getResources( ).getDisplayMetrics( );
		
		//屏幕显示的逻辑密度。设备独立像素(DIP-Density Independent Pixel Unit)。
		//Android规定160dpi（240*320， 1.5"*2"）为基准，那么120dpi的屏幕，density为0.75;
		//而屏幕dpi(Dots per inch)，每英寸有几个点。但是有长和宽怎么算？最常用的方法就是计算对角线上的dpi
		//160dpi = sqrt(240^2 + 320^2) / sqrt(1.5^2 + 2^2)
		float density = dm.density;//以160dpi为基准，Nexus4的density为2
		int densityDPI = dm.densityDpi;//320dpi
		float xdpi = dm.xdpi;//宽的分辨率/宽的英寸
		float ydpi = dm.ydpi;//高的分辨率/高的英寸
		int dmScreenWidth = dm.widthPixels;//分辨率:宽(单位：像素 768px)
		int dmScreenHeight = dm.heightPixels;//分辨率：高(单位：像素 1280px)
	}
```

关于长度单位的[Android官方解释](http://developer.android.com/guide/topics/resources/more-resources.html#Dimension)：
![Alt text]({{ site.url }}/images/phoneInfo/dimension.png)


---

####关于WIFI的信息
主要用到了[**WifiManager**](http://developer.android.com/reference/android/net/wifi/WifiManager.html)这个类:

```java
	public void getWifiInfo()
	{
		WifiManager wifi = ( WifiManager ) mContext.getSystemService( Context.WIFI_SERVICE );
		
		WifiInfo info = wifi.getConnectionInfo( );
		int ipAddress = info.getIpAddress( );//ip地址
		String macAddress = info.getMacAddress( );//mac地址
		String SSID = info.getSSID( );//wifi名字
	}
```

暂时发现常用到的就那么多。如果以后发现有更多需要用到的，到时再补充。