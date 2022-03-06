```java

package com.hundsun.hswealth.mkm;

public class MainApp {

    private static Object lock1 = new Object(); // 锁对象
    private static volatile boolean status = true; // 共享变量可见性

    static class SubThread1 extends Thread {
        @Override
        public void run() {
            synchronized (lock1) {
                while (status) {
                    System.out.println(Thread.currentThread().getName() + "提供者提供商品...");
                    try {
                        Thread.sleep(100);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    status = false;
                }

                System.out.println(Thread.currentThread().getName() + "等待消费者消费商品...");
                lock1.notifyAll(); // 通知所有线程
            }
        }
    }

    static class SubThread2 extends Thread {
        @Override
        public void run() {
            synchronized (lock1) {
                while (!status) {
                    System.out.println(Thread.currentThread().getName() + "消费者消费商品...");
                    try {
                        Thread.sleep(100);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    status = true;
                }
                System.out.println(Thread.currentThread().getName() + "等待提供者提供商品...");
                try {
                    lock1.wait(); // 线程等待
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    public static void main(String[] args) {

        for (;;) {
            Thread thread1 = new Thread(new SubThread1());
            Thread thread2 = new Thread(new SubThread2());
            thread1.start();
            thread2.start();
        }
    }
}

```