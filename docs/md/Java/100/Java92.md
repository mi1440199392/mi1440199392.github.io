# 前言

<font face="幼圆">

> 计算 HashCode 的常用工具

</font>

# 计算 HashCode 的常用工具

```java 
package com.alibaba.frame.jvm;
import java.util.Objects;
import org.springframework.util.ObjectUtils;

public class ComputeHashCodeDemo {
	private static final ComputeHashCodeDemo obj = new ComputeHashCodeDemo();

	public static void main(String[] args) {

		int hashCode_01 = obj.hashCode();
		System.out.println("hashCode_01 = " + hashCode_01);

		int hashCode_02 = System.identityHashCode(obj);
		System.out.println("hashCode_02 = " + hashCode_02);

		int hashCode_03 = ObjectUtils.nullSafeHashCode(obj);
		System.out.println("hashCode_03 = " + hashCode_03);

		int hashCode_04 = Objects.hashCode(obj);
		System.out.println("hashCode_04 = " + hashCode_04);
	}
}
```

# 控制台输出

```text
hashCode_01 = 1058025095
hashCode_02 = 1058025095
hashCode_03 = 1058025095
hashCode_04 = 1058025095
```