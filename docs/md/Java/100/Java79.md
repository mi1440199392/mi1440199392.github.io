# 前言

<font face="幼圆">

> `ReentrantReadWriteLock`读写锁

</font>

# 概述

<font face="幼圆">

> `ReentrantLock`是排他锁，在同一时刻只允许一个线程进行访问，而读写锁在同一时刻可以允许多个读线程访问，但是在写线程访问时，所有的读线程和其他写线程均被阻塞。
> 读写锁维护了一对锁，一个读锁和一个写锁，通过分离读锁和写锁，使得并发性相比一般的排他锁有了很大提升。
>
> 一般情况下，读写锁的性能都会比排它锁好，因为大多数场景读是多于写的。在读多于写的情况下，读写锁能够提供比排它锁更好的并发性和吞吐量。
>
> `ReentrantReadWriteLock`支持以下功能：
> - 支持公平和非公平的获取锁的方式
> - 支持可重入，读线程在获取了读锁后还可以获取读锁；写线程在获取了写锁之后既可以再次获取写锁又可以获取读锁
> - 还允许从写入锁降级为读取锁，其实现方式是：先获取写入锁，然后获取读取锁，最后释放写入锁。但是，从读取锁升级到写入锁是不允许的
> - 读取锁和写入锁都支持锁获取期间的中断

</font>

# 写锁的获取与释放

<font face="幼圆">

> 写锁是一个支持重进入的排它锁。如果当前线程已经获取了写锁，则增加写状态。如果当前线程在获取写锁时，读锁已经被获取`（读状态不为0）`或者该线程不是已经获取写锁的线程，则当前线程进入等待状态

</font>

```java 
protected final boolean tryAcquire(int acquires) {
    Thread current = Thread.currentThread();
    int c = getState();
    int w = exclusiveCount(c);
    if (c != 0) {
        // 存在读锁或者当前获取线程不是已经获取写锁的线程
        if (w == 0 || current != getExclusiveOwnerThread()) return false;
        if (w + exclusiveCount(acquires) > MAX_COUNT) throw new Error("Maximum lock count exceeded");

        setState(c + acquires);
        return true;
    }
    if (writerShouldBlock() || !compareAndSetState(c, c + acquires)) return false;
    setExclusiveOwnerThread(current);
    return true;
}
```

<font face="幼圆">

> 该方法除了重入条件（当前线程为获取了写锁的线程）之外，增加了一个读锁是否存在的判断。如果存在读锁，则写锁不能被获取，原因在于：读写锁要确保写锁的操作对读锁可见，如
果允许读锁在已被获取的情况下对写锁的获取，那么正在运行的其他读线程就无法感知到当前写线程的操作。因此，只有等待其他读线程都释放了读锁，写锁才能被当前线程获取，而写
锁一旦被获取，则其他读写线程的后续访问均被阻塞。
> 
> 写锁的释放与`ReentrantLock`的释放过程基本类似，每次释放均减少写状态，当写状态为`0`时表示写锁已被释放，从而等待的读写线程能够继续访问读写锁，同时前次写线程的修改对后续读写线程可见。

</font>


# 读锁的获取与释放


<font face="幼圆">

> 读锁是一个支持重进入的共享锁，它能够被多个线程同时获取，在没有其他写线程访问`（或者写状态为0）`时，读锁总会被成功地获取，而所做的也只是（线程安全的）增加读状态。
> 如果当前线程已经获取了读锁，则增加读状态。如果当前线程在获取读锁时，写锁已被其他线程获取，则进入等待状态。获取读锁的实现从`Java5`到`Java6`变得复杂许多，主要原因是新增了一
> 些功能，例如`getReadHoldCount()`方法，作用是返回当前线程获取读锁的次数。读状态是所有线程获取读锁次数的总和，而每个线程各自获取读锁的次数只能选择保存在`ThreadLocal`中，由线程自身维护，这使获取读锁的实现变得复杂。
>
> 在`tryAcquireShared(int unused)`方法中，如果其他线程已经获取了写锁，则当前线程获取读锁失败，进入等待状态。
> 如果当前线程获取了写锁或者写锁未被获取，则当前线程（线程安全，依靠`CAS`保证）增加读状态，成功获取读锁。
> 读锁的每次释放（线程安全的，可能有多个读线程同时释放读锁）均减少读状态，减少的值是`（1<<16）`。

