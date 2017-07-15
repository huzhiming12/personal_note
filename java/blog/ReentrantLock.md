---
title: ReentrantLock简介
date: 2017-05-18 14:06:47
categories: Java并发
tags: [lock,并发]
---

> ReentrantLock是一个可重入的互斥锁定 Lock，它具有与使用 synchronized 方法和语句所访问的隐式监视器锁定相同的一些基本行为和语义，但功能更强大。ReentrantLock 将由最近成功获得锁定，并且还没有释放该锁定的线程所拥有。当锁定没有被另一个线程所拥有时，调用 lock 的线程将成功获取该锁定并返回。如果当前线程已经拥有该锁定，此方法将立即返回。可以使用 isHeldByCurrentThread() 和 getHoldCount() 方法来检查此情况是否发生。

**重入锁：**

如果当前占有锁的线程是Thread1，则当Tread1再次到来的时候不要要排队，直接将state加1，即运行次数加1。



ReentrantLock实现了Lock接口，提供了完整的Lock功能。它的一个内部类Sync类继承了**AbstractQueuedSynchronizer（AQS）**实现锁的功能，但是Sync并不提供公平和非公平锁机制，因此在Sync的基础之上，ReentrantLock内部又提供了两个内部类**NonfairSync**非公平锁、和**FairSync**公平锁，这两个类都是继承自Sync。

公平锁和非公平锁的区别：

* **公平锁**：线程按照他们发出信号的先后来获取锁，采用先来先服务的原则。新来的线程直接放入等候队列中。
* **非公平锁**：支持抢占方式，新来的线程首先会检查锁是否占用，如果被占用直接插入等候队列中，如果上一个线程刚好结束state=0时，系统会直接调用这个新来的线程执行，而不会去等候队列中唤醒阻塞的线程。这样做的好处是可以提高系统的吞吐量，因为从阻塞队列总唤醒线程需要耗费时间。

#### Sync

Sync继承自AbstractQueuedSynchronizer，

```Java
abstract static class Sync extends AbstractQueuedSynchronizer {
        private static final long serialVersionUID = -5179523762034025860L;

  		//加锁功能
        abstract void lock();
  
  		//非公平锁尝试获取锁
        final boolean nonfairTryAcquire(int acquires) {
            final Thread current = Thread.currentThread();
            int c = getState();
          	//如果没有锁定，则直接加上锁
            if (c == 0) {
              	//直接
                if (compareAndSetState(0, acquires)) {
                    setExclusiveOwnerThread(current);
                    return true;
                }
            }
          	//如果拥有资源的线程 和当前线程相同则state加1
            else if (current == getExclusiveOwnerThread()) {
                int nextc = c + acquires;
                if (nextc < 0) // overflow
                    throw new Error("Maximum lock count exceeded");
                setState(nextc);
                return true;
            }
          	//否则返回false
            return false;
        }
		//尝试释放锁
        protected final boolean tryRelease(int releases) {
            int c = getState() - releases;
            if (Thread.currentThread() != getExclusiveOwnerThread())
                throw new IllegalMonitorStateException();
            boolean free = false;
            if (c == 0) {
                free = true;
                setExclusiveOwnerThread(null);
            }
            setState(c);
            return free;
        }

        protected final boolean isHeldExclusively() {
            return getExclusiveOwnerThread() == Thread.currentThread();
        }

        final ConditionObject newCondition() {
            return new ConditionObject();
        }

  		//返回正在占用资源的线程
        final Thread getOwner() {
            return getState() == 0 ? null : getExclusiveOwnerThread();
        }

        final int getHoldCount() {
            return isHeldExclusively() ? getState() : 0;
        }

  		//资源是否已经被占用(已经在锁)
        final boolean isLocked() {
            return getState() != 0;
        }

        private void readObject(java.io.ObjectInputStream s)
            throws java.io.IOException, ClassNotFoundException {
            s.defaultReadObject();
            setState(0); // reset to unlocked state
        }
    }
```



#### NonfairSync非公平锁

```Java
static final class NonfairSync extends Sync {
        private static final long serialVersionUID = 7316153563782823691L;
		//加锁
        final void lock() {
          	//比较并更改锁的状态，如果当前state是0，则将state修改成1，并将获取锁的线程设置成当前线程
          	//如果上一个线程刚好结束state=0，这时线程过来就直接获取锁，而排队等待中的线程需要继续等待，
          	//这样可以提高系统的效率，因为要从阻塞队列中唤醒线程需要耗费时间
            if (compareAndSetState(0, 1))
                setExclusiveOwnerThread(Thread.currentThread());
            else
            // 如果state！=0，即已经被占用，则调用获取锁的方法，acquire是AQS的方法
                acquire(1);
        }
		//尝试获取锁
        protected final boolean tryAcquire(int acquires) {
            return nonfairTryAcquire(acquires);
        }
    }
```



