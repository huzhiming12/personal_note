---
title: Java集合-继承关系和常用API
date: 2017-05-5 14:37:43
categories: Java基础
tags: [Collection,API]
---

### 集合继承关系

Collection集合的类继承关系图：

![](Java集合-继承关系和常用API/pic11.jpg)

<!--more--->

Map集合继承关系图：

![](Java集合-继承关系和常用API/pic12.jpg)

### 常用的API

#### ArrayList：

```java
//List尾部添加元素
boolean add(E e);
//在index位置添加元素
public void add(int index,E element);
//移除元素o
boolean remove(Object o);
//移除下标index的元素
public E remove(int index);
//清空列表
public void clear();
//添加集合中的全部元素
public boolean addAll(Collection<? extends E> c);
//返回list的遍历
public Iterator<E> iterator();
//是否包含元素
public boolean contains(Object o);
//将下标为index元素替换
public E set(int index,E element)
```

#### Queue：

```java
//添加元素
boolean add(E e);
//获取并移除队列头,此队列为空时将抛出一个异常。
E remove();
//获取并移除此队列的头，如果此队列为空，则返回 null。
E poll();
//获取，但是不移除此队列的头。此方法与 peek 唯一的不同在于：此队列为空时将抛出一个异常。
E element();
//获取但不移除此队列的头；如果此队列为空，则返回 null。
E peek();
```

#### Set:

```Java
//添加元素
boolean add(E e);
//移除元素
boolean remove(Object o);
//添加所有集合元素
boolean addAll(Collection<? extends E> c);
//清空集合
void clear();
//返回set的迭代器
Iterator<E> iterator();
```

#### Stack: 继承自vector，是线程安全的

```java
//把项压入堆栈顶部。
public E push(E item);
//移除栈顶元素
public E pop();
//返回栈顶元素，但是不删除
public E peek();
//测试栈是否为空
public boolean empty();
//返回元素在栈中的位置，以1为基数，-1表示不存在
public int search(Object o);
```
