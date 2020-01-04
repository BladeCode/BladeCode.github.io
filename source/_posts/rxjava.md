---
title: RxJava 入门
date: 2018-10-02 10:02:00
categories: RxJava
tag: [RxJava]
---

RxJava – Reactive Extensions for the JVM – a library for composing **asynchronous** and **event-based** programs using **observable** sequences for the Java VM.（一个在 Java VM 上使用{% label info@可观测 %}的序列( **观察者模式** )来组成{% label info@异步 %}的、{% label info@基于事件 %}的程序的库）.

在实际开发过程中，RxJava已是一个不可或缺的组件，因此对于RxJava的学习和思考，记录分享是很重要的一个环节  

本系列文章主要：
1. [RxJava 入门](https://incoder.org/2018/10/02/rxjava/)
2. RxJava 实际应用
3. RxJava 源码剖析

<!-- more -->

目前来说，RxJava有两个版本，RxJava1 与 RxJava2 两个版本之间虽然存在很多不同，但它们的本质是相同，由于对于RxJava1 **已废弃**，因此建议没有学习或者是使用过，可直接上手学习RxJava2(在学习过程中部分地方还是会有RxJava1相关的说明，但这不是重点)  

文章使用RxJava版本如下:
* `implementation 'io.reactivex:rxjava:1.3.0'`
* `implementation 'io.reactivex.rxjava2:rxjava:2.2.1'`
* 项目示例：[rc-cluster-network](https://github.com/RootCluster/rc-cluster-network)

>由于一个项目中RxJava1与RxJava2并不能共存，因此实际参考项目中仅RxJava2示例

## RxJava 基础

### RxJava1 VS RxJava2

![rxjava1 vs rxjava2](https://res.cloudinary.com/incoder/image/upload/v1538815280/blog/RxJava1_vs_RxJava2.png)
>以上是列举出不同版本间主要的变换，其它更细节部分，请查看官方[Wiki](https://github.com/ReactiveX/RxJava/wiki/What's-different-in-2.0)

### 关键词

#### RxJava1
* Observable (可观察者，即被观察者)
* Observer (观察者)
* subscribe (订阅)
* 事件

>Observable和Observer通过subscribe()方法实现订阅关系，从而Observable可以在需要的时候发出事件来通知Observer

#### RxJava2
* Observable (可观察者，即被观察者)
* Observer (观察者)
* ObservableEmitter (发射器)
* 事件

>RxJava2中`Subscrber`被`ObservableEmitter`取代，`Observer`中多了一个回调方法 `onSubscribe()`，传递参数为`Disposable`

* ObservableEmitter：Emitter是发射器的意思，这个就是用来发出事件，它可以发出三种类型的事件，通过调用`emitter`的`onNext(T value)`，`onComplete()`和`onError(Throwable e)`就可以分别发出`next`事件，`complete`事件和`error`事件
* Disposable：字面意思是一次性用品，用完即可丢弃。在RxJava中可以理解成两根管道间的阀门，当调用它的的`dispose()`方法时，它就将两根管道切断，从而导致下游收不到事件，即相当于`Subsciption`

### 基本实现

#### Create Observable
![operators](https://raw.githubusercontent.com/wiki/ReactiveX/RxJava/images/rx-operators/legend.png)

##### RxJava1

```java
Observable<String> observable = Observable.unsafeCreate(new Observable.OnSubscribe<String>() {
    @Override
    public void call(Subscriber<? super String> subscriber) {
        subscriber.onNext("Hello");
        subscriber.onNext("World");
        subscriber.onNext("RxJava1");
        subscriber.onCompleted();
    }
});
```
>1.2.7版本后，Observable的`create()`方法已被废弃，如果没有特殊需求，可以使用`unsafeCreate()`代替，构造Obaservable实例

##### RxJava2

```java
Observable<String> observable = Observable.create(new ObservableOnSubscribe<String>() {
    @Override
    public void subscribe(ObservableEmitter<String> emitter) throws Exception {
        emitter.onNext("Hello");
        emitter.onNext("World");
        emitter.onNext("RxJava2");
        emitter.onComplete();
    }
});
```
`unsafeCreate()/create()`方法是RxJava最基本创建时间序列的方法。基于这个方法，RxJava还提供了一些方法来快捷创建事件队列

* just(T...)：将传入的参数依次发送出来
    ```java
    Observable observable = Observable.just("Hello", "World", "RxJava");
    // 将会依次调用：
    // onNext("Hello");
    // onNext("World");
    // onNext("RxJava");
    // onCompleted();
    ```
* from(T[])/from(Iierabble<? extends T>)：将传入的数组或Iterable拆分成具体对象后，依次发送出来
    ```java
    String[] words = {"Hello", "World", "RxJava"};
    Observable observable = Observable.from(words);
    // 将会依次调用：
    // onNext("Hello");
    // onNext("World");
    // onNext("RxJava");
    // onCompleted();
    ```

##### Flowable
Flowable是RxJava2中新增的类，专门应对背压（Backpressure）问题，但这个概念并不是RxJava2中引入的概念。

出现Flowable的原因：即生产者（被观察者发送事件）的速度与消费者（观察者接收所有事件）的速度不匹配，从而导致观察者无法及时响应/处理所有发送过来的事件问题，最终导致缓冲区溢出，事件丢失 & OOM等问题。

一般情况，被观察者发送事件速度 ＞ 观察者接收事件速度。比如：点击过快造成等

```java
Flowable.create(new FlowableOnSubscribe<String>() {

    @Override
    public void subscribe(FlowableEmitter<String> emitter) throws Exception {
        emitter.onNext("Hello");
        emitter.onNext("World");
        emitter.onNext("RxJava2");
        emitter.onComplete();
    }
}, BackpressureStrategy.ERROR)
        .subscribeOn(Schedulers.computation())
        .observeOn(Schedulers.newThread())
        .subscribe(new Consumer<String>() {
            @Override
            public void accept(String s) throws Exception {
                // 相当于onNext
                Thread.sleep(1000);
                System.out.println("accept");
            }
        }, new Consumer<Throwable>() {
            @Override
            public void accept(Throwable throwable) throws Exception {
                // 相当于onError
                System.out.println("accept" + throwable.toString());
            }
        });
```
Flowable并不是订阅就开始发送数据，而是需等到执行`Subscription.request()`才开始发送数据

#### Create Observer

##### RxJava1
```java
Observer<String> observer = new Observer<String>() {
    @Override
    public void onCompleted() {
        System.out.println("Completed!");
    }

    @Override
    public void onError(Throwable e) {
        System.out.println("Error" + e);
    }

    @Override
    public void onNext(String s) {
        System.out.println("Next" + s);
    }
};
```
除了`Observer`接口之外，RxJava内置了一个实现`Observer`的抽象类`Subscriber`，`Subscriber`对`Observer`接口进行了一些扩展，但它们的基本使用方式是完全一样
```java
Subscriber<String> subscriber = new Subscriber<String>() {
    @Override
    public void onCompleted() {
        System.out.println("Completed!");
    }

    @Override
    public void onError(Throwable e) {
        System.out.println("Error" + e);
    }

    @Override
    public void onNext(String s) {
        System.out.println("Next" + s);
    }
};
```
实际，在RxJava的subscribe过程中，`Observer`也总是会先被转成一个`Subscriber`再使用。对于使用者来说`Observer`与`Subscriber`的主要区别是：
1. onStart()：这是`Subscriber`增加的方法。它会再subscribe刚开始，而事件还未发送之前被调用，可以用于做一些准备工作，例如：数据的重置等操作。这是一个可选方法，默认情况下它的实现为空。
>注意：
>对于准备工作有线程要求，`onStart()`就不适用，因为它总是再subscribe所发生的线程被调用，而不能指定线程。要指定线程来准备工作，可以使用`doOnSubscribe()`方法
2. unsubscribe()：`Subscriber`所实现的另一个接口`Subscription`的方法，用于取消订阅。在这个方法被调用后，`Subscriber`将不再接收事件。
>注意：
>* 一般需要在调用`unsubscribe()`方法前，需要使用`isUnsubscribed()`先判断状态。
>* 不再使用的时候尽快在合适的地方调用`unsubscribe()`来解除引用关系，以避免内存泄漏

##### RxJava2
```java
Observer<String> observer = new Observer<String>() {
    @Override
    public void onSubscribe(Disposable d) {
        System.out.println("Subscribe: " + d);
    }

    @Override
    public void onNext(String s) {
        System.out.println("Next: " + s);
    }

    @Override
    public void onError(Throwable e) {
        System.out.println("Error: " + e);
    }

    @Override
    public void onComplete() {
        System.out.println("Complete !");
    }
};
```

#### Subscribe
创建好`Observable`和`Observer`之后，再用`subscribe()`方法将它们联结起来
```java
observable.subscribe(observer);
// 或者(仅支持RxJava1)
observable.subscribe(subscriber);
```

#### chain calls
以上三步是使用RxJava进行异步操作的基本过程，创建`被观察者`，创建`观察者`，`被观察者`订阅`观察者`，我们可以通过链式调用形式完成操作

```java
Observable.unsafeCreate(new Observable.OnSubscribe<String>() {
    @Override
    public void call(Subscriber<? super String> subscriber) {
        subscriber.onNext("Hello");
        subscriber.onNext("World");
        subscriber.onNext("RxJava1");
        subscriber.onCompleted();
    }
}).subscribe(new Observer<String>() {
    @Override
    public void onCompleted() {
        System.out.println("Completed!");
    }

    @Override
    public void onError(Throwable e) {
        System.out.println("Error" + e);
    }

    @Override
    public void onNext(String s) {
        System.out.println("Next" + s);
    }
});
```
#### 简化订阅 
除了`subscribe(Observer)`和`subscribe(Subscriber)（仅支持RxJava1）`，`subscribe()`还支持不完整的简化订阅回调

```java
// RxJava1
Action1<String> onNextAction = new Action1<String>() {
    @Override
    public void call(String s) {
        System.out.println("onNext" + s);
    }
};

Action1<Throwable> onErrorAction = new Action1<Throwable>() {
    @Override
    public void call(Throwable throwable) {
        System.out.println("onError" + throwable);
    }
};

Action0 onCompletedAction = new Action0() {
    @Override
    public void call() {
        System.out.println("completed");
    }
};

// RxJava2
Consumer<String> onNextAction = new Consumer<String>() {
    @Override
    public void accept(String s) throws Exception {
        System.out.println("onNext" + s);
    }
};
        
Consumer<Throwable> onErrorAction = new Consumer<Throwable>() {
    @Override
    public void accept(Throwable throwable) throws Exception {
        System.out.println("onError" + throwable);
    }
};
        
Action onCompleteAction = new Action() {
    @Override
    public void run() throws Exception {
        System.out.println("complete");
    }
};

// 自动创建 Subscriber ，并使用 onNextAction 来定义 onNext()
observable.subscribe(onNextAction);
// 自动创建 Subscriber ，并使用 onNextAction 和 onErrorAction 来定义 onNext() 和 onError()
observable.subscribe(onNextAction, onErrorAction);
// 自动创建 Subscriber ，并使用 onNextAction、 onErrorAction 和 onCompletedAction 来定义 onNext()、 onError() 和 onCompleted()
observable.subscribe(onNextAction, onErrorAction, onCompletedAction/onCompleteAction);

```

## RxJava 线程
在RxJava的默认规则中，事件的发出和消费都是在同一个线程(在哪个线程条用`subscriber()`，就在哪个线程生产事件；在哪个线程生产事件，就在哪个线程消费事件)，也就是说，以上RxJava基本操作，实现出来的只是一个`同步`的观察者模式。而观察者模式本身的目的是“后台处理，前台回调”的`异步`机制，因此在RxJava中通过`Scheduler`来对线程进行管理

### Scheduler API
Scheduler相当于线程控制器，RxJava通过它指定代码应该运行在什么样的线程，其中RxJava中内置了几个Scheduler
* Schedulers.computation()：计算所使用的`Scheduler`。这个计算值的是CPU密集型计算，即不会被I/O操作等限制性能的操作。不要把I/O操作放在`computation()`中，否则I/O操作的等待时间会浪费CPU。
* Schedulers.form(Executor)：
* Schedulers.immediate()：直接在当前线程运行，相当于不指定线程，这也是默认的Scheduler.
* Schedulers.io()：I/O操作（读写文件，读写数据库，网络信息交换等）所使用的Scheduler。行为模式和newThread()差不多，区别在于io()的内部实现是一个无数量上限的线程池，可以重用空闲的线程，因此多数情况下io()比newThread()更高效
* Schedulers.newThread()：总是启用新线程，并在新线程执行操作
* Schedulers.single()『仅RxJava2中存在』：
* Schedulers.test()『仅RxJava1中存在』：顾名思义，这是一个测试
* Schedulers.trampoline()：
* AndoroidSchedulers.mainThread()：指定操作在Android的主线程

有了Scheduler，我们可以使用`subscribeOn()`和`observeOn()`方法来对线程进行控制
* subscribeOn()：指定subscribe()所发生的线程，即Observable.OnSubscribe被激活时所处的线程，或者叫做事件的产生的线程
* observeOn()：指定Subscriber所运行的线程。或者叫做事件的消费线程

    ```java
    // RxJava2
    Observable.create(new ObservableOnSubscribe<String>() {
        @Override
        public void subscribe(ObservableEmitter<String> emitter) throws Exception {
            emitter.onNext("Hello");
            emitter.onNext("World");
            emitter.onNext("RxJava2");
            emitter.onComplete();
        }
    })
            // 指定 subscribe() 发生在 IO 线程
            .subscribeOn(Schedulers.io())
            // 指定 Subscriber 的回调发生在主线程
            .observeOn(AndroidSchedulers.mainThread())
            .subscribe(new Consumer<String>() {
                @Override
                public void accept(String s) throws Exception {
                    System.out.println(s);
                }
            });
    ```

## 操作符
说RxJava好用，还有一个原因是RxJava提供了大量的操作符，这些操作符保证了在面都复杂的逻辑下，依旧可以是逻辑清晰的链式调用

![RxJava_action](https://res.cloudinary.com/incoder/image/upload/v1538815494/blog/RxJava_action.png)

## 总结
本篇文章作为RxJava系列的学习的入门，不会讲解相关操作的原理等
学习目的
* 了解RxJava1与RxJava2之间的不同点，
* 了解RxJava的线程管理，
* 掌握完成RxJava的基本操作，
* 清楚RxJava操作符，以及分别适用于什么样的场景

## 附录
文章中部分原话引用了参考学习文章的原话，在这里向那些无私分享的大佬致敬
* [给 Android 开发者的 RxJava 详解](https://gank.io/post/560e15be2dca930e00da1083)
* [RxJava系列教程](https://www.jianshu.com/nb/14302692)