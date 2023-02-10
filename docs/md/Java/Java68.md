# Main 方法

```java 
package com.alibaba.frame.a;
import com.alibaba.fastjson.JSON;
import com.alibaba.fastjson.TypeReference;
import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.springframework.beans.BeansException;
import org.springframework.context.ApplicationContext;
import org.springframework.context.ApplicationContextAware;
import java.lang.reflect.Method;
import java.util.HashMap;
import java.util.Map;
import java.util.Set;

/**
 * @author hspcadmin
 * @version 1.0
 * @description 模拟数据库读取配置BeanDefinition，反射执行Bean方法
 */
public class A_109 {
	public static void main(String[] args) throws Exception {

		final RuleDefinition definition = LoadBeanDefinitionImpl.instance().loadDefinition();
		ExecutorContext.exec(definition);
	}
}
```

# 配置对象 RuleDefinition

```java 
// 配置对象RuleDefinition
@Data
@Builder
@NoArgsConstructor
@AllArgsConstructor
static class RuleDefinition {
    private String beanName;
    private Boolean isPackage;
    private String methodName;
    private String args;
}
```

# 目标接口

```java 
// 游戏接口
interface GameService {
    void play(Map<String, Object> params);
}
```

# 目标接口实现

```java 
// 游戏接口实现
static class DefaultGameServiceImpl implements GameService {

    @Override
    public void play(Map<String, Object> params) {
        final Set<Map.Entry<String, Object>> entries = params.entrySet();
        for (Map.Entry<String, Object> entry : entries) {
            System.out.println(entry.getKey() + " : " + entry.getValue());
        }
    }
}
```

# 执行器上下文

```java 
// 执行器上下文
static class ExecutorContext {

    public static void exec(RuleDefinition definition) throws Exception {
        final Executor executor = loadExecutor(definition);
        executor.exec(definition);
    }

    private static Executor loadExecutor(RuleDefinition definition) {
        BeanDefinitionType type = definition.isPackage ? BeanDefinitionType.REFERENCE : BeanDefinitionType.SPRING;

        Executor executor;
        switch (type) {
            case REFERENCE:
                executor = new ReferenceExecutor();
                break;
            case SPRING:
                executor = new SpringExecutor();
                break;
            default:
                executor = Executor.defaultImpl;
        }
        return executor;
    }
}
```

# 执行器接口

```java 
// 执行器接口
interface Executor {
    Executor defaultImpl = definition -> {
    };

    void exec(RuleDefinition definition) throws Exception;
}
```

# Java 反射执行器实现

```java 
// Java反射执行器实现
static class ReferenceExecutor implements Executor {

    @Override
    public void exec(RuleDefinition definition) throws Exception {
        final String beanName = definition.getBeanName();
        final String methodName = definition.getMethodName();
        final String defineArgs = definition.getArgs();
        final Map<String, Object> defineArgsMap = JSON.parseObject(defineArgs, new TypeReference<HashMap<String, Object>>() {
        });

        final Class<?> clazz = Class.forName(beanName);
        final Object instance = clazz.newInstance();
        final Method method = clazz.getDeclaredMethod(methodName, Map.class);
        method.invoke(instance, defineArgsMap);
    }
}
```

# Spring 容器执行器

```java 
// spring容器执行器
static class SpringExecutor implements Executor, ApplicationContextAware {
    private ApplicationContext applicationContext;

    @Override
    public void exec(RuleDefinition definition) throws Exception {
        final String beanName = definition.getBeanName();
        final String methodName = definition.getMethodName();
        final String defineArgs = definition.getArgs();
        final Map<String, Object> defineArgsMap = JSON.parseObject(defineArgs, new TypeReference<HashMap<String, Object>>() {
        });

        final Object bean = applicationContext.getBean(beanName);
        final Class<?> clazz = bean.getClass();
        final Method method = clazz.getDeclaredMethod(methodName, Map.class);
        method.invoke(bean, defineArgsMap);
    }

    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        this.applicationContext = applicationContext;
    }
}
```

# 执行器类型

```java 
// 执行器类型
enum BeanDefinitionType {

    SPRING("spring", "spring容器获取"),
    REFERENCE("reference", "原生反射获取");

    private final String name;
    private final String description;

    BeanDefinitionType(String name, String description) {
        this.name = name;
        this.description = description;
    }

    public String getName() {
        return name;
    }

    public String getDescription() {
        return description;
    }
}
```

# 加载配置对象接口

```java 
// 加载配置对象接口
interface LoadBeanDefinition {
    RuleDefinition loadDefinition();
}
```

# 加载配置对象接口实现

```java 
// 加载配置对象接口实现
static class LoadBeanDefinitionImpl implements LoadBeanDefinition {
    private static final LoadBeanDefinition loadBeanDefinition = new LoadBeanDefinitionImpl();

    public static LoadBeanDefinition instance() {
        return loadBeanDefinition;
    }

    @Override
    public RuleDefinition loadDefinition() {
        return RuleDefinition.builder()
                .beanName("com.alibaba.frame.a.A_109$DefaultGameServiceImpl")
                .isPackage(true)
                .methodName("play")
                .args("{\"id\":1001,\"name\":\"张三\",\"age\":30,\"sex\":\"男\"}").build();
    }
}
```

!> 想不出更好的思路，编码水平有限!!! 
