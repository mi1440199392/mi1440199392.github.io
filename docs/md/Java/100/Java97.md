# 前言

<font face="幼圆">

> Spring动态数据源切换 (AbstractRoutingDataSource)

</font>

# 多数据源注解标识

```java 
package com.alibaba.frame.web.annotation;
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;
import com.alibaba.frame.web.enums.DataSourceType;

/**
 * @description: 多数据源
 *
 * @date: 2023-07-23
 */
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface DataSource {

	/**
	 * 切换数据源
	 * 
	 * @return
	 */
	DataSourceType dataSource() default DataSourceType.MASTER;
}
```

# 多数据源枚举

```java 
package com.alibaba.frame.web.enums;

/**
 * @description: 多数据源
 * @date: 2023-07-23
 */
public enum DataSourceType {

	MASTER("master", "主库"), SLAVE("slave", "从库");

	private final String value;
	private final String msg;

	DataSourceType(String value, String msg) {
		this.value = value;
		this.msg = msg;
	}

	public String getValue() {
		return value;
	}
}
```

# 多数据源配置类

```java 
package com.alibaba.frame.web.config;
import javax.sql.DataSource;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import com.alibaba.druid.spring.boot.autoconfigure.DruidDataSourceBuilder;

/**
 * @description: 多数据源配置
 *
 * @date: 2023-07-23
 */
@Configuration
public class DataSourceConfig {

	@Bean
	public DataSource masterDataSource() {
		return DruidDataSourceBuilder.create().build();
	}

	@Bean
	public DataSource slaveDataSource() {
		return DruidDataSourceBuilder.create().build();
	}
}
```

# 动态数据源路由策略

```java 
package com.alibaba.frame.web.bean.jdbc;
import java.util.Map;
import javax.sql.DataSource;
import org.springframework.jdbc.datasource.lookup.AbstractRoutingDataSource;
import org.springframework.stereotype.Component;
import org.springframework.util.CollectionUtils;
import com.alibaba.frame.web.enums.DataSourceType;

/**
 * @description: 动态数据源配置
 *
 * @date: 2023-07-23
 */
@Component
public class DynamicDataSourceManager extends AbstractRoutingDataSource {
	// 线程副本：默认主库；动态数据源切换key
	private static final ThreadLocal<String> dataSourceKeyLocal = ThreadLocal.withInitial(DataSourceType.MASTER::getValue);

	public static void setDataSourceKey(String dataSourceKey) {
		dataSourceKeyLocal.set(dataSourceKey);
	}

	public static String getDataSourceKey() {
		return dataSourceKeyLocal.get();
	}

	public static void closeDataSourceKey() {
		dataSourceKeyLocal.remove();
	}

	@Override
	protected Object determineCurrentLookupKey() {
		return DynamicDataSourceManager.getDataSourceKey();
	}

    // 注入主库数据源，从库数据源
	public DynamicDataSourceManager(DataSource masterDataSource, DataSource slaveDataSource) {
		// 设置默认数据源：主库
		super.setDefaultTargetDataSource(masterDataSource);

		// 添加所有数据源上下文，供切换使用
		Map<Object, Object> targetDataSources = CollectionUtils.newHashMap(2);
		targetDataSources.put(DataSourceType.MASTER.getValue(), masterDataSource);
		targetDataSources.put(DataSourceType.SLAVE.getValue(), slaveDataSource);
		super.setTargetDataSources(targetDataSources);
		super.afterPropertiesSet();
	}
}
```

# 动态数据源Aop切面

```java 
package com.alibaba.frame.web.aop;
import java.util.Objects;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Pointcut;
import org.aspectj.lang.reflect.MethodSignature;
import org.springframework.core.annotation.AnnotationUtils;
import org.springframework.core.annotation.Order;
import org.springframework.stereotype.Component;
import com.alibaba.frame.web.annotation.DataSource;
import com.alibaba.frame.web.bean.jdbc.DynamicDataSourceManager;

/**
 * @description: 动态数据源切面
 *
 * @date: 2023-07-23
 */
@Aspect
@Component
@Order(1)
public class DataSourceAspectJ {

	/**
	 * 拦截Service层，确定走主库、从库数据源
	 */
	@Pointcut("@annotation(com.alibaba.frame.web.annotation.DataSource)")
	public void pointcut() {
	}

	@Around("pointcut()")
	public Object dataSourceAround(ProceedingJoinPoint point) throws Throwable {
		DataSource dataSourceAnnotation = this.findDataSourceAnnotation(point);
		// 设置主库、从库的数据源key
		if (!Objects.isNull(dataSourceAnnotation)) {
			DynamicDataSourceManager.setDataSourceKey(dataSourceAnnotation.dataSource().getValue());
		}

		try {
			// 执行目标方法
			return point.proceed();
		} finally {
			// 执行完Sql后，清除当前线程持有的数据源key
			DynamicDataSourceManager.closeDataSourceKey();
		}
	}

	private DataSource findDataSourceAnnotation(ProceedingJoinPoint point) {
		MethodSignature signature = (MethodSignature) point.getSignature();
		return AnnotationUtils.findAnnotation(signature.getMethod(), DataSource.class);
	}
}
```

# Service目标类

```java 
package com.alibaba.frame.web.service.impl;
import com.alibaba.frame.web.annotation.DataSource;
import com.alibaba.frame.web.bean.User;
import com.alibaba.frame.web.enums.DataSourceType;
import com.alibaba.frame.web.service.UserService;
import javax.inject.Named;

@Named
public class UserServiceImpl implements UserService {

	/**
	 * 1、查询用户信息
	 * 2、查询走从库
	 * @return
	 */
	@Override
	@DataSource(dataSource = DataSourceType.SLAVE)
	public String getUserInfo() {
		return "查询用户信息";
	}

	/**
	 * 1、新增用户信息
	 * 2、新增走主库
	 * @param user
	 * @return
	 */
	@Override
	@DataSource(dataSource = DataSourceType.MASTER)
	public int insertUserInfo(User user) {
		return 0;
	}
}
```
