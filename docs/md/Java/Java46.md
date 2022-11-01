```java
package com.hundsun.demo.list.e;
import org.springframework.beans.BeansException;
import org.springframework.cglib.proxy.Enhancer;
import org.springframework.cglib.proxy.NoOp;
import java.lang.reflect.Constructor;
import java.lang.reflect.InvocationTargetException;
import java.util.HashMap;
import java.util.Map;

/**
 * @author Administrator
 * @Desc 基于 Cglib 和 JDK 实现含构造函数的类实例化策略
 * @Date 2022/9/26
 * @Created root
 */
public class E_108 {
    public static void main(String[] args) throws Exception {
        DefaultListableBeanFactory beanFactory = new DefaultListableBeanFactory();
        BeanDefinition beanDefinition = new BeanDefinition(Person.class);
        BeanDefinition beanDefinition2 = new BeanDefinition(Student.class);
        beanFactory.registerBeanDefinition("person", beanDefinition);
        beanFactory.registerBeanDefinition("student", beanDefinition2);

        System.out.println("----------空参构造----------");
        Object person = beanFactory.getBean("person");
        Object person2 = beanFactory.getBean("person");
        System.out.println(person == person2);
        System.out.println(person.hashCode());
        System.out.println(person2.hashCode());

        System.out.println("----------有参构造----------");
        Object person3 = beanFactory.getBean("person", 1, "张三");
        Object person4 = beanFactory.getBean("person", 1, "张三");
        System.out.println(person3 == person4);
        System.out.println(person3.hashCode());
        System.out.println(person4.hashCode());


        System.out.println("----------空参构造----------");
        Object student = beanFactory.getBean("student");
        Object student2 = beanFactory.getBean("student");
        System.out.println(student == student2);
        System.out.println(student.hashCode());
        System.out.println(student2.hashCode());

        System.out.println("----------有参构造----------");
        Object student3 = beanFactory.getBean("student", 1, "张三");
        Object student4 = beanFactory.getBean("student", 1, "张三");
        System.out.println(student3 == student4);
        System.out.println(student3.hashCode());
        System.out.println(student4.hashCode());
    }

    static class Person {
        private int id;
        private String name;

        public Person(int id, String name) {
            this.id = id;
            this.name = name;
        }

        public Person() {
        }

        public Person(int id) {
            this.id = id;
        }

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

    interface StudentSupport {
    }

    static class Student implements StudentSupport {
        private int id;
        private String name;

        public Student() {
        }

        public Student(int id, String name) {
            this.id = id;
            this.name = name;
        }

        @Override
        public String toString() {
            return "Student{" +
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

        Object getBean(String name, Object... args) throws BeansException;
    }

    // 抽象类定义模板方法
    static abstract class AbstractBeanFactory extends DefaultSingletonBeanRegistry implements BeanFactory {

        // 模板方法
        @Override
        public Object getBean(String beanName) {
            return getBean(beanName, new Object[]{});
        }

        @Override
        public Object getBean(String beanName, Object... args) throws BeansException {

            Object singleton = getSingleton(beanName);
            if (singleton != null) {
                return singleton;
            }

            BeanDefinition beanDefinition = getBeanDefinition(beanName);
            return createBean(beanName, beanDefinition, args);
        }

        protected abstract BeanDefinition getBeanDefinition(String beanName) throws BeansException;

        protected abstract Object createBean(String beanName, BeanDefinition beanDefinition, Object[] args) throws BeansException;
    }

    //  实例化Bean类
    static abstract class AbstractAutowireCapableBeanFactory extends AbstractBeanFactory {
        @Override
        protected Object createBean(String beanName, BeanDefinition beanDefinition, Object[] args) throws BeansException {
            Object bean = null;
            try {
                bean = createBeanInstance(beanName, beanDefinition, args);
            } catch (Exception e) {
                throw new RuntimeException("Instantiation of bean failed", e);
            }
            addSingleton(beanName, bean);
            return bean;
        }

        protected Object createBeanInstance(String beanName, BeanDefinition beanDefinition, Object[] args) {
            Constructor constructorToUse = null;
            Class<?> beanClass = beanDefinition.getBeanClass();
            Constructor<?>[] declaredConstructors = beanClass.getDeclaredConstructors();
            for (Constructor ctor : declaredConstructors) {
                if (null != args && ctor.getParameterTypes().length == args.length) {
                    constructorToUse = ctor;
                    break;
                }
            }
            return getInstantiationStrategy(beanDefinition).instantiate(beanName, beanDefinition, constructorToUse, args);
        }

        // 获取实例化策略类
        private InstantiationStrategy getInstantiationStrategy(BeanDefinition beanDefinition) {
            Class beanClass = beanDefinition.getBeanClass();
            InstantiationStrategy instantiationStrategy;
            if (beanClass.getInterfaces().length == 0) {
                instantiationStrategy = new SimpleInstantiationStrategy();
                System.out.println("----------采用了JDK实例化策略----------");
            } else {
                instantiationStrategy = new CglibSubclassingInstantiationStrategy();
                System.out.println("----------Cglib实例化策略----------");
            }
            return instantiationStrategy;
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

    // 实例化策略接口
    public interface InstantiationStrategy {

        Object instantiate(String beanName, BeanDefinition beanDefinition, Constructor ctor, Object[] args) throws BeansException;
    }

    // JDK实例化策略
    static class SimpleInstantiationStrategy implements InstantiationStrategy {

        @Override
        public Object instantiate(String beanName, BeanDefinition beanDefinition, Constructor ctor, Object[] args) throws BeansException {
            Class clazz = beanDefinition.getBeanClass();
            try {
                if (null != ctor) {
                    return clazz.getDeclaredConstructor(ctor.getParameterTypes()).newInstance(args);
                } else {
                    return clazz.getDeclaredConstructor().newInstance();
                }
            } catch (NoSuchMethodException | InstantiationException | IllegalAccessException | InvocationTargetException e) {
                throw new RuntimeException("Failed to instantiate [" + clazz.getName() + "]", e);
            }
        }
    }

    // Cglib实例化策略
    static class CglibSubclassingInstantiationStrategy implements InstantiationStrategy {

        @Override
        public Object instantiate(String beanName, BeanDefinition beanDefinition, Constructor ctor, Object[] args) throws BeansException {
            Enhancer enhancer = new Enhancer();
            enhancer.setSuperclass(beanDefinition.getBeanClass());
            enhancer.setCallback(new NoOp() {
                @Override
                public int hashCode() {
                    return super.hashCode();
                }
            });
            if (null == ctor) {
                return enhancer.create();
            }
            return enhancer.create(ctor.getParameterTypes(), args);
        }
    }
}
```

> 控制台打印

```java
----------空参构造----------
----------采用了JDK实例化策略----------
true
51228289
51228289
----------有参构造----------
true
51228289
51228289
----------空参构造----------
----------Cglib实例化策略----------
true
248609774
248609774
----------有参构造----------
true
248609774
248609774
```