#### FairSync公平锁

```Java
static final class FairSync extends Sync {
        private static final long serialVersionUID = -3000897897090466540L;
		//公平锁加锁，直接放入等候队里中
        final void lock() {
            acquire(1);
        }

        protected final boolean tryAcquire(int acquires) {
            final Thread current = Thread.currentThread();
            int c = getState();
            if (c == 0) {
                //hasQueuedPredecessors ：查询是否还有线程比当前线程等候的时间更长，
              	//即前面队列中是否还有等候吧的线程,如果没有则当前线程获取锁
                if (!hasQueuedPredecessors() &&
                    compareAndSetState(0, acquires)) {
                  	//设置当前线程为获取资源的线程
                    setExclusiveOwnerThread(current);
                    return true;
                }
            }
            else if (current == getExclusiveOwnerThread()) {
                int nextc = c + acquires;
                if (nextc < 0)
                    throw new Error("Maximum lock count exceeded");
                setState(nextc);
                return true;
            }
            return false;
        }
    }
```



#### ReentrantLock构造方法

ReentrantLock默认是创建非公平锁

```Java
 public ReentrantLock() {
        sync = new NonfairSync();
    }
```

可以通过参数创建公平锁，参数为true时创建公平锁，参数false时创建非公平锁

```Java
public ReentrantLock(boolean fair) {
        sync = fair ? new FairSync() : new NonfairSync();
    }
```



#### ReentrantLock方法

* 加锁，如果已被占用则加入等候队列中

  ```Java
  public void lock() {
          sync.lock();
  }
  ```

* 尝试获取锁，如果获取不到就直接返回

  ```Java
  public boolean tryLock() {
          return sync.nonfairTryAcquire(1);
      }
  ```

* 尝试去获取锁，如果在timeout这段时间内没有获取到就直接返回

  ```Java
  public boolean tryLock(long timeout, TimeUnit unit)
              throws InterruptedException {
          return sync.tryAcquireNanos(1, unit.toNanos(timeout));
      }
  ```

* 释放锁

  ```Java
  public void unlock() {
          sync.release(1);
      }
  ```

* 返回当前线程占有的数量即state值

  ```Java
  public int getHoldCount() {
          return sync.getHoldCount();
      }
  ```

* 判断占有锁的线程是否是当前线程

  ```Java
  public boolean isHeldByCurrentThread() {
          return sync.isHeldExclusively();
      }
  ```

* 判断资源是否加锁

  ```Java
  public boolean isLocked() {
          return sync.isLocked();
      }
  ```

* 判断是否是公平锁

  ```Java
  public final boolean isFair() {
          return sync instanceof FairSync;
      }
  ```

* 返回占有该锁的线程

  ```Java
  protected Thread getOwner() {
          return sync.getOwner();
      }
  ```

* 判断等候队列中是否还有线程

  ```Java
  public final boolean hasQueuedThreads() {
          return sync.hasQueuedThreads();
      }
  ```

* 判断该线程是否在等候队列中

  ```Java
  public final boolean hasQueuedThread(Thread thread) {
          return sync.isQueued(thread);
      }
  ```

* 返回等候队列的长度

  ```Java
  public final int getQueueLength() {
          return sync.getQueueLength();
      }
  ```

* 返回所有的等候线程

  ```Java
  protected Collection<Thread> getQueuedThreads() {
          return sync.getQueuedThreads();
      }
  ```






ReentrantLock使用

```Java
import java.util.concurrent.locks.ReentrantLock;

public class Test
{
	private static int num = 0;
	static ReentrantLock lock = new ReentrantLock();

	public static void main(String[] args)
	{
		for (int i = 0; i < 3; i++)
			new Thread(new MyRunnable()).start();
	}

	public static void lockBlock()
	{
		try
		{
			lock.lock();
			for (int i = 0; i < 3; i++)
				System.out.println(Thread.currentThread().getName() + " " + num++);
			System.out.println();
			Thread.sleep(500);

		} catch (InterruptedException e)
		{
			e.printStackTrace();
		} finally
		{
			lock.unlock();
		}
	}
}

class MyRunnable implements Runnable
{
	@Override
	public void run()
	{
		Test.lockBlock();
	}
}

```