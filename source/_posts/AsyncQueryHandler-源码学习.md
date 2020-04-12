---
title: AsyncQueryHandler 源码学习
date: 2017-03-06 21:09:45
tags: [AsyncQueryHandler, 源码]
categories: 源码阅读
blog: true
---



ContentProvider 的单线程执行工具类

### 构造函数

- 在构造函数中需要传入 ContentResolver 对象
- 创建一个 Thread 线程，用于线性执行 msg
- 然后创建一个 Handler 对象(WorkerHandler)。

### 能处理的操作

- Query
- Insert
- Update
- Delete

### 执行流程

- AsyncQueryHandler 也是一个 Handler 对象。当使用 startInsert/startUpdate 等方法时，先将 msg 发送到 WorkerHandler 的队列中，在其 handleMessage 时执行并获得结果放入 result 中，然后发送到 AsyncQueryHandler 这个 handler 的队列中。
- AsyncQueryHandler 的 handleMessage 处理比较简单，直接将 token, cookie, args.result 作为参数传递给 onQueryComplete。

重要结构体

```java
protected static final class WorkerArgs {
        public Uri uri;
        public Handler handler;
        public String[] projection;
        public String selection;
        public String[] selectionArgs;
        public String orderBy;
        public Object result;
        public Object cookie;
        public ContentValues values;
    }
```

