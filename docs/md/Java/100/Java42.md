```java
package com.hundsun.demo.list.a;

/**
 * @Classname A_100
 * @Description
 * @Date 2022/6/23 14:32
 * @Created hspcadmin
 */
public class A_100 {
    public static void main(String[] args) {

        Character character = null;

        /**
         *
         *   LINENUMBER 15 L1
         *     ALOAD 1
         *     INVOKEVIRTUAL java/lang/Character.charValue ()C
         *     LOOKUPSWITCH
         *       49: L2
         *       50: L3
         *       default: L4
         *    L2
         *
         *  switch 字符包装类的时候, 实际上调用Character.charValue ()进行拆箱操作, 会发生空指针问题
         */
        switch (character) {
            case '1':
                System.out.println("111111");
                break;
            case '2':
                System.out.println("222222");
                break;
            default:
                break;
        }

        String str = null;

        // 实际是调用的 String.equals 对匹配项一一匹配,  会报空指针
        switch (str) {
            case "1":
                System.out.println("111111");
                break;
            case "2":
                System.out.println("222222");
                break;
            default:
                break;
        }
    }
}
```

> 控制台打印

```java
Exception in thread "main" java.lang.NullPointerException
	at com.hundsun.demo.list.a.A_100.main(A_100.java:29)
```