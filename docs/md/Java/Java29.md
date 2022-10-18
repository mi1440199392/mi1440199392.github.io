```java
package com.hundsun.demo.list.d;
import java.util.AbstractList;
import java.util.ArrayList;
import java.util.List;

/**
 * @Desc 创建不可修改的集合, 模拟Collections.unmodifiableList()
 * @Date 2022/9/13
 * @Created root
 */
public class D_107 {
    public static void main(String[] args) {

        List<Integer> integers = new ArrayList<Integer>() {{
            add(100);
            add(200);
            add(300);
        }};

        UnmodifiableList<Integer> unmodifiableList = new UnmodifiableList<>(integers);
        Object o = unmodifiableList.get(0);
        System.out.println("o = " + o);

        unmodifiableList.add(100);
        unmodifiableList.remove(1);
    }

    static class UnmodifiableList<E> extends AbstractList<E> {
        private List<E> list;

        UnmodifiableList(List<E> list) {
            this.list = list;
        }

        @Override
        public E get(int index) {
            return list.get(index);
        }

        @Override
        public int size() {
            return list.size();
        }

        @Override
        public void add(int index, Object element) {
            throw new UnsupportedOperationException("unmodifiable");
        }

        @Override
        public E remove(int index) {
            throw new UnsupportedOperationException("unmodifiable");
        }
    }
}
```

> 控制台打印

```java
o = 100
Exception in thread "main" java.lang.UnsupportedOperationException: unmodifiable
	at com.hundsun.demo.list.d.D_107$UnmodifiableList.add(D_107.java:48)
	at java.util.AbstractList.add(AbstractList.java:108)
	at com.hundsun.demo.list.d.D_107.main(D_107.java:25)
```