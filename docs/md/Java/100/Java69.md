# 前言

<font face="幼圆">

!> 垃圾回收器销毁对象前，运行对象的终结方法`finalize`

</font>


# 垃圾回收前，运行对象的终结方法finalize()

```java 
package com.alibaba.frame.a;
import java.util.concurrent.TimeUnit;

/**
 * @author hspcadmin
 * @version 1.0
 * @description 垃圾回收器销毁对象前，运行对象的终结方法finalize()
 */
public class A_114 {
	public static void main(String[] args) throws InterruptedException {

		// 创建对象
		A a = new A();
		// 对象不再被引用
		a = null;
		// 垃圾回收
		System.gc();

		TimeUnit.SECONDS.sleep(1);
	}

	static class A {
		@Override
		protected void finalize() throws Throwable {
			System.out.println("对象即将被回收...");
		}
	}
}
```

```text
对象即将被回收...
```
