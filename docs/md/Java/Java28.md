```java
package com.hundsun.demo.list.d;
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;
import java.util.Arrays;

/**
 * @Desc 动态代理
 * @Date 2022/9/13
 * @Created root
 */
public class D_108 {
    public static void main(String[] args) {

        Student instance = (Student) Proxy.newProxyInstance(ClassLoader.getSystemClassLoader(), new Class[]{Student.class}, new InvocationHandler() {
            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                System.out.println("前置执行...");
                System.out.println("proxy = " + proxy.getClass());
                System.out.println(method.getName());
                System.out.println("Arrays.toString(args) = " + Arrays.toString(args));

                return null;
            }
        });

        instance.show(1000);
    }

    interface Student {
        void show(int i);
    }
}
```

> 控制台打印

```java
前置执行...
proxy = class com.hundsun.demo.list.d.$Proxy0
show
Arrays.toString(args) = [1000]
```