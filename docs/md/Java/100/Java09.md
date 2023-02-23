```xml

<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.6.2</version>
		<relativePath/>
	</parent>
	<groupId>com.hundsun</groupId>
	<artifactId>springboot-async</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<name>springboot-async</name>
	<properties>
		<java.version>1.8</java.version>
	</properties>
	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>

</project>

```

```java

package com.hundsun.springbootasync.service;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.scheduling.annotation.Async;
import org.springframework.stereotype.Component;

@Component
public class MainApp {

    private Logger logger = LoggerFactory.getLogger(getClass());

    @Async
    public void asyncShow01() {
        sleep();
        logger.info("异步方法内部线程名称：{}", Thread.currentThread().getName());
    }

    public void syncShow02() {
        sleep();
        logger.info("同步方法内部线程名称：{}", Thread.currentThread().getName());
    }

    public void sleep() {
        long number = 0;
        for (long i = 0; i < 100000000; i++) {
            number+=i;
        }
        logger.info("总和：{}",number);
    }
}

```

```java

package com.hundsun.springbootasync;

import org.junit.jupiter.api.Test;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.scheduling.annotation.EnableAsync;

import com.hundsun.springbootasync.service.MainApp;

@EnableAsync // 开启异步支持
@SpringBootTest
class SpringbootAsyncApplicationTests {

    private Logger logger = LoggerFactory.getLogger(this.getClass());

    @Autowired
    private MainApp mainApp;

    @Test
    void test01() {
        long start = System.currentTimeMillis();
        mainApp.asyncShow01();
        long end = System.currentTimeMillis();
        logger.info("总耗时：{} ms", end - start);
    }

    @Test
    void test02() {
        long start = System.currentTimeMillis();
        mainApp.syncShow02();
        long end = System.currentTimeMillis();
        logger.info("总耗时：{} ms", end - start);
    }

}

```