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

		// 返回可用于Java虚拟机的处理器数量
		final int processors = runtime.availableProcessors();
		System.out.println("返回可用于Java虚拟机的处理器数量 = " + processors);
	}
}
```

!> 返回操作系统`CPU`数量，常作为线程池核心线程数的参数：操作系统`CPU`数 * 2

```java
返回可用于Java虚拟机的处理器数量 = 8
```
