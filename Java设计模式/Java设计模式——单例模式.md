---
title: Java设计模式——单例模式
date: 2017-06-11 15:51:08
categories: Java设计模式
tags: 单例模式
---

单例模式是指在程序运行过程中，一个单例类只有有一个实例化对象，并且该类自己负责创建类的实例。

单例模式的实现方式有一下几种

* 饿汉式单例

  它基于 classloder 机制避免了多线程的同步问题，不过，instance 在类装载时就实例化，虽然导致类装载的原因有很多种，在单例模式中大多数都是调用 getInstance 方法， 但是也不能确定有其他的方式（或者其他的静态方法）导致类装载，这时候初始化 instance 显然没有达到 lazy loading 的效果。

  ```Java
  class Singleton1
  {
      private Singleton1()
      {
      }
      private static Singleton1 instance = new Singleton1();
      public static Singleton1 getInstance()
      {
          return instance;
      }
  }
  ```



* 非线程安全的 懒汉式单例

  这种方式是最基本的实现方式，这种实现最大的问题就是不支持多线程。因为没有加锁 synchronized，所以严格意义上它并不算单例模式。这种方式 lazy loading 很明显，不要求线程安全，在多线程不能正常工作。

  ```Java
  class Singleton2
  {
      private Singleton2()
      {
      }
      private static Singleton2 instance;
      public static Singleton2 getInstance()
      {
          if (instance == null)
              instance = new Singleton2();
          return instance;
      }
  }
  ```



* 线程安全的懒汉式单例

  这种方式具备很好的 lazy loading，能够在多线程中很好的工作，但是，效率很低，99% 情况下不需要同步。
  优点：第一次调用才初始化，避免内存浪费。

  缺点：必须加锁 synchronized 才能保证单例，但加锁会影响效率。

  ```Java
  class Singleton3
  {
      private Singleton3()
      {
      }
      private static Singleton3 instance;
      public synchronized static Singleton3 getInstance()
      {
          if (instance == null)
              instance = new Singleton3();
          return instance;
      }
  }
  ```



* 双重检验线程安全的 懒汉式单例

  这种方式采用双锁机制，安全且在多线程情况下能保持高性能。getInstance() 的性能对应用程序很关键。

  ```Java
  class Singleton4
  {
      private Singleton4()
      {
      }
      private static Singleton4 instance;
      public static Singleton4 getInstance()
      {
          if (instance == null)
          {
              synchronized (Singleton4.class)
              {
                  if (instance == null)
                      instance = new Singleton4();
              }
          }
          return instance;
      }
  }
  ```



* 静态内部类 单例

  ```Java
  class Singleton5
  {
      private static class SingletonHoder
      {
          private static Singleton5 INSTANCE = new Singleton5();
      }
      private Singleton5()
      {
      }
      public static Singleton5 getInstance()
      {
          return SingletonHoder.INSTANCE;
      }
  }
  ```



* 枚举类型单例

  ```Java
  enum Singleton6
  {
      INSTANCE;
      public void method()
      {
          System.out.println("test");
      }
  }
  ```