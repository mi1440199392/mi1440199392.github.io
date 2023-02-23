```java
package com.hundsun.demo.list.d;
import java.util.concurrent.ArrayBlockingQueue;
import java.util.concurrent.TimeUnit;

/**
 * @Desc ArrayBlockingQueue特性，实现生产者消费者
 * @Date 2022/9/13
 * @Created root
 */
public class D_109 {
    public static final ArrayBlockingQueue<Integer> queue = new ArrayBlockingQueue<>(1);

    public static void main(String[] args) throws InterruptedException {
        new ThreadProducer(queue).start();
        new ThreadConsumer(queue).start();

        TimeUnit.SECONDS.sleep(3);

        new ThreadProducer(queue).start();
        new ThreadConsumer(queue).start();
    }


    static class ThreadConsumer extends Thread {
        private ArrayBlockingQueue<Integer> queue;

        public ThreadConsumer(ArrayBlockingQueue<Integer> queue) {
            this.queue = queue;
        }

        @Override
        public void run() {
            try {
                consumer();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }

        void consumer() throws InterruptedException {
            System.out.println("消费数据 = " + queue.take());
        }
    }

    static class ThreadProducer extends Thread {
        private ArrayBlockingQueue<Integer> queue;

        public ThreadProducer(ArrayBlockingQueue<Integer> queue) {
            this.queue = queue;
        }

        @Override
        public void run() {
            try {
                producer();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }

        void producer() throws InterruptedException {
            queue.put(1000);
            System.out.println("生产数据...");
        }
    }
}
```

> 控制台打印

```java
生产数据...
消费数据 = 1000
生产数据...
消费数据 = 1000
```