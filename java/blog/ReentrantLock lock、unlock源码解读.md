---
title: ReentrantLock lock、unlock源码解读
date: 2017-05-19 15:34:47
categories: Java并发
tags: [lock,unlock]
---

> ReentrantLock 对于公平锁和非公平锁的加锁过程不同。对于公平锁加锁，如果前面没有比当前线程等候更久的线程的话，则当前线程直接获得锁，否则直接将当前线程插入等候队列中；对于非公平锁的加锁和前面不一样，如果前一个线程刚好结束state=0，此时无论当前线程前面是否有线程等候，当前线程都能直接抢占锁。如果前一个线程还没执行完毕，则当前线程便插入等候队列中。

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



未完……