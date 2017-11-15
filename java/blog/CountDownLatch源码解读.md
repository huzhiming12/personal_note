---
title: CountDownLatch源码解读
date: 2017-05-19 15:34:47
categories: Java并发
tags: countDownLatch
---

### CountDownLatch的是干什么的

当一项任务分给多个线程去完成，每个线程的完成时间不确定，当所有线程完成任务后需要对这些任务进行整合。这样就有一个问题，每个线程完成时间不固定，该何时进行任务的整合呢。这就可以通过CountDownLatch这个工具来实现

#### 使用场景：多任务下载

下载一般是将需要下载的文件分块，定义多个线程，每个线程负责去下载对应的数据块。当所有数据块都下载下来之后对所有数据块进行合并。

#### CountDownLatch实现原理

CountDownLatch通过设定任务数即线程数count，当线程任务执行完毕后会将count减1。当count等于0时，调用CountDownLatch的线程才会接着继续执行。

#### CountDownLatch的简单使用样例

```java
class Worker extends Thread
{
    private CountDownLatch latch;

    public Worker(CountDownLatch latch)
    {
        this.latch = latch;
    }

    @Override
    public void run()
    {
        super.run();
        System.out.println(Thread.currentThread().getName() + " begin work!");
        try
        {
            sleep(new Random().nextInt(10000));
        } catch (InterruptedException e)
        {
            e.printStackTrace();
        }
        System.out.println(Thread.currentThread().getName() + " work over!");
        //线程任务执行完毕，count减1
        latch.countDown();
    }
}

public class CountDownLatchDemo
{
    public static void main(String[] args) throws InterruptedException
    {
      	//设定初始计算
        CountDownLatch latch = new CountDownLatch(3);
        Worker worker1 = new Worker(latch);
        worker1.start();
        Worker worker2 = new Worker(latch);
        worker2.start();
        Worker worker3 = new Worker(latch);
        worker3.start();
        latch.await();
        System.out.println("works is over, begin Main Work!");
    }
}
```



### CountDownLatch源码解读

CountDownLatch底层实现是通过AQS(AbstractQueuedSynchronizer)，实现方式和ReentrantLock很类似

1. 通过构造函数传入count，即要开启的线程任务数。注意count值只能通过构造函数传入，其他时候不能对count进行修改，除了线程执行完毕。

   ```java
   public CountDownLatch(int count) {
           if (count < 0) throw new IllegalArgumentException("count < 0");
           this.sync = new Sync(count);
       }
   ```

   ```
   Sync(int count) {
               setState(count);
           }
   ```

   调用AQS的方法，设置state值

   ```java
   protected final void setState(int newState) {
           state = newState;
       }
   ```

2. await()方法，阻塞当前调用CountDownLatch的线程

   ```java
   public void await() throws InterruptedException {
           sync.acquireSharedInterruptibly(1);
       }
   ```

   AQS方法

   ```java
   public final void acquireSharedInterruptibly(int arg)
               throws InterruptedException {
     		//如果线程已经被阻塞，报出异常
           if (Thread.interrupted())
               throw new InterruptedException();
     		//尝试去获取锁，如果已经被占用，则阻塞自身线程
           if (tryAcquireShared(arg) < 0)
               doAcquireSharedInterruptibly(arg);
       }
   ```

   调用Aync内部类中的重写方法

   ```java
   protected int tryAcquireShared(int acquires) {
     			//如果当前锁已经被占用，则返回负值，如果没有被占用就返回正值
               return (getState() == 0) ? 1 : -1;
           }
   ```

   AQS中的方法

   ```java
   private void doAcquireSharedInterruptibly(int arg)
           throws InterruptedException {
     		//将当前线程插入等候队列中
           final Node node = addWaiter(Node.SHARED);
           boolean failed = true;
           try {
               for (;;) {
                   final Node p = node.predecessor();
                 	//如果队列中除了head结点外只有一个结点。
                   if (p == head) {
                     	//尝试去获取锁
                       int r = tryAcquireShared(arg);
                     	// 锁未被占用
                       if (r >= 0) {
                           setHeadAndPropagate(node, r);
                           p.next = null; // help GC
                           failed = false;
                           return;
                       }
                   }
                   if (shouldParkAfterFailedAcquire(p, node) &&
                       parkAndCheckInterrupt())
                       throw new InterruptedException();
               }
           } finally {
               if (failed)
                   cancelAcquire(node);
           }
       }
   ```

   ​

