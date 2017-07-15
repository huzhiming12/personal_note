---
title: 多线程的实现方式
date: 2017-04-15 19:34:41
categories: Java并发
tags: 
---



Thread.join()方法作用：也就是在子线程调用了join()方法后面的代码，只有等到子线程结束了才能执行。

- 1. 继承Thread,实现run方法

  ```Java
  public class ExtendThread extends Thread
  {
  	private int value;
  	@Override
  	public void run()
  	{
  		// TODO Auto-generated method stub
  		super.run();
  		for (int i = 0; i < 5; i++)
  		{
  			System.out.println(hashCode() + " " + value);
  			value++;
  		}
  	}
  	public static void main(String[] args)
  	{
  		ExtendThread main = new ExtendThread();
  		ExtendThread main2 = new ExtendThread();
  		main.start();
  		main2.start();
  	}
  }
  ```

- 2. 实现Runnable接口

  ```Java
  public class ImplementRunnable implements Runnable
  {
  	private int value;
  	@Override
  	public void run()
  	{
  		for (int i = 0; i < 5; i++)
  		{
  			System.out.println(hashCode() + "  " + value);
  			value++;
  		}
  	}
  	public static void main(String[] args)
  	{
  		ImplementRunnable runnable = new ImplementRunnable();
  		ImplementRunnable runnable2 = new ImplementRunnable();
  		new Thread(runnable).start();
  		new Thread(runnable2).start();
  	}
  }
  ```

- 3. 通过Callable和FutureTask实现

   FutureTask 继承自RunableFuture接口,RunableFuture继承了Runnable接口。即FutureTask间接继承了Runnable接口，FutureTask类中的run方法调用了Callable类中的call方法。

  ```Java
  public class CallAble implements Callable<Integer>
  {

  	@Override
  	public Integer call() throws Exception
  	{
  		for (int i = 0; i < 5; i++)
  		{
  			System.out.println(hashCode() + "  " + i);
  		}
  		return 111;
  	}
  	
  	public static void main(String[] args)
  	{
  		CallAble callAble1 = new CallAble();
  		FutureTask<Integer>futureTask1 = new FutureTask<>(callAble1);
  		new Thread(futureTask1).start();
        	CallAble callAble2 = new CallAble();
  		FutureTask<Integer>futureTask2 = new FutureTask<>(callAble2);
  		new Thread(futureTask2).start();
  	}
  }
  ```

- Executor线程池

  线程池可以预设容纳线程的个数，也可以动态分配。

  ```Java
  public class Executor
  {
  	private static void run(ExecutorService threadPool)
  	{
  		for (int i = 1; i < 5; i++)
  		{
  			final int taskID = i;
  			threadPool.execute(new Runnable()
  			{
  				@Override
  				public void run()
  				{
  					for (int i = 1; i < 5; i++)
  					{
  						try
  						{
  							Thread.sleep(20);// 为了测试出效果，让每次任务执行都需要一定时间
  						} catch (InterruptedException e)
  						{
  							e.printStackTrace();
  						}
  						System.out.println("第" + taskID + "次任务的第" + i + "次执行");
  					}
  				}
  			});
  		}
  		threadPool.shutdown();// 任务执行完毕，关闭线程池
  	}
  	public static void main(String[] args)
  	{
  		// 创建可以容纳3个线程的线程池
  		ExecutorService fixedThreadPool = Executors.newFixedThreadPool(2);
  		// 线程池的大小会根据执行的任务数动态分配
  		ExecutorService cachedThreadPool = Executors.newCachedThreadPool();
  		// 创建单个线程的线程池，如果当前线程在执行任务时突然中断，则会创建一个新的线程替代它继续执行任务
  		ExecutorService singleThreadPool = Executors.newSingleThreadExecutor();
  		// 效果类似于Timer定时器
  		ScheduledExecutorService scheduledThreadPool = 	
            Executors.newScheduledThreadPool(3);

  		run(fixedThreadPool);
  		//run(cachedThreadPool);
  		// run(singleThreadPool);
  		// run(scheduledThreadPool);
  	}
  }
  ```