</font>

# 锁降级

<font face="幼圆">

> 锁降级指的是写锁降级成为读锁。如果当前线程拥有写锁，然后将其释放，最后再获取读锁，这种分段完成的过程不能称之为锁降级。锁降级是指把持住（当前拥有的）写锁，再获取到读锁，随后释放（先前拥有的）写锁的过程。
> 接下来看一个锁降级的示例。因为数据不常变化，所以多个线程可以并发地进行数据处理，当数据变更后，如果当前线程感知到数据变化，则进行数据的准备工作，同时其他处理线程被阻塞，直到当前线程完成数据的准备工作
> 
> `锁降级中读锁的获取是否必要呢？`答案是必要的。主要是为了保证数据的可见性，如果当前线程不获取读锁而是直接释放写锁，假设此刻另一个线程`（记作线程T）`获取了写锁并修改了数据，那么当前线程无法感知线程T的数据更新。如果当前线程获取读锁，即遵循锁降级的步骤，则`线程T`将会被阻塞，直到当前线程使用数据并释放读锁之后，`线程T`才能获取写锁进行数据更新

</font>


# 示例

```java 
package com.alibaba.frame.b;
import java.util.concurrent.TimeUnit;
import java.util.concurrent.locks.ReentrantReadWriteLock;

/**
 * @author hspcadmin
 * @version 1.0
 * @description ReentrantReadWriteLock读写锁
 */
public class B_105 {
	private static final ReentrantReadWriteLock base = new ReentrantReadWriteLock();
	private static final ReentrantReadWriteLock.ReadLock readLock = base.readLock();
	private static final ReentrantReadWriteLock.WriteLock writeLock = base.writeLock();
	private static volatile int numInt = 0;
	private static volatile int numLong = 0;

	public static void main(String[] args) throws Exception {

		final Task task1 = new Task("A");
		final Task task2 = new Task("B");
		task1.start();
		task2.start();

		task1.join();
		task2.join();
		final String name = Thread.currentThread().getName();
		System.out.println("线程：" + name + "，numInt = " + numInt);
		System.out.println("线程：" + name + "，numLong = " + numLong);
	}

	static class Task extends Thread {
		public Task(String name) {
			super(name);
		}

		@Override
		public void run() {
			try {
				readLock.lock();
				final String name = Thread.currentThread().getName();
				System.out.println("线程：" + name + "，获取读锁...");
				System.out.println("线程：" + name + "，正在执行读任务开始...");
				System.out.println("线程：" + name + "，numInt = " + numInt);
				System.out.println("线程：" + name + "，numLong = " + numLong);
				TimeUnit.SECONDS.sleep(3);
				System.out.println("线程：" + name + "，正在执行读任务结束...\n");
				readLock.unlock();

				writeLock.lock();
				System.out.println("线程：" + name + "，获取写锁...");
				System.out.println("线程：" + name + "，正在执行写任务开始...");
				numInt++;
				numLong++;
				TimeUnit.SECONDS.sleep(3);
				System.out.println("线程：" + name + "，正在执行写任务结束...\n");

				readLock.lock();
				System.out.println("线程：" + name + "，再次获取读锁...\n");
			} catch (InterruptedException e) {
			} finally {
				writeLock.unlock();
				readLock.unlock();
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
线程：A，获取读锁...
线程：B，获取读锁...
线程：A，正在执行读任务开始...
线程：B，正在执行读任务开始...
线程：B，numInt = 0
线程：A，numInt = 0
线程：A，numLong = 0
线程：B，numLong = 0
线程：B，正在执行读任务结束...

线程：A，正在执行读任务结束...

线程：B，获取写锁...
线程：B，正在执行写任务开始...
线程：B，正在执行写任务结束...

线程：B，再次获取读锁...

线程：A，获取写锁...
线程：A，正在执行写任务开始...
线程：A，正在执行写任务结束...

线程：A，再次获取读锁...

线程：main，numInt = 2
线程：main，numLong = 2
```
---

<font face="幼圆">

!> 水平有限，再接再厉!!!

</font>

