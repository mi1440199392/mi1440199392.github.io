# 前言

<font face="幼圆">

> `Thread.join()`的使用

</font>

# 代码演示

```java 
package com.alibaba.frame.b;
import java.util.concurrent.TimeUnit;

/**
 * @author hspcadmin
 * @version 1.0
 * @description Thread.join()的使用
 */
public class B_103 {
	public static void main(String[] args) throws Exception {

		final Thread sub = new Thread(() -> {
			final String name = Thread.currentThread().getName();
			System.out.println("线程：" + name + "，正在执行任务...");
			System.out.println("线程：" + name + "，睡眠3秒开始...");
			try {
				TimeUnit.SECONDS.sleep(3);
			} catch (InterruptedException e) {
			}
			System.out.println("线程：" + name + "，睡眠3秒结束...");
		}, "A");
		sub.start();

		sub.join();
		final String name = Thread.currentThread().getName();
		System.out.println("线程：" + name + "，正在执行任务...");
	}
}
```

# 控制台

```text
线程：A，正在执行任务...
线程：A，睡眠3秒开始...
线程：A，睡眠3秒结束...
线程：main，正在执行任务...
```

# join()方法源码

<font face="幼圆">

> 从`join()`方法的源码中可以看到几个重要信息，首先`join()`方法默认是等待0毫秒；`join(long millis)`方法是一个`synchronized`方法；循环判断当前线程是否还活着。什么意思呢？
>  - `Main`线程在调用`线程A`的`join()`方法时，会先获取`对象A`的锁；
>  - 在`join()`方法中会调用`对象A`的`wait()`方法等待，而`wait()`方法会释放`对象A`的锁，并且`Main线程`在执行完`wait()`之后会进入阻塞状态；
>  - 最后`Main线程`在被`notify`唤醒之后，需要再循环判断`对象A`是否还活着，如果还活着会再次执行`wait()`；
>  - 而在线程执行完`run()`方法之后，`JVM`会调用该线程的`exit()`方法，通过`notifyAll()`唤醒处于等待状态的线程。

</font>

```java 
// synchronized修饰，锁住调用join()方法的当前对象A，Thread.currentThread()是Main线程；Main线程拿到对象A的锁
public final synchronized void join(long millis) throws InterruptedException {
    long base = System.currentTimeMillis();
    long now = 0;

    if (millis < 0) {
        throw new IllegalArgumentException("timeout value is negative");
    }

    if (millis == 0) {
        // isAlive()方式是实例方法，判断线程对象A是否存活
        while (isAlive()) {
            // join()方法在主线程Main执行的，Thread.currentThread()是Main线程；Main线程内调用对象A的wait()方法，表示Main线程开始阻塞等待，Main线程释放对象A的锁
            wait(0);
        }
    } else {
        while (isAlive()) {
            long delay = millis - now;
            if (delay <= 0) {
                break;
            }
            wait(delay);
            now = System.currentTimeMillis() - base;
        }
    }
}
```

```java 
private void exit() {
    if (group != null) {
        // 终止group中的线程this
        group.threadTerminated(this);
        group = null;
    }
    /* Aggressively null out all reference fields: see bug 4006245 */
    target = null;
    /* Speed the release of some of these resources */
    threadLocals = null;
    inheritableThreadLocals = null;
    inheritedAccessControlContext = null;
    blocker = null;
    uncaughtExceptionHandler = null;
}

void threadTerminated(Thread t) {
    synchronized (this) {
        remove(t);

        if (nthreads == 0) {
            // 唤醒等待线程
            notifyAll();
        }
        if (daemon && (nthreads == 0) &&
            (nUnstartedThreads == 0) && (ngroups == 0))
        {
            destroy();
        }
    }
}
```



