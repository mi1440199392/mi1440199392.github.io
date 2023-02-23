```java 
package com.hundsun.frameworlk.http.config;
import org.springframework.beans.factory.DisposableBean;
import org.springframework.boot.ApplicationArguments;
import org.springframework.boot.ApplicationRunner;
import org.springframework.stereotype.Component;

/**
 * @author hspcadmin
 * @version 1.0
 * @description SpringBoot 自定义项目启动、关闭的监听
 */
@Component
public class BizListener implements ApplicationRunner, DisposableBean {

	@Override
	public void destroy() throws Exception {
		System.out.println("FrameApplication项目关闭...");
	}

	@Override
	public void run(ApplicationArguments args) throws Exception {
		System.out.println("FrameApplication项目启动...");
	}
}
```

```text
D:\JDK8\bin\java.exe -agentlib:jdwp=transport=dt_socket,address=127.0.0.1:51698,suspend=y,server=n -javaagent:C:\Users\hspcadmin\AppData\Local\JetBrains\IdeaIC2021.2\captureAgent\debugger-agent.jar -Dfile.encoding=UTF-8 -classpath "D:\JDK8\jre\lib\charsets.jar;D:\JDK8\jre\lib\deploy.jar;D:\JDK8\jre\lib\ext\access-bridge-64.jar;D:\JDK8\jre\lib\ext\cldrdata.jar;D:\JDK8\jre\lib\ext\dnsns.jar;D:\JDK8\jre\lib\ext\jaccess.jar;D:\JDK8\jre\lib\ext\jfxrt.jar;D:\JDK8\jre\lib\ext\localedata.jar;D:\JDK8\jre\lib\ext\nashorn.jar;D:\JDK8\jre\lib\ext\sunec.jar;D:\JDK8\jre\lib\ext\sunjce_provider.jar;D:\JDK8\jre\lib\ext\sunmscapi.jar;D:\JDK8\jre\lib\ext\sunpkcs11.jar;D:\JDK8\jre\lib\ext\zipfs.jar;D:\JDK8\jre\lib\javaws.jar;D:\JDK8\jre\lib\jce.jar;D:\JDK8\jre\lib\jfr.jar;D:\JDK8\jre\lib\jfxswt.jar;D:\JDK8\jre\lib\jsse.jar;D:\JDK8\jre\lib\management-agent.jar;D:\JDK8\jre\lib\plugin.jar;D:\JDK8\jre\lib\resources.jar;D:\JDK8\jre\lib\rt.jar;E:\frame\target\classes;D:\maven-repository\org\springframework\boot\spring-boot-starter-web\2.2.2.RELEASE\spring-boot-starter-web-2.2.2.RELEASE.jar;D:\maven-repository\org\springframework\boot\spring-boot-starter\2.2.2.RELEASE\spring-boot-starter-2.2.2.RELEASE.jar;D:\maven-repository\org\springframework\boot\spring-boot\2.2.2.RELEASE\spring-boot-2.2.2.RELEASE.jar;D:\maven-repository\org\springframework\boot\spring-boot-autoconfigure\2.2.2.RELEASE\spring-boot-autoconfigure-2.2.2.RELEASE.jar;D:\maven-repository\org\springframework\boot\spring-boot-starter-logging\2.2.2.RELEASE\spring-boot-starter-logging-2.2.2.RELEASE.jar;D:\maven-repository\ch\qos\logback\logback-classic\1.2.3\logback-classic-1.2.3.jar;D:\maven-repository\ch\qos\logback\logback-core\1.2.3\logback-core-1.2.3.jar;D:\maven-repository\org\slf4j\slf4j-api\1.7.29\slf4j-api-1.7.29.jar;D:\maven-repository\org\apache\logging\log4j\log4j-to-slf4j\2.12.1\log4j-to-slf4j-2.12.1.jar;D:\maven-repository\org\apache\logging\log4j\log4j-api\2.12.1\log4j-api-2.12.1.jar;D:\maven-repository\org\slf4j\jul-to-slf4j\1.7.29\jul-to-slf4j-1.7.29.jar;D:\maven-repository\jakarta\annotation\jakarta.annotation-api\1.3.5\jakarta.annotation-api-1.3.5.jar;D:\maven-repository\org\springframework\spring-core\5.2.2.RELEASE\spring-core-5.2.2.RELEASE.jar;D:\maven-repository\org\springframework\spring-jcl\5.2.2.RELEASE\spring-jcl-5.2.2.RELEASE.jar;D:\maven-repository\org\yaml\snakeyaml\1.25\snakeyaml-1.25.jar;D:\maven-repository\org\springframework\boot\spring-boot-starter-json\2.2.2.RELEASE\spring-boot-starter-json-2.2.2.RELEASE.jar;D:\maven-repository\com\fasterxml\jackson\core\jackson-databind\2.10.1\jackson-databind-2.10.1.jar;D:\maven-repository\com\fasterxml\jackson\core\jackson-annotations\2.10.1\jackson-annotations-2.10.1.jar;D:\maven-repository\com\fasterxml\jackson\core\jackson-core\2.10.1\jackson-core-2.10.1.jar;D:\maven-repository\com\fasterxml\jackson\datatype\jackson-datatype-jdk8\2.10.1\jackson-datatype-jdk8-2.10.1.jar;D:\maven-repository\com\fasterxml\jackson\datatype\jackson-datatype-jsr310\2.10.1\jackson-datatype-jsr310-2.10.1.jar;D:\maven-repository\com\fasterxml\jackson\module\jackson-module-parameter-names\2.10.1\jackson-module-parameter-names-2.10.1.jar;D:\maven-repository\org\springframework\boot\spring-boot-starter-tomcat\2.2.2.RELEASE\spring-boot-starter-tomcat-2.2.2.RELEASE.jar;D:\maven-repository\org\apache\tomcat\embed\tomcat-embed-core\9.0.29\tomcat-embed-core-9.0.29.jar;D:\maven-repository\org\apache\tomcat\embed\tomcat-embed-el\9.0.29\tomcat-embed-el-9.0.29.jar;D:\maven-repository\org\apache\tomcat\embed\tomcat-embed-websocket\9.0.29\tomcat-embed-websocket-9.0.29.jar;D:\maven-repository\org\springframework\boot\spring-boot-starter-validation\2.2.2.RELEASE\spring-boot-starter-validation-2.2.2.RELEASE.jar;D:\maven-repository\jakarta\validation\jakarta.validation-api\2.0.1\jakarta.validation-api-2.0.1.jar;D:\maven-repository\org\hibernate\validator\hibernate-validator\6.0.18.Final\hibernate-validator-6.0.18.Final.jar;D:\maven-repository\org\jboss\logging\jboss-logging\3.4.1.Final\jboss-logging-3.4.1.Final.jar;D:\maven-repository\com\fasterxml\classmate\1.5.1\classmate-1.5.1.jar;D:\maven-repository\org\springframework\spring-web\5.2.2.RELEASE\spring-web-5.2.2.RELEASE.jar;D:\maven-repository\org\springframework\spring-beans\5.2.2.RELEASE\spring-beans-5.2.2.RELEASE.jar;D:\maven-repository\org\springframework\spring-webmvc\5.2.2.RELEASE\spring-webmvc-5.2.2.RELEASE.jar;D:\maven-repository\org\springframework\spring-aop\5.2.2.RELEASE\spring-aop-5.2.2.RELEASE.jar;D:\maven-repository\org\springframework\spring-context\5.2.2.RELEASE\spring-context-5.2.2.RELEASE.jar;D:\maven-repository\org\springframework\spring-expression\5.2.2.RELEASE\spring-expression-5.2.2.RELEASE.jar;D:\maven-repository\org\projectlombok\lombok\1.18.10\lombok-1.18.10.jar;D:\maven-repository\org\dom4j\dom4j\2.1.3\dom4j-2.1.3.jar;D:\maven-repository\cn\hutool\hutool-core\5.8.5\hutool-core-5.8.5.jar;D:\maven-repository\org\apache\commons\commons-lang3\3.9\commons-lang3-3.9.jar;D:\maven-repository\commons-io\commons-io\2.11.0\commons-io-2.11.0.jar;D:\maven-repository\cn\dev33\sa-token-spring-boot-starter\1.33.0\sa-token-spring-boot-starter-1.33.0.jar;D:\maven-repository\cn\dev33\sa-token-servlet\1.33.0\sa-token-servlet-1.33.0.jar;D:\maven-repository\cn\dev33\sa-token-core\1.33.0\sa-token-core-1.33.0.jar;D:\maven-repository\com\alibaba\fastjson\1.2.83\fastjson-1.2.83.jar;D:\ideaIC-2021.2.2\IntelliJ IDEA Community Edition 2021.2.2\lib\idea_rt.jar" com.hundsun.frameworlk.FrameApplication
Connected to the target VM, address: '127.0.0.1:51698', transport: 'socket'

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v2.2.2.RELEASE)

2023-02-04 03:08:53.795  INFO 6568 --- [           main] com.hundsun.frameworlk.FrameApplication  : Starting FrameApplication on DESKTOP-UISKIQ3 with PID 6568 (E:\frame\target\classes started by hspcadmin in E:\frame)
2023-02-04 03:08:53.798  INFO 6568 --- [           main] com.hundsun.frameworlk.FrameApplication  : No active profile set, falling back to default profiles: default
2023-02-04 03:08:55.222  INFO 6568 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port(s): 8080 (http)
2023-02-04 03:08:55.233  INFO 6568 --- [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
2023-02-04 03:08:55.233  INFO 6568 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.29]
2023-02-04 03:08:55.360  INFO 6568 --- [           main] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2023-02-04 03:08:55.360  INFO 6568 --- [           main] o.s.web.context.ContextLoader            : Root WebApplicationContext: initialization completed in 1515 ms
2023-02-04 03:08:55.583  INFO 6568 --- [           main] o.s.s.concurrent.ThreadPoolTaskExecutor  : Initializing ExecutorService 'applicationTaskExecutor'
____ ____    ___ ____ _  _ ____ _  _ 
[__  |__| __  |  |  | |_/  |___ |\ | 
___] |  |     |  |__| | \_ |___ | \| 
https://sa-token.cc (v1.33.0)
2023-02-04 03:08:55.806  INFO 6568 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''
2023-02-04 03:08:55.808  INFO 6568 --- [           main] com.hundsun.frameworlk.FrameApplication  : Started FrameApplication in 2.415 seconds (JVM running for 3.428)
FrameApplication项目启动...
Disconnected from the target VM, address: '127.0.0.1:51698', transport: 'socket'
FrameApplication项目关闭...
```
