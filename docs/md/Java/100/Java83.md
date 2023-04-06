# 前言

<font face="幼圆">

> `JDK`并发工具类`Exchanger`

</font>

# 线程间交换数据的Exchanger

<font face="幼圆">

> `Exchanger(交换者)`是一个用于线程间协作的工具类。`Exchanger`用于进行线程间的数据交换。
> 它提供一个同步点，在这个同步点，两个线程可以交换彼此的数据。这两个线程通过`exchange()`方法交换数据，如果第一个线程先执行`exchange()`方法，它会一直等待第二个线程也执行`exchange()`方法，当两个线程都到达同步点时，这两个线程就可以交换数据，将本线程生产出来的数据传递给对方。
> 
> 如果两个线程有一个没有执行`exchange()`方法，则会一直等待，如果担心有特殊情况发生，避免一直等待，可以使用`exchange(V x，longtimeout，TimeUnit unit)`设置最大等待时长

</font>

# 代码

```java
package com.alibaba.frame.b;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import java.util.concurrent.Exchanger;

/**
 * @author hspcadmin
 * @version 1.0
 * @description 线程间协作的工具类Exchanger
 */
public class B_115 {
	private static final Exchanger<String> exchanger = new Exchanger<>();
	private static final Logger logger = LoggerFactory.getLogger(B_115.class);

	public static void main(String[] args) {
	
		new Task("A", "准备现金10元，买西瓜").start();
		new Task("B", "卖掉西瓜，收到现金10元").start();
	}

	static class Task extends Thread {
		private final String local;

		public Task(String name, String local) {
			super(name);
			this.local = local;
		}

		@Override
		public void run() {

			try {
				final String name = Thread.currentThread().getName();
				System.out.println("线程：" + name + "，交换自己的数据：" + local);
				final String exchange = exchanger.exchange(local);
				System.out.println("线程：" + name + "，获取别人交换的数据：" + exchange);
			} catch (InterruptedException e) {
				logger.error("异常：{}", e.toString());
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
线程：A，交换自己的数据：准备现金10元，买西瓜
线程：B，交换自己的数据：卖掉西瓜，收到现金10元
线程：B，获取别人交换的数据：准备现金10元，买西瓜
线程：A，获取别人交换的数据：卖掉西瓜，收到现金10元
```

