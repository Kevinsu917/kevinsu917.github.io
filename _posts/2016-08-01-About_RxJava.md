---
layout: post
title: About Rxjava
description: Rxjava

tags: ["Android"]
comments: true
---


##定义:
以**类观察者模式**实现链式编程的一种技术.  
Rxjava最基本的两个概念:Observables(被观察者, 事件源)和Subscribes(观察者, 订阅者).流程由Observables发出一系列事件,然后交由给Subscribes来处理.

```java

Observable<Integer> observable = Observable.create(new Observable.OnSubscribe<Integer>() {
	@Override
	public void call(Subscriber<? super Integer> subscriber) {
		subscriber.onNext(1);
		subscriber.onCompleted();
	}
});

Subscriber subscriber = new Subscriber<String>() {
	@Override
	public void onCompleted() {
	}

	@Override
	public void onError(Throwable e) {
	}

	@Override
	public void onNext(String o) {
	}
};

observable.subscribe(subscriber);


```

整个流程可以通过*lambda*和操作符来进行简化.
需要注意的是简化的其中一个方式,就是subscribe,接收1~3个参数的Action1.分别对应Subscriber中的onNext, onError, onCompleted.可以根据自己需要传入对应的参数.


##操作符
其实操作符就是我们所说的方法.  
RxJava中定义了很多操作符,主要是对Observable(被观察者)进行变换.例如事件源本来是int的类型,但是我们需要输出String类型,这类操作.以下是源代码:

```java  

public final Subscription subscribe(final Action1<? super T> onNext, final Action1<Throwable> onError, final Action0 onComplete) {
        if (onNext == null) {
            throw new IllegalArgumentException("onNext can not be null");
        }
        if (onError == null) {
            throw new IllegalArgumentException("onError can not be null");
        }
        if (onComplete == null) {
            throw new IllegalArgumentException("onComplete can not be null");
        }

        return subscribe(new Subscriber<T>() {

            @Override
            public final void onCompleted() {
                onComplete.call();
            }

            @Override
            public final void onError(Throwable e) {
                onError.call(e);
            }

            @Override
            public final void onNext(T args) {
                onNext.call(args);
            }

        });
    }

```

*其实操作符的根本都是更改create方法中OnSubscribe中的call方法*  


####1. 创建一个新的Observable  

* *create*: 通过实现OnSubscribe接口来实现  
* *defer*: 延迟创建Observable, 只有在subscriber的时候才创建  
* *empty/never/throw*: empty不发出一个数据,但是会执行onComplete;never不发出一个数据,啥也不执行;error直接执行onError方法.主要是用来测试  
* *from*: 把普通的数据结构转成Observable  
* *just*: 把一个或者一组普通的数据结构转成Observable  
* *range*: 生成一个范围的integer对应的Observable  
* *repeat*: 重复一个Observable  
* *timer*: 延迟一段时间,发射出一个Long型的0的Observable  

总体来说,简单的创建Observable还是非常简单的.但是要根据实际情况选择哪种创建方式,就需要对每个操作符更加熟悉,才能做到顺手拈来


####2. 变换Observable,在实际情况有可能需要的Observable的类型是不同的,这个时候可以通过变换的操作符来处理

* *buffer*: 把多个数据源分成一定数量的array  
* *window*: 与**buffer**类似,只是分成的是Observable对象  
* *map*: 把一个普通的数据源转成另外一个数据源  
* *flatMap*: 把一个普通的数据源转成Observable  
* *groupBy*: 把多个数据源按照条件分组,通过GroupedObservable可以返回key  
* *scan*: 遍历Observable产生的结果,把结果作为下次参数


####3. 过滤功能.假如我们在数据源中,发现不符合条件的数据,可以直接通过过滤类型的操作符直接剔除

