---
title: IntentService 源码解析
date: 2017-08-12 10:31:29
tags: [IntentServcie]
categories: 源码阅读
blog: true
---



IntentService 继承子 Service，所以是 Service 的加强版

Service 中的函数都是在主线程中进行的，而 Service 一般是用来处理耗时操作的，如果在主线程中处理耗时操作可能导致 ANR，所以出现了后台线程处理耗时操作的 Service —— IntentService，当耗时操作结束时，会自动结束这个 Service

首先看看 onCreate 方法

<!-- more -->

```java
    @Override
    public void onCreate() {

        super.onCreate();
        HandlerThread thread = new HandlerThread("IntentService[" + mName + "]");
        thread.start();

        mServiceLooper = thread.getLooper();
        mServiceHandler = new ServiceHandler(mServiceLooper);
    }
```

onCreate 方法中会创建一个线程和一个 Handler

我们知道，Service 收到 Intent 调用的时候都会调用 onStartCommand 方法

```java
    @Override
    public void onStart(@Nullable Intent intent, int startId) {
        Message msg = mServiceHandler.obtainMessage();
        msg.arg1 = startId;
        msg.obj = intent;
        mServiceHandler.sendMessage(msg);
    }
    @Override
    public int onStartCommand(@Nullable Intent intent, int flags, int startId) {
        onStart(intent, startId);
        return mRedelivery ? START_REDELIVER_INTENT : START_NOT_STICKY;
    }
```

所以 IntentService 都会使用刚刚的 Handler 发送一个 message, 交给后台线程 HandlerThread 处理，下面看看 Hanlder 是如何处理的。

```java
    private final class ServiceHandler extends Handler {
        public ServiceHandler(Looper looper) {
            super(looper);
        }

        @Override
        public void handleMessage(Message msg) {
            onHandleIntent((Intent)msg.obj);
            stopSelf(msg.arg1);
        }
    }
```

Handler 会调用 Servcie 的 onhandleIntent，然后会调用 stopSelf(msg.arg1), 当 onHandleIntent 处理完之后才会调用 stopSelf(int)

然后看看 stopSelf(int) 的作用，其实就是老的 stopSelfResult 方法：

```
Stop the service if the most recent time it was started was <var>startId</var>. This is the same as calling Context#stopService(), 
for this particular service but allows you to afely avoid stopping if there is a start request from a client that you haven't yet seen in {@link #onStart}

Be careful about ordering of your calls to this function. If you call this function with the most-recently received ID before you have called it
 for previously received IDs,the service will be immediately stopped anyway.
```


这里有几个需要注意的地方：
- 只有 stopSelf 使用的 ID 等于最后的 onStart 的 ID 才会把 service 停掉
- 如果 onHandleIntent 中使用了其他线程，onHandleIntent 返回之后，会直接停掉 Service，所以 onHandleIntent 中是不能使用其他线程的



