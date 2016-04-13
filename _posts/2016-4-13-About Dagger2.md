---
layout: post
title: About Dagger2
description: Dagger2

tags: ["Android"]
comments: true
---

因为要阅读**Philm**工程代码,了解MVP的结构.其中遇到了Dagger的使用,所以迫于阅读困难的压力,先花了一些时间对于Dagger2的阅读.

刚开始确实有点蒙圈,查看网上的资料,还是一头雾水,其实是因为有些概念还不清楚,就去阅读导致的.

首先Dagger2的历史就不说了,和Dagger1的相似,区别什么的都先抛开.直接学习Dagger2.  
Dagger2是一种依赖注入的框架.那么第一个概念就是,什么叫做依赖注入,[我通过阅读一篇文章了解到什么叫做依赖注入](http://codethink.me/2015/08/01/dependency-injection-theory/).
还有文章中提到的[反转控制的概念](http://zhangjunhd.blog.51cto.com/113473/126530/).

这里我简单介绍一下依赖注入.依赖的意思:   

```java   
class A{
   B b;
}
```

通过上面的代码,可以看出来,A中有一个B的对象,那么就说A依赖于B.  
如果A中的B通过在A中去创建(也就是new),这个时候A与B的耦合性就会比通过把B传入A中这种方式强.  

```
为什么呢?     
如果B是一个基类,B1,B2都是继承于B,如果在A中B初始化为B1,这样就把控制权放在了A中,
假如需要把A中的B初始化为B2,这个时候就需要改A的代码.这样不够灵活.但是如果通过传入,
就不需要改动A的代码了.
```

假如我们有一个出行的需求,我们准备出行,但是我们可以有多种出行的交通方式.

```java
定义一个出行的方式接口:
public interface Vehicle{
    void go();
}

public class Car implements Vehicle{
  void go(){
    //我们开车去
  }
}

public class Bike implements Vehicle{
  void go(){
    //我们骑自行车去
  }
}

//这样我们要通过坐车还是骑车去,就看我们传入什么交通工具
public class Me{
  Vehicle vehicle;
  public void create(Vehicle v){
    vehicle = v;
  }

  public void letsGo(){
    v.go();
  }
}
```


所以说依赖注入的最终目的,就是把A对B的依赖,通过传入的方式来实现,而不是在A中通过new的方式创建.所以Dagger2是依赖注入的框架,也就是把依赖的类通过传入的方式实现.

现在大致了解Dagger2是要干嘛.至于如何通过Android Studio去添加其中的使用方式.[参考](http://codethink.me/2015/08/06/dependency-injection-with-dagger-2/)

把上面的例子通过Dagger实现:  

```Java
public class Car implements Vehicle{
  void go(){
    //我们开车去
  }
}
public class Bike implements Vehicle{
  void go(){
    //我们骑自行车去
  }
}

@Module
public class VehicleModule {
    @Provides
    Vehicle providesVehicle() {
        return new Car();
    }
}

@Component(modules = VehicleModule.class)
public interface VehicleComponent {
    Vehicle getVehicle();
}

public class Me{
  @Inject
  Vehicle vehicle;

  public void letsGo(){
    vehicle.go();
  }
}
```
以上就是最简单的Dagger2的例子了.从上面看,我们用到了几个注解.
首先要明确,Dagger2的工作原理,是通过注解生成对应代码(注意一定是有注解,才会有生成的对应的代码),让我们只需要关注我们需要的,让Dagger2帮我们生成一些我们不关注的代码,如果Factory工厂类的代码.   
以下三个
* @Module: 就是依赖的提供,在@Module中的方法带有@Provides注解,该注解用于表示该方法是用于提供的方法.  
* @Component: 是用于连接@Module和@Inject使用的   
* @Inject: 用来表明该对象使用依赖注入生成  

简单描述一下:  
@Component下定义的是一个interface接口,Dagger2会根据该接口生成一个实现了此接口的一个DaggerXXXComponent的类.当然注意@Component(modules = VehicleModule.class)这里的modules是表示与Component想关联的Module   

@Module中的@Provides方法,Dagger2会生成XXModule_ProvidesXXXFactory,里面会有@Module中的方法,供DaggerXXXComponent去使用.  

@Inject方法是就是标明这个类是需要注入生成的,通过DaggerXXXComponent去传入.  

*****

### 代码分析
![XMind]({{ site.url }}/images/dagger.png)

@Component之所以是连接@Module和@Inject的理由,是因为会生成一个DaggerXXComponent,该类通过提供一些列的Provider<T>对象(这些Provider对应@Module中的@Provides)对@Inject注解的对象进行注入,这样就可以对需要注入的对象进行初始化.

### 关于Scope