* *debounce*: 防止抖动,在持续规定的时间范围内,没有别的数据加入,就把最后一个数据发送出去  
* *distinct*: 过滤重复的数据  
* *elementAt*: 过滤掉第n个数据源  
* *filter*: 过滤掉不符合条件的数据源  
* *first*: 判断第一个符合条件的数据源才能发送  
* *ignoreElements*: 忽略所有的数据,但是会执行onComplete和onError方法  
* *last*: 和first相反,判断最后一个符合条件的数据源  
* *sample*: 在时间点的取样  
* *skip*: 跳过开头的n个数据源  
* *skipLast*: 跳过结尾的n个数据源  
* *take*: 和skip相反,只去开头n个数据源  
* *takeLast*: 和skipLast相反,只去结尾n个数据源  

####4. combine功能, 结合多个数据源

* *combineLatest*: 两个Observable每次发出数据,找另外一个Observable最近发出的数据,结合在一起  
* *join*: 和combineLatest一样,但是可以控制两个Observable的生命周期  
* *merge*: 按照两个Observable发出数据的时间顺序,组成一个新的Observable  
* *startWith*: 在Observable的开头加入数据  
* *switchNext*: 这个有点复杂,需要理解Observable中的数据是多个Observable,而每一个子Observable也有多个数据源,每当下一个父Observable发射出去,上一个子Observable没有完成的话,就停止继续发送.由新的子Observable继续发送  
* *testZip*: 把两个Observable发出的数据源一一按照Func的条件结合输出.输出的数量按照两个Observable中少的处理  

####5. 异常处理

**catch**  
* *onErrorReturn*: 遇到Error后,返回一个数据源  
* *onErrorResumeNext*: 遇到Error后,返回一个新的Observable  
* *onExceptionResumeNext*: 和onErrorResumeNext一样, 但是只有遇到Exception才处理  
* *retry*: 遇到异常情况可以不断重复,也可以设置重复的次数,还可以设置重复的条件,例如遇到参数异常才retry  
* *retryWhen*: 遇到Error的情况,把Error包装成Observable,如果这个新的Observable可以正常运行,那么运行完重新订阅原来的Observable,继续运行,如果新的Observable不能正常运行,则运行OnError  

####6. 实用操作

* *delay*: 延迟多久执行  
**do系列:**
生命周期: **doOnSubscribe**->**doOnRequest**->([**doOnNext**->onNext->**doOnEach**]循环)->**doOnComplete**->**doOnTerminate**->onCompleted-**doOnUnsubscribe**->finallyDo  
* *meterizlize*: meterizlize把onNext/onError/onComplete都包装成一个Notification来发出  
* *deMaterialize*: 就是相反的过程,把Observable<Notification<?>> 转成 Observable<?>的过程  
* *observeOn*: 指定observeOn以后的操作的运行线程  
* *subscribeOn*: 指定一开始执行的线程.  
* *Subscribe*: 订阅   
* *timeInterval*: 把两次数据发射的时间间隔作为新的数据输出  
* *timeStamp*: 把每次的数据发射时间戳作为新的数据输出  
* *timeOut*: 如果在制定间隔内没有数据发出,也没有执行onComplete,那么就会执行onError  
* *using*:  Using操作符创建一个在Observable生命周期内存活的资源，也可以这样理解：我们创建一个资源并使用它，用一个Observable来限制这个资源的使用时间，当这个Observable终止的时候，这个资源就会被销毁。

####6. 条件操作

* *all*: 判断所有数据源是否满足条件   
* *amb*: 判断多个Observable,哪个先发射,就取哪个的数据  
* *contain*: 判断Observable中是否包含某个数据  
* *DefaultIfEmpty*: 如果某个Observable为空的,就给他默认的值  
* *SequenceEqual*: 判断两个Observable发射的数据顺序是否一致  
* *SkipUntil*: 跳过数据,直到第二个Observable发出第一个数据开始发送数据  
* *SkipWhile*: 跳过数据,当满足条件的时候  
* *TakeUntil*: 使用数据,直到第二个Observable发出第一个数据开始发送数据结束  
* *TakeWhile*: 使用数据,当满足条件的时候  


##Single:
Single其实就是类似于Observable.  

* 只是Observable可以发射出一系列的数据,但是Single只能发出一个数据.  
* Observable需要三个方法来处理(onNext, onError, onCompleted),而Single只需要两个(onSuccess, onError)  
* 另外就是Single的操作符返回的基本上是Single对象...除了个别通过两个Single组合成Observable的方法


