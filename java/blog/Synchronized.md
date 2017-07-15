---
title: Synchronized
date: 2017-05-17 16:38:44
categories: Java并发
tags: [并发,synchronized]
---

> Synchronized 是Java中的一种锁，主要用来给方法和代码块加锁。当某个方法或者代码块使用synchronized关键字时，那么同一时刻最多只能有一个线程执行该代码段。当多个线程同时访问时，只有一个线程执行，其他线程处于阻塞状态，当线程执行完毕后其他线程才能执行。
>
> synchronized 是Java虚拟机底层实现的锁机制，当synchronized方法或者代码块中发生异常时，会系统会自动释放锁定资源。

Synchronized主要有两种用法，一种是synchronized方法，另一种是synchronized代码块

### Synchronized方法

#### 类的成员方法

在类的成员方法声明中加上Synchronized关键字，其锁定的是类的实例对象

```Java
public synchronized void getNum(){}
```

synchronized加在类的成员方法中，本质上锁定是this对象，它和下面的写法是等价的：

```Java
public void getNum(){
  	synchronized(this){
		……
    }
}
```

##### 代码访问的同步性

* 当多个线程同时访问同一个synchronized方法时，一次只能一个线程执行

* 当多个线程同时访问类中不同的Synchronized方法时，一次也只能一个线程执行，其他线程阻塞

  ```Java
  public synchronized void getNum(){}

  public synchronized void getNum1(){}
  ```

  即当两个线程同时一个调用getNum，另一个调用getNum1()，同一时间只能一个线程运行

* 当两个线程一个调用Synchronized方法，另一个线程调用普通方法，则两个线程可以同时进行，不会相互影响

#### 类的静态方法static

在类的静态方法中加上Synchronized关键字，其锁定的是class类对象

```java
public static synchronized void getNum(){}
```

其效果等同于下面的写法

```java
public static void getNum(){  //我们假设这个静态方法在Main这个类中声明
  	synchronized(Main.class){
		……
    }
}
```

##### 代码访问的同步性

```java
public class Main{
    public void method(){};
    public synchronized void method1(){};  //锁定的是this
    public synchronized void method2(){};
    public synchronized static void method3(){};  //锁定的是class
    public synchronized static void method4(){};
}
```

* method() 和 method1() 能同时访问
* method()和method3()能同时访问
* method1()和method2()不能同时访问
* method3()和method4()不能同时访问
* method1()和method3()可以同时访问



### Synchronized代码块

Synchronized代码块把需要同步的代码加上锁，将那些对线程安全没有影响的代码移出Synchronized代码块，具体写法如下

```Java
public void getNum()
{
  	……
  	synchronized(obj)
  	{
      …… 线程同步代码块
  	}
}
```

代码块中添加Synchronized可以减小锁的粒度，提高程序并发的效率。

#### 线程访问的同步性

* 若多个线程同时访问一个Synchronized代码块，则一次只能有一个线程能够访问
* 若多个代码Synchronized代码块锁定的对象是同一obj，则当多个线程同时访问这些代码块时，一次只能有一个线程能够访问这些代码块。

#### 测试用例

用例1：多个线程同时访问一个Synchronized代码块

```java
public class Test implements Runnable
{
	private static Integer num = 0;
	private Object obj = new Object();

	@Override
	public void run()
	{
		getNum();
	}
	public static void main(String[] args)
	{
		Test test = new Test();
		for (int i = 0; i < 3; i++)
			new Thread(test, "Thread_1_" + i).start();
	}
	public void getNum()
	{
		synchronized (obj)
		{
			for (int i = 0; i < 5; i++)
				System.out.println(Thread.currentThread().getName() + " " + num++);
			System.out.println();
		}
	}
}

```

测试结果：一次只能一个线程可以运行

```
Thread_1_0 0
Thread_1_0 1

Thread_1_2 2
Thread_1_2 3

Thread_1_1 4
Thread_1_1 5
```



测试用例2：多个线程同时访问多个Synchronized代码块，且这些代码块的锁定对象相同

```Java
public class Test implements Runnable
{
	private static Integer num = 0;
	private Object obj = new Object();
	@Override
	public void run()
	{
		getNum();
	}
	private class MyRunnable implements Runnable
	{
		@Override
		public void run()
		{
			getNum1();
		}
	}
	public static void main(String[] args)
	{
		Test test = new Test();
		for (int i = 0; i < 3; i++)
			new Thread(test, "Thread_1_" + i).start();

		Runnable myRunnable = test.new MyRunnable();
		for (int i = 0; i < 3; i++)
		{
			new Thread(myRunnable, "Thread_2_" + i).start();
		}
	}
	public void getNum()
	{
		synchronized (obj)
		{
			for (int i = 0; i < 2; i++)
				System.out.println(Thread.currentThread().getName() + " " + num++);
			System.out.println();
		}
	}
	public void getNum1()
	{
		synchronized (obj)
		{
			for (int i = 0; i < 2; i++)
				System.out.println(Thread.currentThread().getName() + " " + num++);
			System.out.println();
		}
	}
}
```

测试结果：每次只能有一个线程可以访问Synchronized代码块

```
Thread_1_0 0
Thread_1_0 1

Thread_1_2 2
Thread_1_2 3

Thread_1_1 4
Thread_1_1 5

Thread_2_0 6
Thread_2_0 7

Thread_2_1 8
Thread_2_1 9

Thread_2_2 10
Thread_2_2 11
```