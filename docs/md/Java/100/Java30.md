```java
package com.hundsun.demo.list.d;

/**
 * @Desc 枚举实现单例
 * @Date 2022/9/9
 * @Created root
 */
public class D_106 {
    private int id;
    private String name;

    private D_106() {
    }

    public static enum SINGLE {
        ONE;
        private D_106 d106l;

        SINGLE() {
            d106l = new D_106();
        }

        public D_106 getInstance() {
            return d106l;
        }
    }

    public static void main(String[] args) {

        D_106 oneInstance = D_106.SINGLE.ONE.getInstance();
        D_106 twoInstance = D_106.SINGLE.ONE.getInstance();

        System.out.println(oneInstance == twoInstance);
    }
}

```

> 控制台打印

```java
true
```