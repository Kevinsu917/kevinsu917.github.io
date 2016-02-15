---
layout: post
title: Design Patterns Observer
description: "观察着模式"

tags: ["Design Patterns"]
comments: true
---

### 观察者模式-Observer

再次写起设计模式,主要是因为要学习RxJava,需要对该模式有更深入的理解.其实这个模式在Android中用的很多,像View的点击事件等等.所以看起来也容易.

**描述:**  
观察者模式属于*行为型模式*,观察者模式有两个对象,一个是被观察者一般用主题(Subject)来表示,一个是观察者(Observer).形成一对多的关系,当主题(Subject)发生变化的时候,需要通知所有的观察者,让所有观察者根据自身的需求,发生变化.

****

#### 关于主题的代码:  

```java
import java.util.ArrayList;
import java.util.List;

public class Subject {

   private List<Observer> observers
      = new ArrayList<Observer>();
   private int state;

   public int getState() {
      return state;
   }

   public void setState(int state) {
      this.state = state;
      notifyAllObservers();
   }

   public void attach(Observer observer){
      observers.add(observer);		
   }

   public void notifyAllObservers(){
      for (Observer observer : observers) {
         observer.update();
      }
   } 	
}
```

1. 需要一个Observer的list
2. 需要一个状态变量,用来记录来判断是否改变
3. 两个串联的方法:绑定和解绑
4. 一个广播的方法,遍历list去一个个通知


#### 关于接口类的代码:  

```java
public abstract class Observer {
   protected Subject subject;
   public abstract void update();
}
```

1. 每一个观察者都需要基础该类
2. 通过该类中的update方法去被调用更新


其实实现的方式有很多,JDK提供了**Observer**接口和**Observable**类,用于实现观察者模式.
