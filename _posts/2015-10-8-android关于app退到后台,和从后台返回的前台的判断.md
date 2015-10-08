---
layout: post
title: app退到后台,和从后台返回的前台的判断
description: "Android"

tags: ["Android"]
comments: true
---

### Android关于app退到后台,和从后台返回的前台的判断

可以通过获取正在运行中的任务列表的第一个(就是处于前台的任务),与app的packagename来判断是否相同,相同则认为当前app处于前台,否则认为处于后台.

```
/**
     * 判断是否处于后台的状态
     * @return
     */
    private boolean isApplicationBroughtToBackground() {
        ActivityManager am = (ActivityManager) getSystemService(Context.ACTIVITY_SERVICE);
        List<ActivityManager.RunningTaskInfo> tasks = am.getRunningTasks(1);
        if (!tasks.isEmpty()) {
            ComponentName topActivity = tasks.get(0).topActivity;
            if (!topActivity.getPackageName().equals(getPackageName())) {
                return true;
            }
        }
        return false;
    }
```

根据isBackground标志,来判断是否从后台返回

```
public void onResume() {
        super.onResume();
        if(BaseApplication.isBackground){
            Logger.e(TAG, "从界面返回");
            BaseApplication.isBackground = false;
        }
    }
 ```
 
判断是否进入后台
 
 ```
 @Override
    protected void onStop() {
        super.onStop();
        if(isApplicationBroughtToBackground()){
            BaseApplication.isBackground = true;
        }
    }
 ```
 
 