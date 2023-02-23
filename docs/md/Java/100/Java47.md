```java
package com.hundsun.demo.list.e;
import cn.hutool.core.bean.BeanUtil;
import lombok.AllArgsConstructor;
import lombok.Data;
import org.springframework.beans.BeansException;
import org.springframework.cglib.proxy.Enhancer;
import org.springframework.cglib.proxy.NoOp;
import java.lang.reflect.Constructor;
import java.lang.reflect.InvocationTargetException;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

/**
 * @author Administrator
 * @Desc 为 Bean 对象注入属性和依赖 Bean 的功能实现
 * @Date 2022-9-26
 * @Created root
 */
public class E_109 {
    public static void main(String[] args) throws Exception {

        // 初始化工厂
        DefaultListableBeanFactory beanFactory = new DefaultListableBeanFactory();
        // 注册userDao
        beanFactory.registerBeanDefinition("userDao", new BeanDefinition(UserDao.class));

        // 设置userService的属性，并注册userService
        PropertyValues propertyValues = new PropertyValues();
        propertyValues.addPropertyValue(new PropertyValue("id", 1001));
        propertyValues.addPropertyValue(new PropertyValue("userDao", new BeanReference("userDao")));
        beanFactory.registerBeanDefinition("userService", new BeanDefinition(UserService.class, propertyValues));

        // 获取Bean
        UserService userService = (UserService) beanFactory.getBean("userService");
        List<String> userList = userService.getUserList();
        System.out.println(userList);
    }

    // BeanDefinition 定义
    @Data
    static class BeanDefinition {
        private Class beanClass;
        private PropertyValues propertyValues;

        public BeanDefinition(Class beanClass, PropertyValues propertyValues) {
            this.beanClass = beanClass;
            this.propertyValues = propertyValues;
        }

        public BeanDefinition(Class beanClass) {
            this.beanClass = beanClass;
        }
    }

    // 属性类
    @Data
    static class PropertyValue {

        private final String name;

        private final Object value;

        public PropertyValue(String name, Object value) {
            this.name = name;
            this.value = value;
        }
    }

    // 属性集合类
    static class PropertyValues {

        private final List<PropertyValue> propertyValueList = new ArrayList<>();

        public void addPropertyValue(PropertyValue pv) {
            this.propertyValueList.add(pv);
        }

        public PropertyValue[] getPropertyValues() {
            return this.propertyValueList.toArray(new PropertyValue[0]);
        }

        public PropertyValue getPropertyValue(String propertyName) {
            for (PropertyValue dto : this.propertyValueList) {
                if (dto.getName().equals(propertyName)) {
                    return dto;
                }
            }
            return null;
        }
    }

    // 单例注册接口
    interface SingletonBeanRegistry {
        Object getSingleton(String beanName);
    }


    @Data
    @AllArgsConstructor
    static class BeanReference {
        private String beanName;
    }

    static class UserDao {
        public static final List<String> userList = new ArrayList<>();

        static {
            userList.add("张三");
            userList.add("李四");
            userList.add("王五");
        }

        public List<String> getUserList() {
            return userList;
        }
    }

    @Data
    static class UserService {
        private Integer id;
        private UserDao userDao;

        public List<String> getUserList() {
            System.out.println("类名：" + getClass().getSimpleName() + "属性id值：" + id);
            return userDao.getUserList();
        }
    }

    // 单例注册接口实现
    static class DefaultSingletonBeanRegistry implements SingletonBeanRegistry {
        private Map<String, Object> singletonObjects = new HashMap<>();

        // 获取单例Bean
        @Override
        public Object getSingleton(String beanName) {
            return singletonObjects.get(beanName);
        }

        // 添加单例Bean
        protected void addSingleton(String beanName, Object singletonObject) {
            singletonObjects.put(beanName, singletonObject);
        }
    }

    // Bean工厂
    interface BeanFactory {

        // 获取Bean
        Object getBean(String name);

        // 获取Bean
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

        // 获取BeanDefinition 定义
        protected abstract BeanDefinition getBeanDefinition(String beanName) throws BeansException;

        // 创建Bean
        protected abstract Object createBean(String beanName, BeanDefinition beanDefinition, Object[] args) throws BeansException;
    }

    //  实例化Bean类
    static abstract class AbstractAutowireCapableBeanFactory extends AbstractBeanFactory {
        @Override
        protected Object createBean(String beanName, BeanDefinition beanDefinition, Object[] args) throws BeansException {
            Object bean = null;
            try {
                // 创建Bean
                bean = createBeanInstance(beanName, beanDefinition, args);
                // Bean 属性填充
                applyPropertyValues(beanName, bean, beanDefinition);
            } catch (Exception e) {
                throw new RuntimeException("Instantiation of bean failed", e);
            }
            addSingleton(beanName, bean);
            return bean;
        }

        // 创建Bean
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

        /**
         * Bean 属性填充
         */
        protected void applyPropertyValues(String beanName, Object bean, BeanDefinition beanDefinition) {
            try {
                PropertyValues propertyValues = beanDefinition.getPropertyValues();
                if (propertyValues != null) {
                    for (PropertyValue propertyValue : propertyValues.getPropertyValues()) {
                        String name = propertyValue.getName();
                        Object value = propertyValue.getValue();

                        if (value instanceof BeanReference) {
                            // A 依赖 B，获取 B 的实例化
                            BeanReference beanReference = (BeanReference) value;
                            value = getBean(beanReference.getBeanName());
                        }
                        // 属性填充
                        BeanUtil.setFieldValue(bean, name, value);
                    }
                }
            } catch (Exception e) {
                throw new RuntimeException("Error setting property values：" + beanName);
            }
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
----------采用了JDK实例化策略----------
----------采用了JDK实例化策略----------
类名：UserService属性id值：1001
[张三, 李四, 王五]
```