# 前言

<font face="幼圆">

> log4j2日志框架入门

</font>

# pom依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.2.RELEASE</version>
        <relativePath/>
    </parent>
    <groupId>com.hundsun</groupId>
    <artifactId>frame</artifactId>
    <version>0.0.1-SNAPSHOT</version>

    <properties>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <!-- springBoot默认集成logback日志，需排除掉该依赖 -->
            <exclusions>
                <exclusion>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-logging</artifactId>
                </exclusion>
            </exclusions>
        </dependency>

        <!-- junit -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <scope>test</scope>
        </dependency>

        <!-- 引入log4j2依赖 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-log4j2</artifactId>
        </dependency>
    </dependencies>
</project>
```

# log4j2.xml配置

<font face="幼圆">

> `log4j2.xml`放在`resources`目录下

</font>

```xml
<?xml version="1.0" encoding="UTF-8" ?>


<!--
    <configuration>
        status：记录到控制台的内部Log4j事件的级别。此属性的有效值为"trace"，"debug"，"info"，"warn"，"error"和"fatal"。
                Log4j将有关初始化、过渡和其他内部操作的详细信息记录到状态记录器中。
                如果需要对log4j进行故障排除，则可设置status =" trace"（或者，设置系统属性log4j2.debug也会将内部Log4j2日志记录打印到控制台，包括在找到配置文件之前进行的内部日志记录。）

        monitorInterval：从文件配置后，Log4j能够自动检测对配置文件的更改并自行重新配置。
                        如果在配置元素上指定了monitorInterval属性，并且将其设置为非零值，则下次评估和/或记录日志事件并且自上次检查以来已经过monitorInterval时，将检查文件。
                        下例显示了如何配置属性，以便仅在至少30秒之后检查配置文件的更改。最小间隔为5秒
 -->
