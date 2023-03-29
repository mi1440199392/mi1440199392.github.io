# 前言

<font face="幼圆">

> 不存储元素的阻塞队列`SynchronousQueue`

</font>

# 概述

<font face="幼圆">

> `SynchronousQueue`是一个不存储元素的阻塞队列。每一个`put`操作必须等待一个`take`操作，否则不能继续添加元素。
> 
> 它支持公平访问队列。默认情况下线程采用非公平性策略访问队列。使用以下构造方法可以创建公平性访问的`SynchronousQueue`，如果设置为`true`，则等待的线程会采用先进先出的顺序访问队列。
> 
> `SynchronousQueue`可以看成是一个传球手，负责把生产者线程处理的数据直接传递给消费者线程。队列本身并不存储任何元素，非常适合传递性场景。`SynchronousQueue`的吞吐量高于`LinkedBlockingQueue`和`ArrayBlockingQueue`。

</font>

# 代码

```java 
package com.alibaba.frame.b;
import java.util.concurrent.SynchronousQueue;
import java.util.concurrent.TimeUnit;

/**
 * @author hspcadmin
 * @version 1.0
 * @description 不存储元素的阻塞队列SynchronousQueue
 */
public class B_110 {
	public static final SynchronousQueue<Integer> queue = new SynchronousQueue<>();

	public static void main(String[] args) {

		new SubTaskA("A").start();
		new SubTaskB("B").start();
	}

	static class SubTaskA extends Thread {
		public SubTaskA(String name) {
			super(name);
		}

		@Override
		public void run() {
			final String name = Thread.currentThread().getName();
			System.out.println("线程：" + name + "，正在取队列元素....");
			try {
				final Integer obj = queue.take();
				System.out.println("线程：" + name + "，已取队列元素：" + obj);
			} catch (InterruptedException e) {
			}
		}
	}

	static class SubTaskB extends Thread {
		public SubTaskB(String name) {
			super(name);
		}

		@Override
		public void run() {
			final String name = Thread.currentThread().getName();
			System.out.println("线程：" + name + "，正在存队列元素....");
			try {
				TimeUnit.SECONDS.sleep(3);
				queue.put(1001);
				System.out.println("线程：" + name + "，结束存队列元素");
			} catch (InterruptedException e) {
			}
		}
	}
}
```
---

```text
线程：A，正在取队列元素....
线程：B，正在存队列元素....
线程：B，结束存队列元素
线程：A，已取队列元素：1001
```

