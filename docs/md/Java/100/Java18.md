```java

package com.hundsun.hswealth.mkm;
import java.util.concurrent.LinkedBlockingDeque;

public class MainApp {
    public static void main(String[] args) {

        // 队列
        LinkedBlockingDeque<String> deque = new LinkedBlockingDeque(10);
        deque.add("111");
        deque.add("222");
        deque.add("333");
        deque.add("444");

        for (;;){
            String poll = deque.poll();
            System.out.println(poll);

            if (deque.isEmpty()) {
                break;
            }
        }
    }
}

```

```java

-- --------------------------------------------------------控制台打印--------------------------------------------------------------------
111
222
333
444

```