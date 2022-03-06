```java

package com.hundsun.hswealth.mkm;
import java.util.ArrayList;
import java.util.Collections;
import java.util.Comparator;
import java.util.List;

public class MainApp {

    static class Person {
        private String name;
        private int age;

        public Person(String name, int age) {
            this.name = name;
            this.age = age;
        }

        public String getName() {
            return name;
        }

        public int getAge() {
            return age;
        }

        @Override
        public String toString() {
            return "Person{" +
                    "name='" + name + '\'' +
                    ", age=" + age +
                    '}';
        }
    }

    private static List<Person> LIST1;
    private static List<Person> LIST2;
    static {
        LIST1 = new ArrayList<>();
        LIST1.add(new Person("张三", 100));
        LIST1.add(new Person("李四", 200));
        LIST1.add(new Person("王五", 300));
        LIST1.add(new Person("赵六", 400));
        LIST1.add(new Person("田七", 500));
    }
    static {
        LIST2 = new ArrayList<>();
        LIST2.add(new Person("张三", 100));
        LIST2.add(new Person("李四", 200));
        LIST2.add(new Person("田七", 500));
    }

    public static void main(String[] args) {

        Collections.sort(LIST2, new Comparator<Person>() {
            @Override
            public int compare(Person o1, Person o2) {
                // o1：列表中的下一个元素
                // 02：列表中的当前元素
                System.out.println(o1.getName());
                System.out.println(o2.getName());
                // 自定义排序：列表中的下一个元素 和 列表中的当前元素 比较
                int sort = 1; // 正数正序, 负数倒叙
                System.out.println(sort);
                return sort;  // 当返回值是：正数正序, 负数倒叙
            }
        });
        System.out.println(LIST2.toString());

        Collections.sort(LIST1,(o1,o2)->o2.getAge() - o1.getAge()); // 按照年龄, 倒叙排序
        System.out.println(LIST1.toString());
    }
}

```

```java

-- --------------------------------------------------控制台打印---------------------------------------------------------------------------
李四
张三
1
田七
李四
1
[Person{name='张三', age=100}, Person{name='李四', age=200}, Person{name='田七', age=500}]
[Person{name='田七', age=500}, Person{name='赵六', age=400}, Person{name='王五', age=300}, Person{name='李四', age=200}, Person{name='张三', age=100}]

```