##Subject:
Subject既可以看做是Observable,也可以看做是Observer.   

* Subject有onNext,onError,onComplete方法,就想Observer一样  
* Subject又有create,just...等Observable方法  

所以subject是一个神奇的对象....

```java

    private void testSubject(){

        //AsyncSubject, 只处理最后一个数据,和OnComplete
        //输出
        //Rx--onNext:d
        //Rx--onCompleted
        AsyncSubject asyncSubject = AsyncSubject.create();
        asyncSubject.onNext("a");
        asyncSubject.onNext("b");
        asyncSubject.subscribe(new Subscriber<String>() {
            @Override
            public void onCompleted() {
                rxLog("onCompleted");
            }

            @Override
            public void onError(Throwable e) {
                rxLog("onError");
            }

            @Override
            public void onNext(String aLong) {
                rxLog("onNext:" + aLong);
            }
        });
        asyncSubject.onNext("c");
        asyncSubject.onNext("d");
        asyncSubject.onCompleted();


        //BehaviorSubject,在这个例子中,数据有可能丢失,因为在还没subscribe就已经把数据发送出去了
        //如果BehaviorSubject因为一个错误而终止,就不会再发射其它数据
        //输出
        //Rx--onNext:3
        //Rx--onNext:4
        //Rx--onNext:5
        //Rx--onCompleted
        BehaviorSubject behaviorSubject = BehaviorSubject.create("1");
        behaviorSubject.onNext("2");
        behaviorSubject.onNext("3");
        behaviorSubject.subscribe(new Subscriber<String>() {
            @Override
            public void onCompleted() {
                rxLog("onCompleted");
            }

            @Override
            public void onError(Throwable e) {
                rxLog("onError");
            }

            @Override
            public void onNext(String aLong) {
                rxLog("onNext:" + aLong);
            }
        });
        behaviorSubject.onNext("4");
//        behaviorSubject.onError(new Throwable("haha"));
        behaviorSubject.onNext("5");
        behaviorSubject.onCompleted();
        behaviorSubject.onNext("6");

        //不管Observer何时订阅ReplaySubject，ReplaySubject会向所有Observer释放Observable释放过的
        //数据。 但是可以限制数量
        //输出
        //Rx--onNext:#
        //Rx--onNext:$
        //Rx--onCompleted
        ReplaySubject replaySubject = ReplaySubject.createWithSize(2);
        replaySubject.onNext("!");
        replaySubject.onNext("@");
        replaySubject.onNext("#");
        replaySubject.onNext("$");
        replaySubject.onCompleted();
        replaySubject.subscribe(new Subscriber<String>() {
            @Override
            public void onCompleted() {
                rxLog("onCompleted");
            }

            @Override
            public void onError(Throwable e) {
                rxLog("onError");
            }

            @Override
            public void onNext(String aLong) {
                rxLog("onNext:" + aLong);
            }
        });

        //只有在subscribe后,onNext的数据才能收到
        //输出
        //Rx--onNext:T
        //Rx--onNext:Y
        //Rx--onCompleted
        PublishSubject publishSubject = PublishSubject.create();
        publishSubject.onNext("Q");
        publishSubject.onNext("W");
        publishSubject.onNext("E");
        publishSubject.onNext("R");
        publishSubject.subscribe(new Subscriber<String>() {
            @Override
            public void onCompleted() {
                rxLog("onCompleted");
            }

            @Override
            public void onError(Throwable e) {
                rxLog("onError");
            }

            @Override
            public void onNext(String aLong) {
                rxLog("onNext:" + aLong);
            }
        });
        publishSubject.onNext("T");
        publishSubject.onNext("Y");
        publishSubject.onCompleted();
    }

```

##Scheduler
主要是控制操作所在的线程..通过subscribeOn和observerOn方法来控制.

* Schedulers.io()
* Schedulers.computation()
* Schedulers.newThread()
* Schedulers.from(Executor executor)
* Schedulers.test()
* Schedulers.trampoline()
* Schedulers.shutdown()
* AndroidSchedulers.mainThread()


