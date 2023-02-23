```java 
package com.hundsun.frameworlk.http.config;

/**
 * @author hspcadmin
 * @version 1.0
 * @description 每个Java应用程序都有一个Runtime类的Runtime，允许应用程序与运行应用程序的环境进行接口
 */
public class RuntimeTest {
	public static void main(String[] args) {
		final Runtime runtime = Runtime.getRuntime();

		// 注册一个新的虚拟机关机挂钩
		runtime.addShutdownHook(MyThread.currentThread());
	}

	static class MyThread extends Thread {
		private static final Thread currentThread = new MyThread();

		private MyThread() {
		}

		public static Thread currentThread() {
			return currentThread;
		}

		@Override
		public void run() {
			System.out.println("关闭...");
		}
	}
}
```

```java 
关闭...
```
