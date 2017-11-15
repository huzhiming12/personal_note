---
title: ReentrantLock lock、unlock源码解读
date: 2017-05-19 15:34:47
categories: Java并发
tags: [lock,unlock]
---

> ReentrantLock 对于公平锁和非公平锁的加锁过程不同。对于公平锁加锁，如果前面没有比当前线程等候更久的线程的话，则当前线程直接获得锁，否则直接将当前线程插入等候队列中；对于非公平锁的加锁和前面不一样，如果前一个线程刚好结束state=0，此时无论当前线程前面是否有线程等候，当前线程都能直接抢占锁。如果前一个线程还没执行完毕，则当前线程便插入等候队列中。ReentrantLock默认是非公平锁。

### 等候队列

ReentrantLock实现是基于**AbstractQueuedSynchronizer(AQS)**，AQS实现是通过一个等候队列和CAS(Compare and swap)原子操作实现的。等候队列其实一个双向链表，链表中的每个元素都是一个Node结点。 Node中的每个变量都用volatile修饰保证内存的可见性

```Java
static final class Node {
        /** Marker to indicate a node is waiting in shared mode */
        static final Node SHARED = new Node();
        /** Marker to indicate a node is waiting in exclusive mode */
        static final Node EXCLUSIVE = null;
        /** waitStatus value to indicate thread has cancelled */
        static final int CANCELLED =  1;
        /** waitStatus value to indicate successor's thread needs unparking */
        static final int SIGNAL    = -1;
        /** waitStatus value to indicate thread is waiting on condition */
        static final int CONDITION = -2;
        
        static final int PROPAGATE = -3;

        volatile int waitStatus;
		//前一个结点
        volatile Node prev;
		//下一个结点
        volatile Node next;
		//结点中存放的线程
        volatile Thread thread;

        Node nextWaiter;

        final boolean isShared() {
            return nextWaiter == SHARED;
        }
		//获取当前结点的上一个结点
        final Node predecessor() throws NullPointerException {
            Node p = prev;
            if (p == null)
                throw new NullPointerException();
            else
                return p;
        }

        Node() {    // Used to establish initial head or SHARED marker
        }

        Node(Thread thread, Node mode) {     // Used by addWaiter
            this.nextWaiter = mode;
            this.thread = thread;
        }

        Node(Thread thread, int waitStatus) { // Used by Condition
            this.waitStatus = waitStatus;
            this.thread = thread;
        }
    }

```

### 公平锁lock

1. ReentrantLocak中的lock方法

   ```Java
   public void lock() {
           sync.lock();
       }
   ```

2. FairSync 中的lock

   ```Java
   final void lock() { //尝试获取锁
               acquire(1);
           }
   ```

3. AbstractQueuedSynchronizer中获取锁

   ```Java
   public final void acquire(int arg) {
     			//先判断能不能获取到锁，能获取到锁的话直接就结束了
           if (!tryAcquire(arg) &&
               //如果上面不能获取到锁的话，就将当前线程插入等候队列中
               acquireQueued(addWaiter(Node.EXCLUSIVE), arg))
             	//阻塞当前线程
               selfInterrupt();
       }
   ```

   FairSync 覆盖了AbstractQueuedSynchronizer中的tryAcquire方法

   ```Java
   protected final boolean tryAcquire(int acquires) {
               final Thread current = Thread.currentThread();
               int c = getState();
     			//state==0 当前没有线程占用锁
               if (c == 0) {
                 	//如果队列中没有比当前线程等候时间更长的线程
                   if (!hasQueuedPredecessors() &&
                       //修改锁的状态，表示锁被当前线程占用
                       compareAndSetState(0, acquires)) {
                     	//设置当前线程是占用锁的线程
                       setExclusiveOwnerThread(current);
                       return true;
                   }
               }
     			// 如果占用锁的线程就是当前线程
               else if (current == getExclusiveOwnerThread()) {
                 	//直接将state 加1
                   int nextc = c + acquires;
                   if (nextc < 0)
                       throw new Error("Maximum lock count exceeded");
                   setState(nextc);
                   return true;
               }
     			//上面的情况都不满足，返回false，表示线程没有获取到锁
               return false;
           }
   ```

   获取不到锁的话，直接插入等候队列的队尾

   ```Java
   private Node addWaiter(Node mode) {
           Node node = new Node(Thread.currentThread(), mode);
           // Try the fast path of enq; backup to full enq on failure
           Node pred = tail;
           if (pred != null) {
             	//队尾元素不为空，插入队尾
               node.prev = pred;
               if (compareAndSetTail(pred, node)) {
                   pred.next = node;
                   return node;
               }
           }
     		//tail 为空，插入队列头
           enq(node);
           return node;
       }
   ```




