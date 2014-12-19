---
layout: post
title: Tasks and Back Stack  
description: Tasks and Back Stack  

tags: ["Android"]
comments: true
---
####Tasks and Back Stack   
1.i can keep two activities in the same task even from different applications  
2.all the activities will be arranged in a stack, include new activity,the different is taking the focus or not.  
3.A task is a cohesive unit  
4.you can user manifest attributes and intent flags to interrupt the order of the activities in task
****
#####launchMode:
1.standard: activity can be instantiated multiple times  
2.singleTop: A-B-C-D D create a new D will call   onNewIntent() ,but not become A-B-C-D-D, if create a new B no mater B has singleTop flag,will become A-B-C-D-B   
3.singleTask:just one instance of activity in a task, A-B-C-D,D create a new B in the task will become A-B in the task.还有一点比较重要的是,当其它task包含你要打开的带有singleTask标志的activity时候,会把那个task的所有activity叠加到当前task上去.     
![Alt text]({{ site.url }}/images/Task&BackStack/1.png)  
4.singleInstance: like singleTask,but the activity which has singleInstance flag will be only one in the task.if B is the singleInstance activity then A create B, B create C, C create D, the task will become: task1: A-C-D,task2: B, it will create two task   


**Using Intent Flag to modify the default association of an activity to its task**  
1.FLAG_ACTIVITY_NEW_TASK: same as singleTask launchMode   
2.FLAG_ACTIVITY_SINGLE_TOP:same as singleTop launchMode  
3.FLAG_ACTIVITY_CLEAR_TOP:it is easy to know, when start an activity with FLAG_ACTIVITY_CLEAR_TOP,it will clear top of the activity.like this: A-B-C-D, D create B,and stack will become A-B, C and D will be remove(destoried) 

**Note: **   
![Alt text]({{ site.url }}/images/Task&BackStack/2.png)
4.task 通过affinity来区分