<configuration status="warn">

    <!--
        <properties>：全局配置，Log4j2支持在配置中定义全局属性，name属性必须存在且唯一，允许使用${LOG_HOME}来引用该变量。
    -->
    <properties>
        <property name="LOG_HOME">./log</property>
    </properties>


    <!--
        <Appenders>：负责将日志传送至配置目的地，配置日志打印内容、格式、方式、保存策略等。
                    可以使用特定的appender标签名称或者<appender>标签+type属性来配置appender，都必须有name属性，且该name值唯一，该name将在<Logger>标签中被使用。

                    常用标签：
                        <Console>：控制台日志打印配置
                        <File>：最普通的文件日志打印配置
                        <RollingFileAppender>：增加了缓存和滚动策略的文件日志打印配置
                        <RollingRandomAccessFile>：RollingRandomAccessFileAppender与RollingFileAppender相似，除了它会被缓存且在内部它使用ByteBuffer + RandomAccessFile而不是BufferedOutputStream。
                                                    与RollingFileAppender相比，性能提高了20-200％。
    -->
    <Appenders>
        <!-- 输出控制台，SYSTEM_OUT输出黑色，SYSTEM_ERR输出红色 -->
        <Console name="ConsoleAppender" target="SYSTEM_OUT">
            <!-- 日志输出格式 -->
            <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss} [%t] [%-5level] %c{6} -| %m%n"/>
        </Console>

        <!-- 输出到文件 -->
        <File name="FileAppender" fileName="${LOG_HOME}/biz-info.log">
            <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss} [%t] [%-5level] %c{6} -| %m%n"/>
        </File>

        <!-- 文件拆分 -->
        <RollingFile name="RollingFileAppender" fileName="${LOG_HOME}/biz-error.log"
                     filePattern="${LOG_HOME}/$${date:yyyy-MM-dd}/biz-error-%d{yyyy-MM-dd}-%i.log">

            <!-- 日志输出格式 -->
            <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss} [%t] [%-5level] %c{6} -| %m%n"/>

            <!-- 拆分策略 -->
            <Policies>
                <!-- 每过多久生成新的日志文件 -->
                <TimeBasedTriggeringPolicy/>
                <!-- 文件每超过多少，就生成新的日志文件 -->
                <SizeBasedTriggeringPolicy size="10kb"/>
            </Policies>
        </RollingFile>

        <!-- 文件拆分，文件格式为压缩包 -->
        <RollingFile name="RollingFileAppender2" fileName="${LOG_HOME}/biz-error.log"
                     filePattern="${LOG_HOME}/$${date:yyyy-MM-dd}/biz-error-%d{yyyy-MM-dd}-%i.log.gz">

            <!-- 日志输出格式 -->
            <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss} [%t] [%-5level] %c{6} -| %m%n"/>

            <!-- 拆分策略 -->
            <Policies>
                <!-- 每过多久生成新的日志文件 -->
                <TimeBasedTriggeringPolicy/>
                <!-- 文件每超过多少，就生成新的日志文件 -->
                <SizeBasedTriggeringPolicy size="10kb"/>
            </Policies>
        </RollingFile>


        <!--        <Async name="AsyncRollingFile">-->
        <!--                    <AppenderRef ref="RollingFileAppender"/>-->
        <!--                </Async>-->
    </Appenders>


    <!--
        <Loggers>：日志打印配置，只有此处配置了Appender，Appender才会生效。
                  <Logger>：log4j2允许日志打印到一个或多个目标文件，该标签可以配置一个或多个AppenderRef子元素，ref属性值与该name的Appender关联，每一个Appender为一个日志输出目标。
                        name：必填，该项仅打印包含该name的日志信息
                        level：非必填，默认ERROR。日志打印级别，可以配置为TRACE，DEBUG，INFO，WARN，ERROR，ALL或OFF之一
                        additivity：非必填，默认true。该Logger是否附加给Root（此参数详细介绍看<Root>）

                  <Root>：Root用来收集所有的<Logger>设置的日志打印，每个log4j2的配置都必须有，但可缺省，使用默认的root具有error级别，并且仅附加Console控制台打印。
    -->
    <Loggers>

        <!--
            includeLocation=false，关闭日志记录的行号信息，开启的话会严重影响异步输出的性能
            additivity=false，不再继承RootLogger对象
            <AppenderRef ref="AsyncRollingFile"/>，不引用Appender则不生效
         -->
        <!--                <AsyncLogger name="com.alibaba.frame" level="error" includeLocation="false" additivity="false">-->
        <!--                    &lt;!&ndash; <AppenderRef ref="AsyncRollingFile"/> &ndash;&gt;-->
        <!--                </AsyncLogger>-->

        <!-- 同步日志，自定义Logger -->
        <!--        <Logger name="com.alibaba.frame1" level="info" includeLocation="false" additivity="false">-->
        <!--            <AppenderRef ref="ConsoleAppender"/>-->
        <!--        </Logger>-->

        <!-- 配置文件拆分Appender，输出文件格式为.log -->
        <!--        <Logger name="com.alibaba.frame" level="debug" includeLocation="false" additivity="false">-->
        <!--            <AppenderRef ref="RollingFileAppender"/>-->
        <!--        </Logger>-->

        <!-- 配置文件拆分Appender，输出文件格式为.gz -->
        <!--        <Logger name="com.alibaba.frame" level="debug" includeLocation="false" additivity="false">-->
        <!--            <AppenderRef ref="RollingFileAppender2"/>-->
        <!--        </Logger>-->


        <Logger name="com.alibaba.frame" level="info" includeLocation="false" additivity="false">
            <AppenderRef ref="FileAppender"/>
        </Logger>

        <Root level="info">
            <!--            <AppenderRef ref="ConsoleAppender"/>-->
            <!--            <AppenderRef ref="FileAppender"/>-->
            <!--            <AppenderRef ref="RollingFileAppender"/>-->
        </Root>
    </Loggers>
</configuration>
```

# 测试

```java
package com.alibaba.frame;
import org.junit.jupiter.api.Test;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.boot.test.context.SpringBootTest;

/**
 * @author hspcadmin
 * @version 1.0
 * @description
 */
@SpringBootTest
public class FrameApplicationTest2 {
	private static final Logger logger = LoggerFactory.getLogger(FrameApplicationTest2.class);

