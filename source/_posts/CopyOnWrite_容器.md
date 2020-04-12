---
title: CopyOnWrite 容器
date: 2017-07-20 12:06:47
tags: [CopyOnWrite]
categories: Java
blog: true
---



CopyOnWrite 容器包括 CopyOnWriteArrayList, CopyOnWriteSet

### CopyOnWriteArrayList

1. elements 是 volatile 修饰的，当任何一个线程把 elements 地址修改之后，其他任何线程都能够知晓。
2. write 操作都是加锁的，只会有一个线程修改 elements
3. write 操作的时候，先拷贝一份数组，然后再修改拷贝数组，修改完之后，把 elements 指向这个拷贝，然后其他线程都使用新的数组
4. read 操作都是不需要加锁的

<!-- more -->

```java
public synchronized boolean add(E e) {
    Object[] newElements = new Object[elements.length + 1];
    System.arraycopy(elements, 0, newElements, 0, elements.length);
    newElements[elements.length] = e;
    elements = newElements;
    return true;
}
public synchronized boolean addAll(int index, Collection<? extends E> collection) {
    Object[] toAdd = collection.toArray();
    Object[] newElements = new Object[elements.length + toAdd.length];
    System.arraycopy(elements, 0, newElements, 0, index);
    System.arraycopy(toAdd, 0, newElements, index, toAdd.length);
    System.arraycopy(elements, index,
            newElements, index + toAdd.length, elements.length - index);
    elements = newElements;
    return toAdd.length > 0;
}
```

Get 不需要加锁

```java
final Object[] getArray() {
    // CopyOnWriteArraySet needs this.
    return elements;
}
public int indexOf(Object object) {
    Object[] snapshot = elements;
    return indexOf(object, snapshot, 0, snapshot.length);
}
@SuppressWarnings("unchecked")
public E get(int index) {
    return (E) elements[index];
}
```

### CopyOnWriteArraySet

使用 CopyOnWriteArrayList 实现的

```java
public synchronized int addAllAbsent(Collection<? extends E> collection) {
    Object[] toAdd = collection.toArray();
    Object[] newElements = new Object[elements.length + toAdd.length];
    System.arraycopy(elements, 0, newElements, 0, elements.length);
    int addedCount = 0;
    for (Object o : toAdd) {
        if (indexOf(o, newElements, 0, elements.length + addedCount) == -1) {
            newElements[elements.length + addedCount++] = o;
        }
    }
    if (addedCount < toAdd.length) {
        newElements = Arrays.copyOfRange(
                newElements, 0, elements.length + addedCount); // trim to size
    }
    elements = newElements;
    return addedCount;
}
public boolean add(E e) {
    return al.addIfAbsent(e);
}
```

// get 没有加锁
```java
public boolean contains(Object o) {
    return al.contains(o);
}
```






