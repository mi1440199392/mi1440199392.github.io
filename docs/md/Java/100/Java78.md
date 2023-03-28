# 前言

<font face="幼圆">

> `ReentrantLock`可重入锁

</font>

# 定义

<font face="幼圆">

> 重入锁`ReentrantLock`，顾名思义，就是支持重进入的锁，它表示该锁能够支持一个线程对资源的重复加锁。
>
> `ReentrantLock`虽然没能像`synchronized`关键字一样支持隐式的重进入，但是在调用`lock()`
> 方法时，已经获取到锁的线程，能够再次调用`lock()`方法获取锁而不被阻塞。
>
> 实现重进入：重进入是指任意线程在获取到锁之后能够再次获取该锁而不会被锁所阻塞，该特性的实现需要解决以下两个问题
> - 线程再次获取锁：锁需要去识别获取锁的线程是否为当前占据锁的线程，如果是，则再次成功获取。
> - 锁的最终释放：线程重复n次获取了锁，随后在第n次释放该锁后，其他线程能够获取到该锁。锁的最终释放要求锁对于获取进行计数自增，计数表示当前锁被重复获取的次数，而锁被释放时，计数自减，当计数等于0时表示锁已经成功释放。

</font>

# 测试ReentrantLock可重入

```java 
package com.alibaba.frame.b;
import java.util.concurrent.TimeUnit;
import java.util.concurrent.locks.ReentrantLock;

/**
 * @author hspcadmin
 * @version 1.0
 * @description ReentrantLock可重入锁
 */
public class B_104 {
	private static final ReentrantLock lock = new ReentrantLock();
	private static volatile int numLong = 0;
	private static volatile int numInt = 0;

	public static void main(String[] args) throws InterruptedException {

		final Task task1 = new Task("A");
		final Task task2 = new Task("B");
		task1.start();
		task2.start();


		task1.join();
		task2.join();
		System.out.println("numLong = " + numLong);
		System.out.println("numInt = " + numInt);
	}


	static class Task extends Thread {
		public Task(String name) {
			super(name);
		}

		@Override
		public void run() {
			try {
		        // 加2次锁
				lock.lock();
				final String name = Thread.currentThread().getName();
				System.out.println("线程：" + name + "，正在执行任务，开始第 1 次加锁...");
				System.out.println("线程：" + name + "，正在执行任务，操作 numLong 开始...");
				numLong++;
				TimeUnit.SECONDS.sleep(3);
				System.out.println("线程：" + name + "，正在执行任务，操作 numLong 结束...\n");


				lock.lock();
				System.out.println("线程：" + name + "，正在执行任务，开始第 2 次加锁...");
				System.out.println("线程：" + name + "，正在执行任务，操作 numInt 开始...");
				numInt++;
				TimeUnit.SECONDS.sleep(3);
				System.out.println("线程：" + name + "，正在执行任务，操作 numInt 结束...\n");
			} catch (InterruptedException e) {
			} finally {
			    // 释放2次锁
				lock.unlock();
				lock.unlock();
			}
		}
	}
}
```
--- 

<font face="幼圆">

> 控制台

</font>

```text
线程：A，正在执行任务，开始第 1 次加锁...
线程：A，正在执行任务，操作 numLong 开始...
线程：A，正在执行任务，操作 numLong 结束...

线程：A，正在执行任务，开始第 2 次加锁...
线程：A，正在执行任务，操作 numInt 开始...
线程：A，正在执行任务，操作 numInt 结束...

线程：B，正在执行任务，开始第 1 次加锁...
线程：B，正在执行任务，操作 numLong 开始...
线程：B，正在执行任务，操作 numLong 结束...

线程：B，正在执行任务，开始第 2 次加锁...
线程：B，正在执行任务，操作 numInt 开始...
线程：B，正在执行任务，操作 numInt 结束...

numLong = 2
numInt = 2
```
