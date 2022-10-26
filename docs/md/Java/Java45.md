```java
package com.hundsun.demo.list.e;
import org.springframework.beans.BeansException;
import java.util.HashMap;
import java.util.Map;

/**
 * @author Administrator
 * @Desc 实现 Bean 的定义、注册、获取
 * @Date 2022/9/26
 * @Created root
 */
public class E_107 {
    public static void main(String[] args) throws Exception {
        DefaultListableBeanFactory beanFactory = new DefaultListableBeanFactory();
        BeanDefinition beanDefinition = new BeanDefinition(Person.class);
        beanFactory.registerBeanDefinition("person", beanDefinition);

        Object person = beanFactory.getBean("person");
        Object person2 = beanFactory.getBean("person");

        System.out.println(person == person2);
        System.out.println(person.hashCode());
        System.out.println(person2.hashCode());
    }

    static class Person {
        private int id;
        private String name;

        @Override
        public String toString() {
            return "Person{" +
                    "id=" + id +
                    ", name='" + name + '\'' +
                    '}';
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
    }


    // BeanDefinition 定义
    static class BeanDefinition {
        private Class beanClass;

        public BeanDefinition(Class beanClass) {
            this.beanClass = beanClass;
        }

        public Class getBeanClass() {
            return beanClass;
        }

        public void setBeanClass(Class beanClass) {
            this.beanClass = beanClass;
        }
    }

    // 单例注册接口
    interface SingletonBeanRegistry {
        Object getSingleton(String beanName);
    }

    // 单例注册接口实现
    static class DefaultSingletonBeanRegistry implements SingletonBeanRegistry {
        private Map<String, Object> singletonObjects = new HashMap<>();

        @Override
        public Object getSingleton(String beanName) {
            return singletonObjects.get(beanName);
        }

        protected void addSingleton(String beanName, Object singletonObject) {
            singletonObjects.put(beanName, singletonObject);
        }
    }

    // Bean工厂
    interface BeanFactory {
        Object getBean(String name);
    }

    // 抽象类定义模板方法
    static abstract class AbstractBeanFactory extends DefaultSingletonBeanRegistry implements BeanFactory {

        // 模板方法
        @Override
        public Object getBean(String beanName) {
            Object singleton = getSingleton(beanName);
            if (singleton != null) {
                return singleton;
            }

            BeanDefinition beanDefinition = getBeanDefinition(beanName);
            return createBean(beanName, beanDefinition);
        }

        protected abstract BeanDefinition getBeanDefinition(String beanName) throws BeansException;

        protected abstract Object createBean(String beanName, BeanDefinition beanDefinition) throws BeansException;
    }

    //  实例化Bean类
    static abstract class AbstractAutowireCapableBeanFactory extends AbstractBeanFactory {
        @Override
        protected Object createBean(String beanName, BeanDefinition beanDefinition) throws BeansException {
            Object bean = null;
            try {
                bean = beanDefinition.getBeanClass().newInstance();
            } catch (InstantiationException | IllegalAccessException e) {
                throw new RuntimeException("Instantiation of bean failed", e);
            }
            addSingleton(beanName, bean);
            return bean;
        }
    }

    // BeanDefinition注册
    interface BeanDefinitionRegistry {
        void registerBeanDefinition(String beanName, BeanDefinition beanDefinition);

        BeanDefinition getBeanDefinition(String beanName);
    }

    //  核心类实现
    static class DefaultListableBeanFactory extends AbstractAutowireCapableBeanFactory implements BeanDefinitionRegistry {
        private Map<String, BeanDefinition> beanDefinitionMap = new HashMap<>();

        @Override
        public void registerBeanDefinition(String beanName, BeanDefinition beanDefinition) {
            beanDefinitionMap.put(beanName, beanDefinition);
        }

        @Override
        public BeanDefinition getBeanDefinition(String beanName) {
            BeanDefinition beanDefinition = beanDefinitionMap.get(beanName);
            if (beanDefinition == null) {
                throw new RuntimeException("No bean named '" + beanName + "' is defined");
            }

            return beanDefinition;
        }
    }
}
```

> 控制台打印

```java
Student{id=1, name='张三'}
```