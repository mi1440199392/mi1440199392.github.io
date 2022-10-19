```java
package com.hundsun.demo.list.b;

/**
 * @Description 适配器模式
 * @Date 2022/7/22
 * @Created root
 */
public class B_105 {
    public static void main(String[] args) {
        AdapterA adapterA = new AdapterA(new B());
        adapterA.run();

    }

    static class AdapterA {
        private B b;

        public AdapterA(B b) {
            this.b = b;
        }

        public void run() {
            b.play();
        }
    }


    static class A {
        public void run() {
            B b = new B();
            b.play();
        }
    }

    static class B {
        public void play() {
            System.out.println("调用B对象执行play方法");
        }
    }
}
```

> 控制台打印

```java
调用B对象执行play方法
```