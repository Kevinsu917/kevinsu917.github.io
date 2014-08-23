---
layout: post
title: Android MediaPlayer
description: "Android MediaPlayer"

tags: ["Android"]
comments: true
---

###MediaPlayer
关于音频、视频播放，是Andorid应用中常见的功能。音频只需要设置源即可播放，视频还需要View来播放。
**MediaPlayer**类是用来播放音频/视频文件和流的。
**状态图**：
![Alt text]({{ site.url }}/images/mediaplayer/1408796523668.png)
为了能够正常的播放，必须搞清楚各种状态。不然就会抛各种的异常。
图中，
* 椭圆表示MediaPlayer对象的状态。
* 线条代表状态的转换操作。带单箭头的表示同步方法调用的；两个箭头的表示异步方法调用。
通过图，我们看到一个MediaPlayer对象有以下几种状态：
1. Idle 初始状态
2. Initialized 已经初始化状态
3. Preparing 准备中状态
3. Prepared 已经准备好状态
4. Started 已经开始播放状态
5. Paused 已经暂停状态——就像我们mp3的暂停，可以通过开始，**接着播放**
6. Stopped 已经停止状态——就像我们mp3的停止，可以通过开始，**重头播放**
7. PlaybackCompleted 已经播放完成状态
8. End 结束状态
9. Error 出错状态

在MediaPlayer刚刚通过new或者reset()后，它处于Idle状态；而在release()后，它处于End状态。在这两个状态之间，就是MediaPlayer的生命周期。
* 上面我们说到Idle的状态可以通过**新构建new**或者**重置reset()**得到，但是，两者还是有细微的差别。
处于Idle状态下，调用以下方法都会出错：
    * getCurrentPosition()
    * getDuration()
    * getVideoHeight()
    * getVideoWidth()
    * setAudioStreamType(int)
    * setLooping(boolean)
    * setVolume(float, float)
    * pause()
    * start()
    * stop()
    * seekTo(int)
    * prepare()
    * prepareAsync()
如果这些方法在MediaPlayer刚刚被构建的时候调用，系统不会去调用OnErrorListener.OnError()回调方法，同时，Idle状态不会被改变。
而如果是在reset()后调用，就会回调到OnErrorListener.OnError()方法，而且状态会改为Error.

* MediaPlayer有一个重要的原则，当不需要使用MediaPlayer时候，应当马上调用release()方法释放资源。这个时候状态会变成End，而且再也不能到达其他的状态。

* 通过create()方法得到MediaPlayer不是处于Idle状态，而是Prepared状态。

通常，很多播放的操作都会因为各种原因而失败。例如播放不支持的格式源、分辨率过高、获取源超时等等。通过注册错误监听器setOnErrorLinstener()可以很好的反馈告诉我们为什么播放失败，同时做异常处理。通常是在某个状态下调用了错误的方法导致的。当MediaPlayer处于Error状态的情况下，可以通过reset方法重置成Idle状态。

---

要播放一个资源，必须处于Prepared状态下，否者会抛异常。
* 有两个方法可以转变成这个状态。（同步)prepare()方法；（异步）prepareAsync()方法。成功后，都会调用OnPrepared()方法（OnPreparedListener接口）。
* Preparing状态是一个临时的状态，在MediaPlayer中并没有定义。

---

播放中的情况下，可以调用isPlaying()方法来判断MediaPlayer对象时候处于started状态。
onBufferingUpdate()方法可以跟踪播放的轨迹。
处于stared状态下调用start()方法是无意义的。

---

pause()方法可以stated状态转变成Paused状态，但是这个方法是异步执行的，所以状态不会马上变化。
stop()方法可以从Started, Paused, Prepared or PlaybackCompleted 转变成Stoped状态，一旦处于Stoped状态，就必须通过prepare()或者prepareAsync()方法把状态设置成Prepared状态才能播放。

---
想要调整播放的位置，可以通过seekTo(int)方法。这是一个异步方法。所以你调整完，需要一段时间才能完成。可以注册OnSeekComplete接口。等待回调。 Prepared, Paused and PlaybackCompleted这些状态下都可以调用。
###嵌套的类
![Alt text]({{ site.url }}/images/mediaplayer/1408800704728.png)


