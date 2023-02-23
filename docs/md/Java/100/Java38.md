```java
package com.hundsun.demo.list.c;

/**
 * @Desc 代理模式
 * @Date 2022/9/6
 * @Created root
 */
public class C_104 {
    public static void main(String[] args) {
        ProxyObject proxyObject = new ProxyObject(new TargetObject());
        proxyObject.run();
    }


    // 抽象层
    static abstract class abstractObject {

        abstract void run();
    }

    // 目标类
    static class TargetObject extends abstractObject {
        @Override
        void run() {
            System.out.println("类TargetObject 执行 run()方法...");
        }
    }

    // 代理类
    static class ProxyObject extends abstractObject {
        private TargetObject targetObject;

        public ProxyObject(TargetObject targetObject) {
            this.targetObject = targetObject;
        }

        private void beforeRun() {
            System.out.println("run()方法之前，执行beforeRun()方法");
        }

        @Override
        void run() {
            beforeRun();
            targetObject.run();
            afterRun();
        }

        private void afterRun() {
            System.out.println("run()方法之后，执行afterRun()方法");
        }
    }
}
```

> 控制台打印

```java
run()方法之前，执行beforeRun()方法
类TargetObject 执行 run()方法...
run()方法之后，执行afterRun()方法
```