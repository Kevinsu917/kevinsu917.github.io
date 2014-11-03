---
layout: post
title: 语音播放之普通模式和听筒模式
description: “AudioPlay"

tags: ["Android,IAround"]
comments: true
---  

###语音播放之普通模式和听筒模式,含距离传感器[转载]

**最近为了实现微信的语音播放听筒模式,上网查阅了一些资料,看到一篇写得很完整的文章,特意转载一下**

***

####一、利用距离感应器监听听筒靠近耳朵事件  
准确的说距离感应器并不能监听到你是否把手机靠近耳朵，也许是你用手挡在了距离感应器前面，但这不是我们关心的，我们关心的是当你的耳朵靠近听筒时，我们要捕获到这个事件

**step 1，新建实现SensorEventListener接口的类并实现onSensorChanged(SensorEvent event)方法**    
>
public class MainActivity extends Activity implements SensorEventListener

 
**step 2，得到距离感应器的实例**      
>
audioManager = (AudioManager) this
                .getSystemService(Context.AUDIO_SERVICE);        
        mSensorManager = (SensorManager) getSystemService
(Context.SENSOR_SERVICE);     
        mSensor = mSensorManager.getDefaultSensor(Sensor.TYPE_PROXIMITY);      


**step 3，在onSensorChanged方法中判断是否靠近听筒**    
>
public void onSensorChanged(SensorEvent event) {    
    // TODO Auto-generated method stub
    float range = event.values[0];
    if (range == mSensor.getMaximumRange()) {
        Toast.makeText(this, "正常模式", 	Toast.LENGTH_LONG).show();
    } else {
        Toast.makeText(this, "听筒模式", Toast.LENGTH_LONG).show();
    }

***

####二、根据距离感应器改变AudioManager播放模式    
默认情况下，播放语音都是用外置喇叭或者耳机，我们需要编码实现播放的切换。   

**step 1，在AndroidManifest文件中添加权限声明**    

>
<uses-permission android:name="android.permission.MODIFY_AUDIO_SETTINGS"/>


**step 2，切换播放模式**     

>
public void onSensorChanged(SensorEvent event) {     
    // TODO Auto-generated method stub   
    float range = event.values[0];    
    if (range == mSensor.getMaximumRange()) {    
        Toast.makeText(this, "正常模式", Toast.LENGTH_LONG).show();     
         audioManager.setMode(AudioManager.MODE_NORMAL);
    } else {   
        Toast.makeText(this, "听筒模式", Toast.LENGTH_LONG).show();    
         audioManager.setMode(AudioManager.MODE_IN_CALL);    
    }


***

**完整代码**      

package com.markjoker.sensortest;
 
import java.io.FileInputStream;
import java.io.IOException;
 
import android.hardware.Sensor;
import android.hardware.SensorEvent;
import android.hardware.SensorEventListener;
import android.hardware.SensorManager;
import android.media.AudioManager;
import android.media.MediaPlayer;
import android.os.Bundle;
import android.app.Activity;
import android.content.Context;
import android.util.Log;
import android.view.Menu;
import android.widget.Toast;
 
public class MainActivity extends Activity implements SensorEventListener {
    private AudioManager audioManager;
    private SensorManager mSensorManager;
    private Sensor mSensor;
 
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        init();
    }
 
    private void init() {
        audioManager = (AudioManager) this
                .getSystemService(Context.AUDIO_SERVICE);
        mSensorManager = (SensorManager) getSystemService(Context.SENSOR_SERVICE);
        mSensor = mSensorManager.getDefaultSensor(Sensor.TYPE_PROXIMITY);
         
        MediaPlayer mPlayer = new MediaPlayer();
        try {
            mPlayer.reset();
            mPlayer.setDataSource(new FileInputStream("/sdcard/snow.mp3").getFD());
            mPlayer.prepare();
            mPlayer.start();
        } catch (IllegalArgumentException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        } catch (SecurityException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        } catch (IllegalStateException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        } catch (IOException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }
 
    @Override
    protected void onResume() {
        mSensorManager.registerListener(this, mSensor,
                SensorManager.SENSOR_DELAY_NORMAL);
        super.onResume();
    }
 
    @Override
    protected void onPause() {
        // TODO Auto-generated method stub
        mSensorManager.unregisterListener(this);
        super.onPause();
    }
 
    @Override
    public void onSensorChanged(SensorEvent event) {
        // TODO Auto-generated method stub
        float range = event.values[0];
 
        if (range == mSensor.getMaximumRange()) {
            Toast.makeText(this, "正常模式", Toast.LENGTH_LONG).show();
             audioManager.setMode(AudioManager.MODE_NORMAL);
        } else {
            Toast.makeText(this, "听筒模式", Toast.LENGTH_LONG).show();
             audioManager.setMode(AudioManager.MODE_IN_CALL);
        }
    }
 
    @Override
    public void onAccuracyChanged(Sensor sensor, int accuracy) {
        // TODO Auto-generated method stub
 
    }
 
}

[原文链接](http://www.2cto.com/kf/201312/263642.html)