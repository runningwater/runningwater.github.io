---
layout: post
title: Java 并发包中 Semaphore
categories: blog
description: Java 集合类
keywords: Semaphore,Java,Currency,信号量
---

在多线程程序设计中有三个同步工具需要我们掌握，分别是`Semaphore(信号量)`，`countDownLatch（倒计数门闸锁）`，`CyclicBarrier(可重用栅栏)`

**目录**

* TOC
{:toc}




## 信号量Semaphore的介绍

我们以一个停车场运作为例来说明信号量的作用。假设停车场只有三个车位，一开始三个车位都是空的。这时如果同时来了三辆车，看门人允许其中它们进入进入，然后放下车拦。以后来的车必须在入口等待，直到停车场中有车辆离开。这时，如果有一辆车离开停车场，看门人得知后，打开车拦，放入一辆，如果又离开一辆，则又可以放入一辆，如此往复。


在这个停车场系统中，车位是公共资源，每辆车好比一个线程，**看门人**起的就是 **信号量** 的作用。**信号量是一个非负整数**，表示了当前公共资源的可用数目（在上面的例子中可以用空闲的停车位类比信号量），当一个线程要使用公共资源时（在上面的例子中可以用车辆类比线程），首先要查看信号量，如果信号量的值大于1，则将其减1，然后去占有公共资源。如果信号量的值为0，则线程会将自己阻塞，直到有其它线程释放公共资源。


在信号量上我们定义两种操作： `acquire（获取）` 和 `release（释放）`。当一个线程调用acquire操作时，它要么通过成功获取信号量（信号量减1），要么一直等下去，直到有线程释放信号量，或超时。release（释放）实际上会将信号量的值加1，然后唤醒等待的线程。

信号量主要用于两个目的，一个是用于多个共享资源的互斥使用，另一个用于并发线程数的控制。

## 信号量Semaphore的源码分析

在 Java 的并发包中，`Semaphore` 类表示**信号量**。Semaphore 内部主要通过 `AQS（AbstractQueuedSynchronizer）`实现线程的管理。Semaphore 有两个构造函数，参数 permits 表示许可数，它最后传递给了 AQS 的 state 值。

线程在运行时首先获取许可，如果成功，许可数就**减 1**，线程运行，当线程运行结束就释放许可，许可数就**加1**。如果许可数为0，则获取失败，线程位于AQS的等待队列中，它会被其它释放许可的线程唤醒。

在创建Semaphore对象的时候还可以指定它的**公平性**。一般常用非公平的信号量，非公平信号量是指在获取许可时先尝试获取许可，而不必关心是否已有需要获取许可的线程位于等待队列中，如果获取失败，才会入列。而公平的信号量在获取许可时首先要查看等待队列中是否已有线程，如果有则入列。

构造函数源代码

	//非公平的构造函数
	public Semaphore(int permits) {
	    sync = new NonfairSync(permits);
	}
	
	//通过fair参数决定公平性
	public Semaphore(int permits, boolean fair) {
	    sync = fair ? new FairSync(permits) : new NonfairSync(permits);
	}
	

acquire源代码

```Java
	public void acquire() throws InterruptedException {
	    sync.acquireSharedInterruptibly(1);
	}
	
	public final void acquireSharedInterruptibly(int arg) throws InterruptedException {
	    if (Thread.interrupted())
	        throw new InterruptedException();
	    if (tryAcquireShared(arg) < 0)
	        doAcquireSharedInterruptibly(arg);
	}
	
	final int nonfairTryAcquireShared(int acquires) {
	    for (;;) {
	        int available = getState();
	        int remaining = available - acquires;
	        if (remaining < 0 ||
	            compareAndSetState(available, remaining))
	            return remaining;
	    }
	}
```
	

可以看出，如果remaining < 0 即获取许可后，许可数小于0，则获取失败，在 `doAcquireSharedInterruptibly` 方法中线程会将自身阻塞，然后入列。

release源代码

```Java
public void release() {
    sync.releaseShared(1);
}

public final boolean releaseShared(int arg) {
    if (tryReleaseShared(arg)) {
        doReleaseShared();
        return true;
    }
    return false;
}

protected final boolean tryReleaseShared(int releases) {
    for (;;) {
        int current = getState();
        int next = current + releases;
        if (next < current) // overflow
           throw new Error("Maximum permit count exceeded");
        if (compareAndSetState(current, next))
            return true;
    }
}
```

可以看出释放许可就是将`AQS` 中 `state` 的值加1。然后通过`doReleaseShared`唤醒等待队列的第一个节点。可以看出`Semaphore`使用的是 `AQS` 的共享模式，等待队列中的第一个节点，如果第一个节点成功获取许可，又会唤醒下一个节点，以此类推。

## 使用示例

```Java
package javalearning;

import java.util.Random;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Semaphore;

public class SemaphoreDemo {
	private Semaphore smp = new Semaphore(3); 
	private Random rnd = new Random();
	
	class TaskDemo implements Runnable{
		private String id;
		TaskDemo(String id){
			this.id = id;
		}
		@Override
		public void run(){
			try {
				smp.acquire();
				System.out.println("Thread " + id + " is working");
				Thread.sleep(rnd.nextInt(1000));
				smp.release();
				System.out.println("Thread " + id + " is over");
			} catch (InterruptedException e) {
			}
		}
	}
	
	public static void main(String[] args){
		SemaphoreDemo semaphoreDemo = new SemaphoreDemo();
		//注意我创建的线程池类型，
		ExecutorService se = Executors.newCachedThreadPool();
		se.submit(semaphoreDemo.new TaskDemo("a"));
		se.submit(semaphoreDemo.new TaskDemo("b"));
		se.submit(semaphoreDemo.new TaskDemo("c"));
		se.submit(semaphoreDemo.new TaskDemo("d"));
		se.submit(semaphoreDemo.new TaskDemo("e"));
		se.submit(semaphoreDemo.new TaskDemo("f"));
		se.shutdown();
	}
}
```

运行结果

	Thread c is working
	
	Thread b is working
	
	Thread a is working
	
	Thread c is over
	
	Thread d is working
	
	Thread b is over
	
	Thread e is working
	
	Thread a is over
	
	Thread f is working
	
	Thread d is over
	
	Thread e is over
	
	Thread f is over

可以看出，最多同时有三个线程并发执行，也可以认为有三个公共资源（比如计算机的三个串口）。


