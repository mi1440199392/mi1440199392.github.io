```java
package com.hundsun.demo.list.d;
import java.util.Stack;

/**
 * @Desc 栈Stack
 * @Date 2022/9/8
 * @Created root
 */
public class D {
    public static void main(String[] args) {
        Stack<String> source = new Stack<String>() {{
            push("走第一步");
            push("走第二步");
            push("走第三步");
            push("走第四步");
        }};

        System.out.println(source);

        Stack<String> target = new Stack<String>() {{
            while (!source.empty()) {
                push(source.pop());
            }
        }};

        System.out.println(target);
    }
}
```

> 控制台打印

```java
[走第一步, 走第二步, 走第三步, 走第四步]
[走第四步, 走第三步, 走第二步, 走第一步]
```