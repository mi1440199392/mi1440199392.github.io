```java
package com.hundsun.demo.list.d;
import java.util.ArrayList;
import java.util.List;

/**
 * @Desc 模板方法模式
 * @Date 2022/9/9
 * @Created root
 */
public class D_104 {
    public static void main(String[] args) {
        PlayInterface playInterfaceImplA = new PlayInterfaceImplA();
        PlayInterface playInterfaceImplB = new PlayInterfaceImplB();
        PlayInterface playInterfaceImplC = new PlayInterfaceImplC();

        PlayInterfaceContext.add(playInterfaceImplA);
        PlayInterfaceContext.add(playInterfaceImplB);
        PlayInterfaceContext.add(playInterfaceImplC);

        PlayInterfaceContext.sum();
    }

    // 上下文环境
    final static class PlayInterfaceContext {
        private static List<PlayInterface> playInterfaces = new ArrayList<>(10);

        public static void add(PlayInterface playInterface) {
            PlayInterfaceContext.playInterfaces.add(playInterface);
        }

        public static void sum() {
            for (PlayInterface playInterface : playInterfaces) {
                playInterface.sum();
            }
        }
    }

    // 模板方法抽象
    interface PlayInterface {

        // 基础方法
        void run();

        // 基础方法
        void play();

        // 基础方法
        void sleep();

        // 钩子方法, 控制方法是否执行
        boolean isNext();

        // 模板方法
        default void sum() {
            run();
            play();
            if (isNext()) {
                sleep();
            }
        }
    }

    static class PlayInterfaceImplA implements PlayInterface {
        @Override
        public void run() {
            System.out.println("类PlayInterfaceImplA 执行run()方法... ");
        }

        @Override
        public void play() {
            System.out.println("类PlayInterfaceImplA 执行play()方法... ");
        }

        @Override
        public void sleep() {
            System.out.println("类PlayInterfaceImplA 执行sleep()方法... ");
        }

        @Override
        public boolean isNext() {
            return true;
        }
    }

    static class PlayInterfaceImplB implements PlayInterface {
        @Override
        public void run() {
            System.out.println("类PlayInterfaceImplB 执行run()方法... ");
        }

        @Override
        public void play() {
            System.out.println("类PlayInterfaceImplB 执行play()方法... ");
        }

        @Override
        public void sleep() {
            System.out.println("PlayInterfaceImplB 执行sleep()方法... ");
        }

        @Override
        public boolean isNext() {
            return false;
        }
    }

    static class PlayInterfaceImplC implements PlayInterface {
        @Override
        public void run() {
            System.out.println("类PlayInterfaceImplC 执行run()方法... ");
        }

        @Override
        public void play() {
            System.out.println("类PlayInterfaceImplC 执行play()方法... ");
        }

        @Override
        public void sleep() {
            System.out.println("类PlayInterfaceImplC 执行sleep()方法... ");
        }

        @Override
        public boolean isNext() {
            return true;
        }
    }
}
```

> 控制台打印

```java
类PlayInterfaceImplA 执行run()方法... 
类PlayInterfaceImplA 执行play()方法... 
类PlayInterfaceImplA 执行sleep()方法...
 
类PlayInterfaceImplB 执行run()方法... 
类PlayInterfaceImplB 执行play()方法... 

类PlayInterfaceImplC 执行run()方法... 
类PlayInterfaceImplC 执行play()方法... 
类PlayInterfaceImplC 执行sleep()方法... 
```