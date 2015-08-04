---
layout: post
title: 关于ImageLoader
description: ImageLoader

tags: ["Android"]
comments: true
---

### 关于ImageLoader

**universal-image-loader**是最常用的网络图片加载的开源框架.  
当然volley中也又ImageLoader类,但是它的强项仅仅在于对网络图片的加载,还需要自己做L2的缓存,已经对于多样化的图片显示不是特别的方便(圆角,圆形,模糊显示).

***

**简介:**  

	Gradle dependency:
	compile 'com.nostra13.universalimageloader:universal-image-loader:1.9.4'

`ImageLoaderConfiguration`

在第一次使用的之前,初始化**ImageLoaderConfiguration**,这是初始化一个ImageLoader的默认配置.例如:

	// DON'T COPY THIS CODE TO YOUR PROJECT! This is just example of ALL options using.
	// See the sample project how to use ImageLoader correctly.
	File cacheDir = StorageUtils.getCacheDirectory(context);
	ImageLoaderConfiguration config = new ImageLoaderConfiguration.Builder(context)
        .memoryCacheExtraOptions(480, 800) // default = device screen dimensions
        .diskCacheExtraOptions(480, 800, null)
        .taskExecutor(...)
        .taskExecutorForCachedImages(...)
        .threadPoolSize(3) // default
        .threadPriority(Thread.NORM_PRIORITY - 2) // default
        .tasksProcessingOrder(QueueProcessingType.FIFO) // default
        .denyCacheImageMultipleSizesInMemory()
        .memoryCache(new LruMemoryCache(2 * 1024 * 1024))
        .memoryCacheSize(2 * 1024 * 1024)
        .memoryCacheSizePercentage(13) // default
        .diskCache(new UnlimitedDiscCache(cacheDir)) // default
        .diskCacheSize(50 * 1024 * 1024)
        .diskCacheFileCount(100)
        .diskCacheFileNameGenerator(new HashCodeFileNameGenerator()) // default
        .imageDownloader(new BaseImageDownloader(context)) // default
        .imageDecoder(new BaseImageDecoder()) // default
        .defaultDisplayImageOptions(DisplayImageOptions.createSimple()) // default
        .writeDebugLogs()
        .build(); 

1. .diskCache(new UnlimitedDiscCache(cacheDir))设置disk缓存的路径
2. .threadPoolSize(3) 设置线程池的线程数量
3. .memoryCache(new LruMemoryCache(2 * 1024 * 1024)) 设置内存缓存的大小

通过自定义设置**ImageLoaderConfiguration**,满足自己的对于ImageLoader的使用.


`DisplayImageOptions`

每一张图片的显示,其实都是一个任务.用ImageLoader.displayImage(...).很多时候,我们对于不同位置的图片,想展示不同的样式.例如A位置我想展示圆角的图片,而B位置却想展示直角的图片.那么这个时候,就需要设置**DisplayImageOptions**这个选项参数.如果不传,就会使用默认的Options,那么那个就是在前面说的**ImageLoaderConfiguration**中设置的.

以下是关于**DisplayImageOptions**的设置:  

	// DON'T COPY THIS CODE TO YOUR PROJECT! This is just example of ALL options using.
	// See the sample project how to use ImageLoader correctly.
	DisplayImageOptions options = new DisplayImageOptions.Builder()
        .showImageOnLoading(R.drawable.ic_stub) // resource or drawable
        .showImageForEmptyUri(R.drawable.ic_empty) // resource or drawable
        .showImageOnFail(R.drawable.ic_error) // resource or drawable
        .resetViewBeforeLoading(false)  // default
        .delayBeforeLoading(1000)
        .cacheInMemory(false) // default
        .cacheOnDisk(false) // default
        .preProcessor(...)
        .postProcessor(...)
        .extraForDownloader(...)
        .considerExifParams(false) // default
        .imageScaleType(ImageScaleType.IN_SAMPLE_POWER_OF_2) // default
        .bitmapConfig(Bitmap.Config.ARGB_8888) // default
        .decodingOptions(...)
        .displayer(new SimpleBitmapDisplayer()) // default
        .handler(new Handler()) // default
        .build();

1. .showImageOnLoading() 设置图片加载中时使用的默认图片
2. .cacheInMemory() 是否缓存到内存中

就是一下基本的方法.如果有需要,我们会在后面详细分析.

`ImageLoader能够加载的图片类型:`  

1. "http://site.com/image.png" // from Web  
2. "file:///mnt/sdcard/image.png" // from SD card  
3. "file:///mnt/sdcard/video.mp4" // from SD card  (video thumbnail)    
4. "content://media/external/images/media/13" // from content provider  
5. "content://media/external/video/media/13" // from content provider (video thumbnail)  
6. "assets://image.png" // from assets  
7. "drawable://" + R.drawable.img // from drawables (non-9patch images)  

