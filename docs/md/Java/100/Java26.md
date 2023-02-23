```java

package com.hundsun.demo.list.d;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Semaphore;
import java.util.concurrent.TimeUnit;

/**
 * @Desc 同步器: 信号量Semaphore
 * @Date 2022/9/20
 * @Created root
 */
public class D_110 {
    public static void main(String[] args) {

        ExecutorService threadPool = Executors.newFixedThreadPool(10);
        Semaphore semaphore = new Semaphore(5);

        for (int i = 0; i < 20; i++) {
            Runnable runnable = () -> {
                try {
                    // 获取锁
                    semaphore.acquire();
                    System.out.println(Thread.currentThread().getName() + "正在执行...");
                    TimeUnit.SECONDS.sleep(3);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } finally {
                    // 释放锁
                    semaphore.release();
                }
            };
            threadPool.execute(runnable);
        }
        threadPool.shutdown();
    }
}

```

> 控制台打印

```java

pool-1-thread-2正在执行...
pool-1-thread-4正在执行...
pool-1-thread-1正在执行...
pool-1-thread-3正在执行...
pool-1-thread-5正在执行...

pool-1-thread-1正在执行...
pool-1-thread-2正在执行...
pool-1-thread-5正在执行...
pool-1-thread-7正在执行...
pool-1-thread-6正在执行...

pool-1-thread-8正在执行...
pool-1-thread-4正在执行...
pool-1-thread-3正在执行...
pool-1-thread-10正在执行...
pool-1-thread-9正在执行...

pool-1-thread-1正在执行...
pool-1-thread-6正在执行...
pool-1-thread-5正在执行...
pool-1-thread-7正在执行...
pool-1-thread-2正在执行...

```