```java

package com.hundsun.demo.list.b;
import org.apache.commons.collections4.ListUtils;
import java.util.ArrayList;
import java.util.List;

public class B_101 {
    public static void main(String[] args) {

        List<String> list = new ArrayList<>(10);
        for (int i = 0; i < 80; i++) {
            list.add("张三" + i);
        }

        // 集合数据分片
        List<List<String>> partition = ListUtils.partition(list, 30);
        for (List<String> item : partition) {
            int size = item.size();
            System.out.println("size = " + size);
        }
    }
}

```

> 控制台打印

```java

size = 30
size = 30
size = 20

```