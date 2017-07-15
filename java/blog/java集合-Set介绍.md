---
title: java集合-Set介绍
date: 2017-05-14 16:19:08
categories: Java基础
tags: [HashSet,TreeSet]
---

> 前面介绍了一些关于List、Map的相关知识，下面我们继续来看下集合中的set。set作为容器主要存储不重复的元素。

#### HashSet

* HashSet底层实现是通过HashMap实现的，set中每个元素对应Map中的每个EntrySet结点。存储元素时只用Map的key值存储，value值统一设置为同一个new Object()。
* HashSet底层存储数据的是map，所以Hashset允许null存在
* HashSet存放的元素各不相同，当掉用add方法添加已存在的元素时会返回false.


#### TreeSet

* TreeSet底层是通过TreeMap实现。存储元素只使用TreeMap中的key，Value同一设成相同的new Object();
* TreeSet不允许插入null值
* ​TreeSet元素是有序的。