**Note:** 作者建议我们在显示drawble://的时候,考虑使用原生的方法,ImageView.setImageResource()来替代.

*** 
**使用:**  

1. 获取ImageLoader的单例

		ImageLoader imageLoader = ImageLoader.getInstance(); // Get singleton instance
		
2. 加载一个图片,对该图片解码成一个Bitmap,然后再ImageView中显示(或者是其它实现了ImageAware接口的View).3. 
	* 可以直接调用displayImage方法,用默认的options  
	
			imageLoader.displayImage(imageUri, imageView);
		
	* 可以定义SimpleImageLoadingListener,ImageLoaderListener或者ImageLoadingProgressListener,在加载的各个状态中回调.

			imageLoader.loadImage(imageUri, new SimpleImageLoadingListener() {
    		@Override
    		public void onLoadingComplete(String imageUri, View view, Bitmap loadedImage) {
        			// Do whatever you want with Bitmap}
        		}
        	);
	* 可以同步下载一个Bitmap图片
	
        	// Load image, decode it to Bitmap and return Bitmap synchronously
			Bitmap bmp = imageLoader.loadImageSync(imageUri);
	* 可以定义下载图片的尺寸,利用ImageSize
		
			// Load image, decode it to Bitmap and return Bitmap to callback
			ImageSize targetSize = new ImageSize(80, 50); // result Bitmap will be fit to this size 
			imageLoader.loadImage(imageUri, targetSize, options, new SimpleImageLoadingListener() {
    			@Override
    			public void onLoadingComplete(String imageUri, View view, Bitmap loadedImage) {
        			// Do whatever you want with Bitmap
    			}
			});
			
			// Load image, decode it to Bitmap and return Bitmap synchronously
			ImageSize targetSize = new ImageSize(80, 50); // result Bitmap will be fit to this size
			Bitmap bmp = imageLoader.loadImageSync(imageUri, targetSize, options);

***

**Useful Info:**  

1. 默认情况下是不缓存的.所以需要通过默认的DisplayImageOptions,并把设置其中的.cacheInMemory和.cacheOnDisk,然后把定义好的option,设置到ImageLoaderConfiguration中.defaultDisplayImageOptions(defaultOptions)方法.
2. 如果使用了ImageLoader出现OutOfMemoryError的话,那么可以通过
	* 关闭memory的cache
	* 减少线程池的大小
	* 用Bitmap.Config.RGB_565代替ARGB_8888
	* 使用.imageScaleType(ImageScaleType.EXACTLY)
	* 使用.diskCacheExtraOption(480, 320, null) 
3. 关于内存缓存的设置,有一些实现好的实现策略:
	* 只有强引用  
		* LruMemoryCache (Least recently used bitmap is deleted when cache size limit is exceeded) - **Used by default**
	* 强引用和弱引用混合用  
		* UsingFreqLimitedMemoryCache (Least frequently used bitmap is deleted when cache size limit is exceeded)
		* LRULimitedMemoryCache (Least recently used bitmap is deleted when cache size limit is exceeded)
		* FIFOLimitedMemoryCache (FIFO rule is used for deletion when cache size limit is exceeded)
		* LargestLimitedMemoryCache (The largest bitmap is deleted when cache size limit is exceeded)
		* LimitedAgeMemoryCache (Decorator. Cached object is deleted when its age exceeds defined value)
	* 只有弱引用  
		* WeakMemoryCache (Unlimited cache)
4. 关于disk缓存的配置,一些已经实现好的策略:  
	* UnlimitedDiscCache (The fastest cache, doesn't limit cache size) - **Used by default**
	* LruDiskCache (Cache limited by total cache size and/or by file count. If cache size exceeds specified limit then least-recently used file will be deleted)
	* LimitedAgeDiscCache (Size-unlimited cache with limited files' lifetime. If age of cached file exceeds defined limit then it will be deleted from cache.)  
**NOTE:** UnlimitedDiscCache 是速度最快的实现方式. 

5. 展示bitmap的时候,有两个已经实现好的方法:  
	* 显示圆角 RoundedBitmapDisplayer (Displays bitmap with rounded corners)  
	* 淡入显示 FadeInBitmapDisplayer (Displays image with "fade in" animation)  
6. 如果不想在list或者grid滑动的过程中,加载图片.可以通过设置PauseOnScrollListener:

		boolean pauseOnScroll = false; // or true
		boolean pauseOnFling = true; // or false
		PauseOnScrollListener listener = new PauseOnScrollListener(imageLoader, pauseOnScroll, pauseOnFling);
		listView.setOnScrollListener(listener);


[参考1](https://github.com/nostra13/Android-Universal-Image-Loader/wiki)  
[参考2](http://blog.csdn.net/xiaanming/article/details/26810303)  