	@Test
	void test01() {
		for (int i = 0; i < 1000; i++) {
			logger.error("error");
			logger.warn("warn");
			logger.info("info");
			logger.debug("debug");
			logger.trace("trace");
		}
	}
}
```
# 输出日志目录结构

```markdown
├─log
│  │  biz-error.log
│  │  biz-info.log
│  │  
│  └─2023-03-20
│          biz-error-2023-03-20-1.log.gz
│          biz-error-2023-03-20-2.log.gz
│          biz-error-2023-03-20-3.log.gz
│          biz-error-2023-03-20-4.log.gz
│          biz-error-2023-03-20-5.log.gz
│          biz-error-2023-03-20-6.log.gz
│          biz-error-2023-03-20-7.log.gz
│          
├─src
│  ├─main
│  │  ├─java
│  │  │  └─com
│  │  │      └─alibaba
│  │  │          └─frame
│  │  │              │  FrameApplication.java
│  │  │              │  
│  │  │              ├─a
│  │  │              │      A_100.java 
│  │  └─resources
│  │      │  application.properties
│  │      │  log4j2.xml
│  │      │  Spring.xml
│  │      │  
│  │      ├─log
│  │      │      token-error.log
│  │      │      
│  │      ├─static
│  │      └─templates
│  └─test
│      └─java
│          └─com
│              └─alibaba
│                  └─frame
│                          FrameApplicationTest2.java
│                          FrameApplicationTests.java
│
```

# 日志内容

```text
2023-03-20 15:00:43 [main] [INFO ] com.alibaba.frame.FrameApplicationTest2 -| Starting FrameApplicationTest2 on DESKTOP-UISKIQ3 with PID 3024 (started by hspcadmin in E:\frame)
2023-03-20 15:00:43 [main] [INFO ] com.alibaba.frame.FrameApplicationTest2 -| No active profile set, falling back to default profiles: default
2023-03-20 15:00:45 [main] [INFO ] com.alibaba.frame.FrameApplicationTest2 -| Started FrameApplicationTest2 in 2.503 seconds (JVM running for 4.512)
2023-03-20 15:00:45 [main] [ERROR] com.alibaba.frame.FrameApplicationTest2 -| error
2023-03-20 15:00:45 [main] [WARN ] com.alibaba.frame.FrameApplicationTest2 -| warn
2023-03-20 15:00:45 [main] [INFO ] com.alibaba.frame.FrameApplicationTest2 -| info
2023-03-20 15:00:45 [main] [ERROR] com.alibaba.frame.FrameApplicationTest2 -| error
2023-03-20 15:00:45 [main] [WARN ] com.alibaba.frame.FrameApplicationTest2 -| warn
2023-03-20 15:00:45 [main] [INFO ] com.alibaba.frame.FrameApplicationTest2 -| info
2023-03-20 15:00:45 [main] [ERROR] com.alibaba.frame.FrameApplicationTest2 -| error
2023-03-20 15:00:45 [main] [WARN ] com.alibaba.frame.FrameApplicationTest2 -| warn
2023-03-20 15:00:45 [main] [INFO ] com.alibaba.frame.FrameApplicationTest2 -| info
2023-03-20 15:00:45 [main] [ERROR] com.alibaba.frame.FrameApplicationTest2 -| error
2023-03-20 15:00:45 [main] [WARN ] com.alibaba.frame.FrameApplicationTest2 -| warn
2023-03-20 15:00:45 [main] [INFO ] com.alibaba.frame.FrameApplicationTest2 -| info
2023-03-20 15:00:45 [main] [ERROR] com.alibaba.frame.FrameApplicationTest2 -| error
2023-03-20 15:00:45 [main] [WARN ] com.alibaba.frame.FrameApplicationTest2 -| warn
2023-03-20 15:00:45 [main] [INFO ] com.alibaba.frame.FrameApplicationTest2 -| info
2023-03-20 15:00:45 [main] [ERROR] com.alibaba.frame.FrameApplicationTest2 -| error
```

<font face="幼圆">

!> log4j2日志框架的更多配置信息，百度一下

</font>