####工程中的一个语音播放工具类
```
/**
 * @ClassName AudioPlayUtil.java
 * @author KevinSu kevinsu917@126.com
 * @version 创建时间：2014-8-10 下午5:19:50
 * @Description: 语音播放工具类，如果文件不存在会去下载
 */

public class AudioPlayUtil implements OnCompletionListener, OnPreparedListener, DownloadFileCallback, OnErrorListener
{
	//记得release
	private static MediaPlayer mediaPlayer;
	private static AudioPlayUtil instance;
	
	//存放当前播放的Url
	private String currentPlayUrl;
	//文件下载后存放的位置,文件名为Url的md5
	private final String downloadPath = PathUtil.getAudioDir( );
	
	//音频播放的回调
	private AudioPlayStateCallback audioCallback;
	//文件下载的回调
	private DownloadFileCallback downloadCallback;
	
	private AudioPlayUtil( )
	{
		// TODO Auto-generated constructor stub
	}
	
	public static AudioPlayUtil getInstance()
	{
		if(instance == null)
		{
			instance = new AudioPlayUtil( );
		}
		return instance;
	}
	
	
	public void playAudio(Context mContext, String url)
	{
		playAudio(mContext, url , null );
	}
	
	public void playAudio(Context mContext, String url, AudioPlayStateCallback playCallback)
	{
		playAudio(mContext, url , playCallback, null );
	}
	
	/**
	 * 播放音频的接口
	 * @param mContext
	 * @param url 文件的url
	 * @param callback 播放的回调
	 * @param downloadCallback 下载的回调
	 */
	public void playAudio(Context mContext, String url, AudioPlayStateCallback callback, DownloadFileCallback downloadCallback)
	{
		if(mediaPlayer == null)
		{
			mediaPlayer = new MediaPlayer( );
			mediaPlayer.setOnCompletionListener( this );
			mediaPlayer.setOnPreparedListener( this );
		}
		

		if(mediaPlayer.isPlaying( ))
		{
			mediaPlayer.stop( );
			mediaPlayer.reset( );
			if(downloadCallback != null)
			{
				downloadCallback.onDownloadFileFinish( 1 , "" , "" );
			}
			if(this.audioCallback != null)
			{
				this.audioCallback.onPlayCompleted( );
			}
			
			
			if(url == currentPlayUrl)
			{

			}else
			{
				currentPlayUrl = url;
				String fileUrl = url;
				this.audioCallback = callback;
				this.downloadCallback = downloadCallback;
				
				if(url.contains( PathUtil.getHTTPPrefix( ) ))
				{
					fileUrl = downloadPath + CryptoUtil.md5( url ) + PathUtil.getMP3Postfix( );
				}
				
				File audioFile = new File( fileUrl );
				if(audioFile.exists( ))
				{
					initMediaPlayer(audioFile.getAbsolutePath( ));
				}else
				{
					//下载
					new DownLoadAuidoThread( mContext, url , this ).start( );
				}
			}
			
		}else
		{
			mediaPlayer.reset( );
			if(downloadCallback != null)
			{
				downloadCallback.onDownloadFileFinish( 1 , "" , "" );
			}
			
			String fileUrl = url;
			currentPlayUrl = url;
			this.audioCallback = callback;
			this.downloadCallback = downloadCallback;
			
			if(url.contains( PathUtil.getHTTPPrefix( ) ))
			{
				fileUrl = downloadPath + CryptoUtil.md5( url ) + PathUtil.getMP3Postfix( );
			}
			
			File audioFile = new File( fileUrl );
			if(audioFile.exists( ))
			{
				initMediaPlayer(fileUrl);
			}else
			{
				//下载
				new DownLoadAuidoThread( mContext, url , this ).start( );
			}
		}

	}
	
	
	private void initMediaPlayer(String url)
	{
		try
		{
			mediaPlayer.setDataSource( url );
			mediaPlayer.prepareAsync( );
		}catch (Exception e) {
			// TODO: handle exception
			if(audioCallback != null)
			{
				audioCallback.OnPlayError( );
				resetConfig();
			}
		}
		
	}
	
	@Override
	public void onPrepared( MediaPlayer mp )
	{
		// TODO Auto-generated method stub
		mp.start( ); 
		if(audioCallback != null)
		{
			audioCallback.OnPlayStarted( );
		}
	}
	
	@Override
	public void onCompletion( MediaPlayer mp )
	{
		// TODO Auto-generated method stub
		if(audioCallback != null)
		{
			audioCallback.onPlayCompleted( );
		}
	}

	private void resetConfig()
	{
		
		audioCallback = null;
		downloadCallback = null;
		if(mediaPlayer!=null)
		{
			mediaPlayer.reset( );
		}
	}

	public void releaseRes()
	{
		if(mediaPlayer!=null)
		{
			mediaPlayer.release( );
			mediaPlayer = null;
		}
	}
	
	@Override
	public void onDownloadFileProgress( long lenghtOfFile , long LengthOfDownloaded , int flag )
	{
		// TODO Auto-generated method stub
		if(downloadCallback != null && flag == currentPlayUrl.hashCode( ))
		{
			downloadCallback.onDownloadFileProgress( lenghtOfFile , LengthOfDownloaded , flag );
		}
	}

	@Override
	public void onDownloadFileFinish( int flag , String fileName , String savePath )
	{
		// TODO Auto-generated method stub
		
		String completedFileName = "";
		if(fileName.contains( PathUtil.getTMPPostfix( ) ))
		{
			int endIndex = fileName.lastIndexOf( PathUtil.getTMPPostfix( ) );
			completedFileName = ( String ) fileName.subSequence( 0 , endIndex ) + PathUtil.getMP3Postfix( );
			File tmpFile = new File( savePath + fileName );
			final File completedFile = new File( savePath + completedFileName );
			try
			{
				completedFile.createNewFile( );
			}
			catch ( IOException e )
			{
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
			if(tmpFile.exists( ))
			{
				boolean isSuccess = tmpFile.renameTo( completedFile );
			}
			
			if(downloadCallback != null && flag == currentPlayUrl.hashCode( ))
			{
				if(completedFile.exists( ))
				{
					initMediaPlayer( completedFile.getAbsolutePath( ) );
				}
				
				downloadCallback.onDownloadFileFinish( flag , completedFileName , savePath );
			}
		}
	}

	@Override
	public void onDownloadFileError( int flag , String fileName , String savePath )
	{
		// TODO Auto-generated method stub
		if(downloadCallback != null)
		{
			downloadCallback.onDownloadFileError( flag , fileName , savePath );
		}
		
	}
	
	//音频播放的回调
	public interface AudioPlayStateCallback
	{
		/**开始播放 */
		public void OnPlayStarted();
		
		/**正在播放 */
		public void OnPlayingProgress();
		
		/**正在播放完毕*/
		public void onPlayCompleted();
		
		/**正在播放失败 */
		public void OnPlayError();
	}
	
	//Audio下载线程
	class DownLoadAuidoThread extends Thread
	{
		
		private Context context;
		private DownloadFileCallback callback;
		private String fileUrl;
		
		public DownLoadAuidoThread( Context context , String url , DownloadFileCallback callback )
		{
			// TODO Auto-generated constructor stub
			this.context = context;
			this.callback = callback;
			fileUrl = url;
		}
		
		@Override
		public void run( )
		{
			// TODO Auto-generated method stub
			String fileName = CryptoUtil.md5( fileUrl );
			//临时文件名
			String fileNameTemp = fileName + PathUtil.getTMPPostfix( );
			try
			{ 
				FileDownloadManager manager = new FileDownloadManager( context , callback ,
						fileUrl , fileNameTemp , downloadPath , fileUrl.hashCode( ) );
				manager.run( );
			}
			catch ( ConnectionException e )
			{
				// TODO Auto-generated catch block
				e.printStackTrace( );
			}
			super.run( );
		}
	}

	@Override
	public boolean onError( MediaPlayer mp , int what , int extra )
	{
		// TODO Auto-generated method stub
		return false;
	}
}
```

