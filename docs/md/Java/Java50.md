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
import org.springframework.beans.factory.BeanNotOfRequiredTypeException;
import org.springframework.beans.factory.NoSuchBeanDefinitionException;
import org.springframework.cglib.proxy.Enhancer;
import org.springframework.cglib.proxy.NoOp;
import org.springframework.lang.Nullable;
import java.io.*;
import java.lang.reflect.Constructor;
import java.lang.reflect.InvocationTargetException;
import java.net.MalformedURLException;
import java.net.URL;
import java.net.URLConnection;
import java.util.*;
/**
 * @author Administrator
 * @Desc 实现应用上下文，自动识别、资源加载、扩展机制
 * @Date 2022-9-6
 */
public class E_111 {
    public static void main(String[] args) {

        // ################################ 不使用应用上下文 ################################

         // 1.初始化 BeanFactory
        DefaultListableBeanFactory beanFactory = new DefaultListableBeanFactory();

        // 2. 读取配置文件&注册Bean
        XmlBeanDefinitionReader reader = new XmlBeanDefinitionReader(beanFactory);
        reader.loadBeanDefinitions("E:\\demo\\demo\\src\\main\\resources\\spring.xml");

        // 3. BeanDefinition 加载完成 & Bean实例化之前，修改 BeanDefinition 的属性值
        MyBeanFactoryPostProcessor beanFactoryPostProcessor = new MyBeanFactoryPostProcessor();
        beanFactoryPostProcessor.postProcessBeanFactory(beanFactory);

        // 4. Bean实例化之后，修改 Bean 属性信息
        MyBeanPostProcessor beanPostProcessor = new MyBeanPostProcessor();
        beanFactory.addBeanPostProcessor(beanPostProcessor);

        // 5. 获取Bean对象调用方法
        UserService userService = (UserService) beanFactory.getBean("userService");
        System.out.println(userService.getUserList());


        // ################################ 使用应用上下文 ################################
        // 1.初始化 BeanFactory
        ClassPathXmlApplicationContext applicationContext = new ClassPathXmlApplicationContext("E:\\demo\\demo\\src\\main\\resources\\spring.xml");

        // 2. 获取Bean对象调用方法
        UserService userService2 = (UserService) applicationContext.getBean("userService");
        System.out.println(userService2.getUserList());
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

        public Map<String, Object> getSingletonObjects() {
            return singletonObjects;
        }

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
        protected final List<BeanPostProcessor> beanPostProcessors = new ArrayList<>();

        public List<BeanPostProcessor> getBeanPostProcessors() {
            return beanPostProcessors;
        }

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
    static abstract class AbstractAutowireCapableBeanFactory extends AbstractBeanFactory implements AutowireCapableBeanFactory {
        @Override
        protected Object createBean(String beanName, BeanDefinition beanDefinition, Object[] args) throws BeansException {
            Object bean = null;
            try {
                // 创建Bean
                bean = createBeanInstance(beanName, beanDefinition, args);
                // Bean 属性填充
                applyPropertyValues(beanName, bean, beanDefinition);
                // 执行 Bean 的初始化方法和 BeanPostProcessor 的前置和后置处理方法
                bean = initializeBean(beanName, bean, beanDefinition);
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

        // 执行 Bean 的初始化方法和 BeanPostProcessor 的前置和后置处理方法
        private Object initializeBean(String beanName, Object bean, BeanDefinition beanDefinition) {
            // 1. 执行 BeanPostProcessor Before 处理
            Object wrappedBean = applyBeanPostProcessorsBeforeInitialization(bean, beanName);

            // 待完成内容：invokeInitMethods(beanName, wrappedBean, beanDefinition);
            invokeInitMethods(beanName, wrappedBean, beanDefinition);

            // 2. 执行 BeanPostProcessor After 处理
            wrappedBean = applyBeanPostProcessorsAfterInitialization(bean, beanName);
            return wrappedBean;
        }

        private void invokeInitMethods(String beanName, Object wrappedBean, BeanDefinition beanDefinition) {

        }

        @Override
        public Object applyBeanPostProcessorsBeforeInitialization(Object existingBean, String beanName) throws BeansException {
            Object result = existingBean;
            for (BeanPostProcessor processor : getBeanPostProcessors()) {
                Object current = processor.postProcessBeforeInitialization(result, beanName);
                if (null == current) return result;
                result = current;
            }
            return result;
        }

        @Override
        public Object applyBeanPostProcessorsAfterInitialization(Object existingBean, String beanName) throws BeansException {
            Object result = existingBean;
            for (BeanPostProcessor processor : getBeanPostProcessors()) {
                Object current = processor.postProcessAfterInitialization(result, beanName);
                if (null == current) return result;
                result = current;
            }
            return result;
        }
    }

    // BeanDefinition注册
    interface BeanDefinitionRegistry {
        void registerBeanDefinition(String beanName, BeanDefinition beanDefinition);

        BeanDefinition getBeanDefinition(String beanName);

        boolean containsBeanDefinition(String beanName);
    }

    //  核心类实现
    static class DefaultListableBeanFactory extends AbstractAutowireCapableBeanFactory implements BeanDefinitionRegistry, ConfigurableListableBeanFactory {
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

        @Override
        @SuppressWarnings("unchecked")
        public <T> Map<String, T> getBeansOfType(Class<T> type) throws BeansException {
            HashMap<String, T> result = new HashMap<>();
            Map<String, Object> singletonObjects = getSingletonObjects();
            Set<Map.Entry<String, Object>> entries = singletonObjects.entrySet();
            for (Map.Entry<String, Object> entry : entries) {
                String key = entry.getKey();
                Object value = entry.getValue();
                if (type != null && !type.isInstance(value)) {
                    throw new BeanNotOfRequiredTypeException(key, type, value.getClass());
                }
                result.put(key, (T) value);
            }
            return result;
        }

        @Override
        public void preInstantiateSingletons() throws BeansException {
            System.out.println("------------------preInstantiateSingletons------------------------");
        }

        @Override
        public void addBeanPostProcessor(BeanPostProcessor beanPostProcessor) {
            beanPostProcessors.add(beanPostProcessor);
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

    // BeanDefinition 加载完成后，实例化 Bean 对象之前，提供修改 BeanDefinition 属性的机制
    interface BeanFactoryPostProcessor {

        //  在所有的 BeanDefinition 加载完成后，实例化 Bean 对象之前，提供修改 BeanDefinition 属性的机制
        void postProcessBeanFactory(ConfigurableListableBeanFactory beanFactory) throws BeansException;
    }

    // BeanPostProcessor 是在 Bean 对象实例化之后修改 Bean 对象，也可以替换 Bean 对象
    interface BeanPostProcessor {

        // 在 Bean 对象执行初始化方法之前，执行此方法
        Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException;

        // 在 Bean 对象执行初始化方法之后，执行此方法
        Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException;
    }

    interface ConfigurableBeanFactory {

        void addBeanPostProcessor(BeanPostProcessor beanPostProcessor);
    }

    interface ListableBeanFactory extends BeanFactory {

        <T> Map<String, T> getBeansOfType(@Nullable Class<T> type) throws BeansException;
    }

    interface ConfigurableListableBeanFactory extends ListableBeanFactory, ConfigurableBeanFactory {

        void preInstantiateSingletons() throws BeansException;

        BeanDefinition getBeanDefinition(String beanName) throws NoSuchBeanDefinitionException;
    }

    // 应用上下文接口
    interface ApplicationContext extends ListableBeanFactory {


    }

    // 应用上下文接口
    interface ConfigurableApplicationContext extends ApplicationContext {

        // 刷新容器
        void refresh() throws BeansException;
    }

    // 应用上下文抽象类实现
    abstract static class AbstractApplicationContext extends DefaultResourceLoader implements ConfigurableApplicationContext {

        // 模板方法
        @Override
        public void refresh() throws BeansException {

            // 1. 创建 BeanFactory，并加载 BeanDefinition
            refreshBeanFactory();

            // 2. 获取 BeanFactory
            ConfigurableListableBeanFactory beanFactory = getBeanFactory();

            // 3. 在 Bean 实例化之前，执行 BeanFactoryPostProcessor (Invoke factory processors registered as beans in the context.)
            invokeBeanFactoryPostProcessors(beanFactory);

            // 4. BeanPostProcessor 需要提前于其他 Bean 对象实例化之前执行注册操作
            registerBeanPostProcessors(beanFactory);

            // 5. 提前实例化单例Bean对象
            beanFactory.preInstantiateSingletons();
        }


        @Override
        public Object getBean(String name) {
            return getBeanFactory().getBean(name);
        }

        @Override
        public Object getBean(String name, Object... args) throws BeansException {
            return getBeanFactory().getBean(name, args);
        }

        //  创建 BeanFactory，并加载 BeanDefinition
        protected abstract void refreshBeanFactory() throws BeansException;

        protected abstract ConfigurableListableBeanFactory getBeanFactory();

        @Override
        public <T> Map<String, T> getBeansOfType(Class<T> type) throws BeansException {
            ConfigurableListableBeanFactory beanFactory = getBeanFactory();
            return beanFactory.getBeansOfType(type);
        }

        //  在 Bean 实例化之前，执行 BeanFactoryPostProcessor (Invoke factory processors registered as beans in the context.)
        private void invokeBeanFactoryPostProcessors(ConfigurableListableBeanFactory beanFactory) {
            Map<String, BeanFactoryPostProcessor> beanFactoryPostProcessorMap = beanFactory.getBeansOfType(BeanFactoryPostProcessor.class);
            for (BeanFactoryPostProcessor beanFactoryPostProcessor : beanFactoryPostProcessorMap.values()) {
                beanFactoryPostProcessor.postProcessBeanFactory(beanFactory);
            }
        }

        // BeanPostProcessor 需要提前于其他 Bean 对象实例化之前执行注册操作
        private void registerBeanPostProcessors(ConfigurableListableBeanFactory beanFactory) {
            Map<String, BeanPostProcessor> beanPostProcessorMap = beanFactory.getBeansOfType(BeanPostProcessor.class);
            for (BeanPostProcessor beanPostProcessor : beanPostProcessorMap.values()) {
                beanFactory.addBeanPostProcessor(beanPostProcessor);
            }
        }
    }

    // 获取Bean工厂和加载资源
    abstract static class AbstractRefreshableApplicationContext extends AbstractApplicationContext {
        private DefaultListableBeanFactory beanFactory;

        @Override
        protected void refreshBeanFactory() throws BeansException {

            DefaultListableBeanFactory beanFactory = createBeanFactory();
            loadBeanDefinitions(beanFactory);
            this.beanFactory = beanFactory;
        }


        private DefaultListableBeanFactory createBeanFactory() {
            return new DefaultListableBeanFactory();
        }

        protected abstract void loadBeanDefinitions(DefaultListableBeanFactory beanFactory);

        @Override
        protected ConfigurableListableBeanFactory getBeanFactory() {
            return beanFactory;
        }
    }

    // 上下文中对配置信息的加载
    abstract static class AbstractXmlApplicationContext extends AbstractRefreshableApplicationContext {

        @Override
        protected void loadBeanDefinitions(DefaultListableBeanFactory beanFactory) {
            XmlBeanDefinitionReader beanDefinitionReader = new XmlBeanDefinitionReader(beanFactory, this);
            String configLocations = getConfigLocations();
            if (null != configLocations) {
                beanDefinitionReader.loadBeanDefinitions(configLocations);
            }
        }

        protected abstract String getConfigLocations();
    }

    //  应用上下文实现类
    static class ClassPathXmlApplicationContext extends AbstractXmlApplicationContext {
        private String configLocations;

        // 从 XML 中加载 BeanDefinition，并刷新上下文
        public ClassPathXmlApplicationContext(String configLocations) {
            this.configLocations = configLocations;
            refresh();
        }

        @Override
        protected String getConfigLocations() {
            return configLocations;
        }
    }

    interface AutowireCapableBeanFactory {

        Object applyBeanPostProcessorsBeforeInitialization(Object existingBean, String beanName)
                throws BeansException;

        Object applyBeanPostProcessorsAfterInitialization(Object existingBean, String beanName)
                throws BeansException;
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

    // BeanDefinition 加载完成后，实例化 Bean 对象之前，提供修改 BeanDefinition 属性的机制
    static class MyBeanFactoryPostProcessor implements BeanFactoryPostProcessor {

        @Override
        public void postProcessBeanFactory(ConfigurableListableBeanFactory beanFactory) throws BeansException {
            BeanDefinition beanDefinition = beanFactory.getBeanDefinition("userService");
            PropertyValues propertyValues = beanDefinition.getPropertyValues();
            propertyValues.addPropertyValue(new PropertyValue("name", "BeanFactoryPostProcessor改为：客户经理"));
            beanDefinition.setPropertyValues(propertyValues);
            ((DefaultListableBeanFactory) beanFactory).registerBeanDefinition("userService", beanDefinition);
        }
    }

    // BeanPostProcessor 是在 Bean 对象实例化之后修改 Bean 对象，也可以替换 Bean 对象
    static class MyBeanPostProcessor implements BeanPostProcessor {

        @Override
        public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
            if ("userService".equals(beanName)) {
                UserService userService = (UserService) bean;
                userService.setName("BeanPostProcessor改为：系统管理员");
            }
            return bean;
        }

        @Override
        public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
            System.out.println("postProcessBeforeInitialization后置操作");
            return null;
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
        private String name;
        private UserDao userDao;

        public List<String> getUserList() {
            System.out.println("类名：" + getClass().getSimpleName() + ", 属性id值：" + id + ", 属性name值：" + name);
            return userDao.getUserList();
        }
    }
}
```

> Spring.xml 文件配置 Bean 注册

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans>

    <bean id="userDao" name="userDao" class="com.hundsun.demo.list.e.E_111$UserDao"/>

    <bean id="userService" name="userService" class="com.hundsun.demo.list.e.E_111$UserService">
        <property name="id" value="10001"/>
        <property name="name" value="普通管理员"/>
        <property name="userDao" ref="userDao"/>
    </bean>

    <!-- BeanPostProcessor 是在 Bean 对象实例化之后修改 Bean 对象，也可以替换 Bean 对象 -->
    <bean id="myBeanPostProcessor" name="myBeanPostProcessor"
          class="com.hundsun.demo.list.e.E_111$MyBeanPostProcessor"/>

    <!-- BeanDefinition 加载完成后，实例化 Bean 对象之前，提供修改 BeanDefinition 属性的机制 -->
    <bean id="myBeanFactoryPostProcessor" name="myBeanFactoryPostProcessor"
          class="com.hundsun.demo.list.e.E_111$MyBeanFactoryPostProcessor"/>

</beans>
```

> 控制台打印

```java
----------采用了JDK实例化策略----------
----------采用了JDK实例化策略----------
postProcessBeforeInitialization后置操作
postProcessBeforeInitialization后置操作
类名：UserService, 属性id值：10001, 属性name值：BeanPostProcessor改为：系统管理员
[张三, 李四, 王五]
------------------preInstantiateSingletons------------------------
----------采用了JDK实例化策略----------
----------采用了JDK实例化策略----------
类名：UserService, 属性id值：10001, 属性name值：普通管理员
[张三, 李四, 王五]
```