### 非公平锁

1. ReentrantLock获取锁

   ```java
   public void lock() {
           sync.lock();
       }
   ```

2. 调用NonfairSync的lock方法，先判断锁是否被占用，如果没有直接获取；否则，尝试获取

   ```java
   final void lock() {
     			//如果此时上一个线程刚执行完，state=0，则直接获取锁，这就是非公平锁，
     			//无论此时等候队列中是否还有排队的线程，都直接占有锁
               if (compareAndSetState(0, 1))
                 	//设置当前线程为执行的线程
                   setExclusiveOwnerThread(Thread.currentThread());
               else
                 	//尝试获取锁
                   acquire(1);
           }
   ```

3. 调用AbstractQueuedSynchronizer中的获取锁方法

   ```java
   public final void acquire(int arg) {
     		//尝试去获取锁
           if (!tryAcquire(arg) &&
               //如果上面获取锁失败，则加入等候队列中
               acquireQueued(addWaiter(Node.EXCLUSIVE), arg))
               // 如果成功加入等候队列中，则阻塞自身
               selfInterrupt();
       }
   ```

   * 调用NonfairSync重写的tryAcquire方法

     ```java
     protected final boolean tryAcquire(int acquires) {
                 return nonfairTryAcquire(acquires);
             }
     ```

   * 调用Sync内部类中nonfairTryAcquire方法

     ```java
     final boolean nonfairTryAcquire(int acquires) {
                 final Thread current = Thread.currentThread();
                 int c = getState();
       			//如果此时没有线程占有锁，则当前线程直接占有，将state值改为1
                 if (c == 0) {
                   	//设置锁的状态为1
                     if (compareAndSetState(0, acquires)) {
                       	//设置当前线程为占有锁的线程
                         setExclusiveOwnerThread(current);
                         return true;
                     }
                 }
       			//如果当前线程已经占有锁
                 else if (current == getExclusiveOwnerThread()) {
                   	//直接将锁的state值加1，表示当前线程有两个任务，可重入的实现
                     int nextc = c + acquires;
                     if (nextc < 0) // overflow
                         throw new Error("Maximum lock count exceeded");
                   	//更改state值
                     setState(nextc);
                     return true;
                 }
                 return false;
             }
     ```

   * addWaiter()方法，将当前线程加入等候队列队尾

     ```java
     private Node addWaiter(Node mode) {
             Node node = new Node(Thread.currentThread(), mode);
             // Try the fast path of enq; backup to full enq on failure
             Node pred = tail;
       		//如果尾结点不为空，将当前结点插入尾结点的后面
             if (pred != null) {
               	//设置当前结点前结点为尾结点，即当前结点变为尾结点
                 node.prev = pred;
                 if (compareAndSetTail(pred, node)) {
                     pred.next = node;
                     return node;
                 }
             }
       		//如果尾结点为空，即当前队列是空队列。则插入队列中，并把当前结点设为头结点和尾结点
             enq(node);
             return node;
         }
     ```

   * enq操作，队里为空时，则先添加头结点，然后再加入队尾，头结点不存储信息

     ```java
     private Node enq(final Node node) {
             for (;;) {
                 Node t = tail;
                 if (t == null) { // Must initialize
                   	//如果尾结点为空，即队列为空，则先生成头结点。然后再次循环，走下面的逻辑
                     if (compareAndSetHead(new Node()))
                         tail = head;
                 } else {
                   	//如果队列不为空了，则加入队尾
                     node.prev = t;  
                     if (compareAndSetTail(t, node)) {
                         t.next = node;
                         return t;
                     }
                 }
             }
         }
     ```

   4. acquireQueued方法

      ```java
      final boolean acquireQueued(final Node node, int arg) {
              boolean failed = true;
              try {
                  boolean interrupted = false;
                  for (;;) {
                      final Node p = node.predecessor();
                    	//如果当前线程是等候队列中的唯一线程(因为head里面是没有信息的)
                    	//则会再次尝试获取锁
                      if (p == head && tryAcquire(arg)) {
                        	//如果获取到锁的话，修改当前结点为头结点，并将里面的信息清空，同时删除当前的头
                        	//结点。返回当前结点没有被阻塞
                          setHead(node);
                          p.next = null; // help GC
                          failed = false;
                          return interrupted;
                      }
                      if (shouldParkAfterFailedAcquire(p, node) &&
                          parkAndCheckInterrupt())
                          interrupted = true;
                  }
              } finally {
                  if (failed)
                      cancelAcquire(node);
              }
          }
      private void setHead(Node node) {
              head = node;
              node.thread = null;
              node.prev = null;
          }
      ```

      ​