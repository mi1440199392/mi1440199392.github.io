# 前言

<font face="幼圆">

> `JDK`并发工具类`Semaphore`

</font>

# 控制并发线程数的Semaphore

<font face="幼圆">

> `Semaphore(信号量)`是用来控制同时访问特定资源的线程数量，它通过协调各个线程，以保证合理的使用公共资源。
> 
> 把它比作是控制流量的红绿灯。比如`xx马路`要限制流量，只允许同时有`一百辆车`在这条路上行使，其他的都必须在路口等待，所以前一百辆车会看到绿灯，可以开进这条马路，后面的车会看到红灯，不能驶入`xx马路`，但是如果前一百辆中有`5辆车`已经离开了`xx马路`，那么后面就允许有`5辆车`驶入马路，这个例子里说的车就是线程，驶入马路就表示线程在执行，离开马路就表示线程执行完成，看见红灯就表示线程被阻塞，不能执行。

</font>


# 代码

<font face="幼圆">

> 在代码中，虽然有`20`个线程在执行，但是只允许`5`个并发执行。`Semaphore`的构造方法`Semaphore(int permits)`接受一个整型的数字，表示可用的许可证数量。
> 
> `Semaphore(5)`表示允许`5`个线程获取许可证，也就是最大并发数是`5`。`Semaphore`的用法也很简单，首先线程使用`Semaphore`的`acquire()`方法获取一个许可证，使用完之后调用`release()`方法归还许可证。

</font>

```java 
package com.alibaba.frame.b;
import java.util.concurrent.Semaphore;
import java.util.concurrent.atomic.AtomicInteger;

/**
 * @author hspcadmin
 * @version 1.0
 * @description Semaphore信号量
 */
public class B_114 {
	public static final Semaphore semaphore = new Semaphore(5);
	public static AtomicInteger atomicInteger = new AtomicInteger(0);

	public static void main(String[] args) {
		for (int i = 0; i < 20; i++) {
			new Task().start();
		}
	}

	static class Task extends Thread {
		@Override
		public void run() {
			try {
				// 获取许可证
				semaphore.acquire();
				final String name = Thread.currentThread().getName();
				System.out.println("线程：" + name + "，正在执行任务，数字自增，" + atomicInteger.incrementAndGet());
			} catch (InterruptedException e) {
				throw new RuntimeException(e);
			} finally {
				// 释放许可证
				semaphore.release();
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
线程：Thread-0，正在执行任务，数字自增，1
线程：Thread-4，正在执行任务，数字自增，5
线程：Thread-3，正在执行任务，数字自增，4
线程：Thread-2，正在执行任务，数字自增，3
线程：Thread-1，正在执行任务，数字自增，2
线程：Thread-6，正在执行任务，数字自增，7
线程：Thread-8，正在执行任务，数字自增，8
线程：Thread-5，正在执行任务，数字自增，6
线程：Thread-7，正在执行任务，数字自增，9
线程：Thread-9，正在执行任务，数字自增，10
线程：Thread-10，正在执行任务，数字自增，11
线程：Thread-11，正在执行任务，数字自增，12
线程：Thread-12，正在执行任务，数字自增，13
线程：Thread-13，正在执行任务，数字自增，14
线程：Thread-14，正在执行任务，数字自增，15
线程：Thread-15，正在执行任务，数字自增，16
线程：Thread-16，正在执行任务，数字自增，17
线程：Thread-17，正在执行任务，数字自增，18
线程：Thread-18，正在执行任务，数字自增，19
线程：Thread-19，正在执行任务，数字自增，20
```
