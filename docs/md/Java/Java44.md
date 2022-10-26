```java
package com.hundsun.demo.list.e;
import java.util.HashMap;
import java.util.Map;

/**
 * @Desc 创建简单的Bean容器
 * @Date 2022/9/26
 * @Created root
 */
public class E_106 {
    public static void main(String[] args) throws Exception {
        BeanDefinition beanDefinition = new BeanDefinition();
        beanDefinition.setBean(new Student(1, "张三"));

        BeanFactory.registerBeanDefinition("student", beanDefinition);
        Object student = BeanFactory.getBean("student");
        System.out.println(student);
    }

    // BeanDefinition，用于定义 Bean 实例化信息，现在的实现是以一个 Object 存放对象
    static class BeanDefinition {

        private Object bean;

        public Object getBean() {
            return bean;
        }

        public void setBean(Object bean) {
            this.bean = bean;
        }
    }

    // BeanFactory，代表了 Bean 对象的工厂，可以存放 Bean 定义到 Map 中以及获取
    static class BeanFactory {
        private static Map<String, BeanDefinition> beanDefinitionMap = new HashMap<>();

        public static Object getBean(String name) {
            return beanDefinitionMap.get(name).getBean();
        }

        public static void registerBeanDefinition(String name, BeanDefinition beanDefinition) {
            beanDefinitionMap.put(name, beanDefinition);
        }
    }

    // 实体类
    static class Student {
        private int id;
        private String name;

        public Student(int id, String name) {
            this.id = id;
            this.name = name;
        }

        public int getId() {
            return id;
        }

        public void setId(int id) {
            this.id = id;
        }

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }

        @Override
        public String toString() {
            return "Student{" +
                    "id=" + id +
                    ", name='" + name + '\'' +
                    '}';
        }
    }
}
```

> 控制台打印

```java
Student{id=1, name='张三'}
```