```java
package com.hundsun.demo.list.e;
import cn.hutool.core.bean.BeanUtil;
import lombok.AllArgsConstructor;
import lombok.Data;
import org.apache.commons.lang3.StringUtils;
import org.dom4j.Document;
import org.dom4j.DocumentException;
import org.dom4j.Element;
import org.dom4j.io.SAXReader;
import org.springframework.beans.BeansException;
import org.springframework.beans.factory.BeanCreationException;
import org.springframework.cglib.proxy.Enhancer;
import org.springframework.cglib.proxy.NoOp;
import java.io.*;
import java.lang.reflect.Constructor;
import java.lang.reflect.InvocationTargetException;
import java.net.MalformedURLException;
import java.net.URL;
import java.net.URLConnection;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

/**
 * @author Administrator
 * @Desc 设计与实现资源加载器，从 Spring.xml 解析和注册 Bean 对象
 * @Date 2022-9-26
 * @Created root
 */
public class E_110 {
    public static void main(String[] args) {

        // 初始化 BeanFactory
        DefaultListableBeanFactory beanFactory = new DefaultListableBeanFactory();

        // 读取配置文件 && 注册Bean
        XmlBeanDefinitionReader definitionReader = new XmlBeanDefinitionReader(beanFactory);
        definitionReader.loadBeanDefinitions("E:\\demo\\demo\\src\\main\\resources\\spring.xml");

        // 获取Bean对象调用方法
        UserService userService = (UserService) beanFactory.getBean("userService");
        System.out.println(userService.getUserList());
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

        // Bean 属性填充
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

        boolean containsBeanDefinition(String beanName);
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

        @Override
        public boolean containsBeanDefinition(String beanName) {
            return beanDefinitionMap.containsKey(beanName);
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

    // 资源加载接口定义
    interface Resource {
        InputStream getInputStream() throws IOException;
    }

    // ClassPath资源加载实现
    static class ClassPathResource implements Resource {
        private String path;
        private ClassLoader classLoader;

        public ClassPathResource(String path) {
            this.path = path;
            this.classLoader = getClass().getClassLoader();
        }

        public ClassPathResource(String path, ClassLoader classLoader) {
            this.path = path;
            this.classLoader = classLoader;
        }

        @Override
        public InputStream getInputStream() throws IOException {
            InputStream is = classLoader.getResourceAsStream(path);
            if (is == null) {
                throw new FileNotFoundException(this.path + " cannot be opened because it does not exist");
            }
            return is;
        }
    }

    // File资源加载实现
    static class FileSystemResource implements Resource {
        private File file;
        private String path;

        public FileSystemResource(File file) {
            this.file = file;
            this.path = file.getPath();
        }

        public FileSystemResource(String path) {
            this.file = new File(path);
            this.path = path;
        }

        @Override
        public InputStream getInputStream() throws IOException {
            return new FileInputStream(file);
        }
    }

    // Url资源加载实现
    static class UrlResource implements Resource {
        private URL url;

        public UrlResource(URL url) {
            this.url = url;
        }

        @Override
        public InputStream getInputStream() throws IOException {
            URLConnection urlConnection = this.url.openConnection();
            return urlConnection.getInputStream();
        }
    }

    // 包装资源加载器 - 按照资源加载的不同方式，资源加载器可以把这些方式集中到统一的类服务下进行处理，外部用户只需要传递资源地址即可，简化使用，类似于资源加载器的上下文管理
    interface ResourceLoader {
        // Pseudo URL prefix for loading from the class path: "classpath:"
        String CLASSPATH_URL_PREFIX = "classpath:";

        Resource getResource(String location);
    }

    // 包装资源加载器实现
    static class DefaultResourceLoader implements ResourceLoader {

        @Override
        public Resource getResource(String location) {

            if (location.startsWith(CLASSPATH_URL_PREFIX)) {
                return new ClassPathResource(location.substring(CLASSPATH_URL_PREFIX.length()));
            }

            try {
                URL url = new URL(location);
                return new UrlResource(url);
            } catch (MalformedURLException e) {
                return new FileSystemResource(location);
            }
        }
    }

    // Bean定义读取接口
    interface BeanDefinitionReader {
        BeanDefinitionRegistry getRegistry();

        ResourceLoader getResourceLoader();

        void loadBeanDefinitions(Resource resource) throws BeansException;

        void loadBeanDefinitions(String location) throws BeansException;
    }

    // Bean定义抽象类实现
    @Data
    abstract static class AbstractBeanDefinitionReader implements BeanDefinitionReader {
        private BeanDefinitionRegistry registry;
        private ResourceLoader resourceLoader;

        public AbstractBeanDefinitionReader(BeanDefinitionRegistry registry) {
            this.registry = registry;
            this.resourceLoader = new DefaultResourceLoader();
        }

        public AbstractBeanDefinitionReader(BeanDefinitionRegistry registry, ResourceLoader resourceLoader) {
            this.registry = registry;
            this.resourceLoader = resourceLoader;
        }
    }

    // 解析XML处理Bean注册
    static class XmlBeanDefinitionReader extends AbstractBeanDefinitionReader {

        public XmlBeanDefinitionReader(BeanDefinitionRegistry registry) {
            super(registry);
        }

        public XmlBeanDefinitionReader(BeanDefinitionRegistry registry, ResourceLoader resourceLoader) {
            super(registry, resourceLoader);
        }

        @Override
        public void loadBeanDefinitions(Resource resource) throws BeansException {

            try (InputStream is = resource.getInputStream()) {
                doLoadBeanDefinitions(is);
            } catch (IOException | DocumentException | ClassNotFoundException e) {
                throw new BeanCreationException("IOException parsing XML document from " + resource);
            }
        }

        @Override
        public void loadBeanDefinitions(String location) throws BeansException {
            ResourceLoader resourceLoader = getResourceLoader();
            Resource resource = resourceLoader.getResource(location);
            loadBeanDefinitions(resource);
        }

        private void doLoadBeanDefinitions(InputStream is) throws DocumentException, ClassNotFoundException {
            SAXReader saxReader = new SAXReader();
            Document document = saxReader.read(is);
            Element rootElement = document.getRootElement();
            List<Element> elements = rootElement.elements();

            for (Element beanElement : elements) {

                // 判断 <bean> 标签
                if ("bean".equals(beanElement.getName())) {
                    // 解析 <bean> 标签
                    String id = beanElement.attribute("id") == null ? "" : beanElement.attribute("id").getValue();
                    String name = beanElement.attribute("name") == null ? "" : beanElement.attribute("name").getValue();
                    String className = beanElement.attribute("class") == null ? "" : beanElement.attribute("class").getValue();

                    // 获取Class对象
                    Class<?> clazz = Class.forName(className);

                    // 优先级 id > name
                    String beanName = StringUtils.isNotBlank(id) ? id : name;

                    // 定义Bean
                    BeanDefinition beanDefinition = new BeanDefinition(clazz, new PropertyValues());

                    // 获取 <property> 标签
                    List<Element> propertyElements = beanElement.elements();
                    for (Element propertyElement : propertyElements) {
                        // 解析 <property> 标签
                        String proName = propertyElement.attribute("name") == null ? "" : propertyElement.attribute("name").getValue();
                        String value = propertyElement.attribute("value") == null ? "" : propertyElement.attribute("value").getValue();
                        String ref = propertyElement.attribute("ref") == null ? "" : propertyElement.attribute("ref").getValue();

                        // 获取属性值：引入对象、值对象
                        Object PropertyValue = StringUtils.isNotBlank(ref) ? new BeanReference(ref) : value;
                        beanDefinition.getPropertyValues().addPropertyValue(new PropertyValue(proName, PropertyValue));
                    }

                    if (getRegistry().containsBeanDefinition(beanName)) {
                        throw new BeanCreationException("Duplicate beanName[" + beanName + "] is not allowed");
                    }

                    // 注册 BeanDefinition
                    getRegistry().registerBeanDefinition(beanName, beanDefinition);
                }
            }
        }
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
}
```

> Spring.xml 文件配置 Bean 注册

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans>

    <bean id="userDao" name="userDao" class="com.hundsun.demo.list.e.E_110$UserDao"/>

    <bean id="userService" name="userService" class="com.hundsun.demo.list.e.E_110$UserService">
        <property name="id" value="10001"/>
        <property name="userDao" ref="userDao"/>
    </bean>

</beans>
```

> 控制台打印

```java
----------采用了JDK实例化策略----------
----------采用了JDK实例化策略----------
类名：UserService属性id值：10001
[张三, 李四, 王五]
```