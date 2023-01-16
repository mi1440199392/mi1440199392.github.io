# SpringBoot - 自定义 HandlerInterceptor 拦截器 - 参数校验

>  SpringBoot HandlerInterceptor 拦截器的使用，平常项目开发过程中，会遇到登录拦截，权限校验，参数处理，防重复提交等问题，那拦截器就能帮我们统一处理这些问题。

# HandlerInterceptor 方法介绍

```java 
public interface HandlerInterceptor {
    default boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        return true;
    }

    default void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, @Nullable ModelAndView modelAndView) throws Exception {
    }

    default void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, @Nullable Exception ex) throws Exception {
    }
}
```

- preHandle：预处理，在业务处理器处理请求之前被调用，可以进行登录拦截，编码处理、安全控制、权限校验等处理。
- postHandle：后处理，在业务处理器处理请求执行完成后，生成视图之前被调用。即调用了 Service 并返回 ModelAndView 但未进行页面渲染，可以修改 ModelAndView 这个比较少用。
- afterCompletion：返回处理，在 DispatcherServlet 完全处理完请求后被调用，可用于清理资源等。已经渲染了页面。



# 项目结构

```text
├─src
│  ├─main
│  │  ├─java
│  │  │  └─com
│  │  │      └─hundsun
│  │  │          └─frameworlk
│  │  │              ├─a
│  │  │              ├─http
│  │  │              │  ├─controller
│  │  │              │  └─policy
│  │  │              │      └─impl
│  │  │              └─token
│  │  │                  ├─author
│  │  │                  ├─config
│  │  │                  ├─controller
│  │  │                  ├─domain
│  │  │                  │  ├─constant
│  │  │                  │  ├─model
│  │  │                  │  ├─request
│  │  │                  │  │  └─dto
│  │  │                  │  └─response
│  │  │                  ├─exception
│  │  │                  ├─function
│  │  │                  ├─handle
│  │  │                  └─util
│  │  └─resources
│  │      ├─log
│  │      ├─static
│  │      └─templates
│  └─test
│      └─java
│          └─com
│              └─hundsun
│                  └─frameworlk
└─target
    ├─classes
    │  ├─com
    │  │  └─hundsun
    │  │      └─frameworlk
    │  │          ├─a
    │  │          ├─http
    │  │          │  ├─controller
    │  │          │  └─policy
    │  │          │      └─impl
    │  │          └─token
    │  │              ├─author
    │  │              ├─config
    │  │              ├─controller
    │  │              ├─domain
    │  │              │  ├─constant
    │  │              │  ├─model
    │  │              │  ├─request
    │  │              │  │  └─dto
    │  │              │  └─response
    │  │              ├─exception
    │  │              ├─function
    │  │              ├─handle
    │  │              └─util
    │  └─log
    ├─generated-sources
    │  └─annotations
    ├─generated-test-sources
    │  └─test-annotations
    ├─maven-status
    │  └─maven-compiler-plugin
    │      └─compile
    │          └─default-compile
    └─test-classes
```

# 项目源码

> pom依赖

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
    <artifactId>frameworlk</artifactId>
    <version>0.0.1-SNAPSHOT</version>

    <properties>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>

        <dependency>
            <groupId>org.dom4j</groupId>
            <artifactId>dom4j</artifactId>
            <version>2.1.3</version>
        </dependency>

        <dependency>
            <groupId>cn.hutool</groupId>
            <artifactId>hutool-core</artifactId>
            <version>5.8.5</version>
        </dependency>


        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-lang3</artifactId>
        </dependency>

        <!-- https://mvnrepository.com/artifact/commons-io/commons-io -->
        <dependency>
            <groupId>commons-io</groupId>
            <artifactId>commons-io</artifactId>
            <version>2.11.0</version>
        </dependency>

        <!-- fastjson -->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.2.83</version>
        </dependency>
    </dependencies>
</project>
```

```java 
package com.hundsun.frameworlk.http.policy;

/**
 * @author hspcadmin
 * @version 1.0
 * @description 游戏
 */
public interface Game {

	void play();
}
```

```java 
package com.hundsun.frameworlk.http.policy.impl;
import com.hundsun.frameworlk.http.policy.Game;
import org.springframework.stereotype.Component;

/**
 * @author hspcadmin
 * @version 1.0
 * @description CF
 */
@Component("cf")
public class CF implements Game {

	@Override
	public void play() {
		System.out.println("玩CF游戏...");
	}
}
```

```java 
package com.hundsun.frameworlk.http.policy.impl;
import com.hundsun.frameworlk.http.policy.Game;
import org.springframework.stereotype.Component;

/**
 * @author hspcadmin
 * @version 1.0
 * @description CS
 */
@Component("cs")
public class CS implements Game {

	@Override
	public void play() {
		System.out.println("正在玩CS游戏...");
	}
}
```

```java 
package com.hundsun.frameworlk.http.policy.impl;
import com.hundsun.frameworlk.http.policy.Game;
import org.springframework.stereotype.Component;

/**
 * @author hspcadmin
 * @version 1.0
 * @description DNF
 */
@Component("dnf")
public class DNF implements Game {

	@Override
	public void play() {
		System.out.println("正在玩DNF游戏...");
	}
}
```

```java 
package com.hundsun.frameworlk.http.controller;
import com.hundsun.frameworlk.http.policy.Game;
import com.hundsun.frameworlk.token.domain.request.Request;
import com.hundsun.frameworlk.token.domain.request.dto.GameRequestDTO;
import com.hundsun.frameworlk.token.domain.response.Response;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RestController;
import java.util.Map;

/**
 * @author hspcadmin
 * @version 1.0
 * @description
 */
@RestController
public class GameController {

	// 注入Bean
	private final Map<String, Game> games;

	public GameController(Map<String, Game> games) {
		this.games = games;
	}

	/**
	 * 玩游戏
	 *
	 * @param <T> t
	 * @return response
	 */
	@GetMapping("/play")
	public <T> Response<T> play() {
		games.get("dnf").play();
		return Response.ok(null);
	}

	/**
	 * 保存
	 *
	 * @param <T>
	 * @return
	 */
	@PostMapping("/game")
	public <T> Response<T> game(@RequestBody Request<GameRequestDTO> request) {
		System.out.println("访问game()，request = " + request);
		return Response.ok(null);
	}
}
```

> 自定义 HandlerInterceptor 拦截器 - 参数校验

```java 
package com.hundsun.frameworlk.token.handle;
import com.alibaba.fastjson.JSON;
import com.hundsun.frameworlk.token.domain.constant.Constant;
import com.hundsun.frameworlk.token.domain.request.Request;
import com.hundsun.frameworlk.token.domain.request.dto.GameRequestDTO;
import com.hundsun.frameworlk.token.exception.ParamsCheckedException;
import com.hundsun.frameworlk.token.util.ParamsCheckedUtil;
import org.apache.commons.io.IOUtils;
import org.springframework.web.servlet.HandlerInterceptor;
import javax.servlet.ServletInputStream;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.ByteArrayOutputStream;
import java.io.IOException;

/**
 * @author hspcadmin
 * @version 1.0
 * @description 参数校验拦截器
 */
@SuppressWarnings({"all"})
public class ParamsCheckedInterceptor implements HandlerInterceptor {


	/**
	 * 参数校验
	 *
	 * @param request
	 * @param response
	 * @param handler
	 * @return
	 * @throws Exception
	 */
	@Override
	public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {

		final Integer dbId = 1001;
		final String dbName = "CF";

		final Request requestBody = this.getRequestBody(request);
		final GameRequestDTO body = JSON.parseObject(JSON.toJSONString(requestBody.getBody()), GameRequestDTO.class);

		// 参数校验
		ParamsCheckedUtil.builder()
				.checked(() -> body.getId() == null)
				.checked(() -> body.getName() == null)
				.checked(() -> !dbId.equals(body.getId()))
				.checked(() -> !dbName.equals(body.getName()))
				.error(() -> {
					throw new ParamsCheckedException(Constant.error, Constant.paramsCheckedErrorMsg);
				});
		return true;
	}

	/**
	 * 获取POST请求体Body
	 *
	 * @param request
	 * @return
	 */
	private Request getRequestBody(HttpServletRequest request) {

		try (ByteArrayOutputStream os = new ByteArrayOutputStream()) {
			ServletInputStream is = request.getInputStream();
			IOUtils.copy(is, os);
			return JSON.parseObject(os.toString(), Request.class);
		} catch (IOException e) {
			e.printStackTrace();
			return null;
		}
	}
}
```

> WebMvcConfigurer 注册参数校验拦截器

```java 
package com.hundsun.frameworlk.token.config;
import com.hundsun.frameworlk.token.handle.ParamsCheckedInterceptor;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

/**
 * @author hspcadmin
 * @version 1.0
 * @description 注册参数校验拦截器
 */
@Configuration
public class ParamsCheckedHandleConfigure implements WebMvcConfigurer {


	/**
	 * 参数校验拦截
	 *
	 * @param registry
	 */
	@Override
	public void addInterceptors(InterceptorRegistry registry) {

		// 注册ParamsCheckedInterceptor拦截器，拦截/game
		registry.addInterceptor(new ParamsCheckedInterceptor()).addPathPatterns("/game");
	}
}
```

> 自定义业务异常

```java 
package com.hundsun.frameworlk.token.exception;

/**
 * @author hspcadmin
 * @version 1.0
 * @description 参数校验异常
 */
public class ParamsCheckedException extends RuntimeException {

	private Integer errorCode;
	private String errorMsg;

	public ParamsCheckedException(Integer errorCode, String errorMsg) {
		super(errorMsg);
		this.errorCode = errorCode;
		this.errorMsg = errorMsg;
	}

	public Integer getErrorCode() {
		return errorCode;
	}

	public void setErrorCode(Integer errorCode) {
		this.errorCode = errorCode;
	}

	public String getErrorMsg() {
		return errorMsg;
	}

	public void setErrorMsg(String errorMsg) {
		this.errorMsg = errorMsg;
	}
}
```

> 定义统一请求、响应对象

```java 
package com.hundsun.frameworlk.token.domain.model;
import java.io.Serializable;

public interface Model extends Serializable {

}
```

```java 
package com.hundsun.frameworlk.token.domain.request;
import com.hundsun.frameworlk.token.domain.model.Model;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.Setter;
import lombok.ToString;

@AllArgsConstructor
@NoArgsConstructor
@ToString
@Data
@Setter
@Getter
public class Request<T> implements Model {

	private String operatorCode;
	private T body;
}
```

```java 
package com.hundsun.frameworlk.token.domain.response;
import com.hundsun.frameworlk.token.domain.constant.Constant;
import com.hundsun.frameworlk.token.domain.model.Model;
import lombok.Getter;
import lombok.Setter;
import lombok.ToString;
import java.util.function.Supplier;


@ToString
@Getter
@Setter
public class Response<T> implements Model {

	private Integer code;
	private String message;
	private T data;

	private Response(Integer code, String message, T data) {
		this.code = code;
		this.message = message;
		this.data = data;
	}

	private Response() {
	}

	public Response<T> code(Integer code) {
		this.code = code;
		return this;
	}

	public Response<T> message(String message) {
		this.message = message;
		return this;
	}


	public Response<T> data(T data) {
		this.data = data;
		return this;
	}

	public static <T> Response<T> ok(T data) {
		return new Response<>(Constant.ok, Constant.success, data);
	}

	public static <T> Response<T> error() {
		return new Response<>(Constant.error, Constant.errorMsg, null);
	}

	public static <T> Response<T> error(String msg) {
		return new Response<>(Constant.error, msg, null);
	}

	public static <T> Response<T> error(Supplier<String> supplier) {
		return new Response<>(Constant.error, supplier.get(), null);
	}

	public static <T> boolean isOk(Response<T> response) {
		return Constant.ok.equals(response.getCode());
	}

	public static <T> boolean isError(Response<T> response) {
		return !Constant.ok.equals(response.getCode());
	}
}
```

> 定义常量

```java 
package com.hundsun.frameworlk.token.domain.constant;

public interface Constant {

	Integer ok = 0;
	String success = "success";
	Integer error = 9999;
	String errorMsg = "error";
	String paramsCheckedErrorMsg = "参数校验失败";
	String fileName = "src/main/resources/log/token-error.log";
}
```

> 定义函数接口

```java 
package com.hundsun.frameworlk.token.function;

/**
 * @author hspcadmin
 * @version 1.0
 * @description ParamsCheckedFunction：参数校验
 */
@FunctionalInterface
public interface ParamsCheckedFunction {
	ParamsCheckedFunction defaultValue = () -> false;

	boolean checked();

	default void error(ErrorFunction function) {
		if (checked()) {
			function.error();
		}
	}
}
```

```java 
package com.hundsun.frameworlk.token.function;

/**
 * @author hspcadmin
 * @version 1.0
 * @description ErrorFunction：抛异常
 */
@FunctionalInterface
public interface ErrorFunction {

	void error();

}
```

> 定义参数校验工具

```java 
package com.hundsun.frameworlk.token.util;
import com.hundsun.frameworlk.token.function.ErrorFunction;
import com.hundsun.frameworlk.token.function.ParamsCheckedFunction;
import java.util.ArrayList;
import java.util.List;

/**
 * @author hspcadmin
 * @version 1.0
 * @description 参数校验工具
 */
public class ParamsCheckedUtil {

	private final List<ParamsCheckedFunction> functions = new ArrayList<>(10);

	public ParamsCheckedUtil checked(ParamsCheckedFunction function) {
		this.register(function);
		return this;
	}

	public void error(ErrorFunction errorFunction) {
		for (ParamsCheckedFunction function : functions) {
			function.error(errorFunction);
		}
	}

	private void register(ParamsCheckedFunction function) {
		functions.add(function);
	}

	private static ParamsCheckedUtil newInstance() {
		return new ParamsCheckedUtil();
	}

	public static ParamsCheckedUtil builder() {
		return ParamsCheckedUtil.newInstance();
	}

	private ParamsCheckedUtil() {
	}
}
```

> 定义全局异常处理器

```java 
package com.hundsun.frameworlk.token.handle;
import com.hundsun.frameworlk.token.domain.constant.Constant;
import com.hundsun.frameworlk.token.domain.response.Response;
import com.hundsun.frameworlk.token.exception.ParamsCheckedException;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.PrintStream;

// 全局异常处理器
@RestControllerAdvice
public class WebExceptionHandle {

	/**
	 * 拦截参数校验异常
	 *
	 * @param e
	 * @param <R>
	 * @return
	 */
	@ExceptionHandler(value = {ParamsCheckedException.class})
	public <R> Response<R> gameCheckedException(ParamsCheckedException e) {

		try (FileOutputStream os = new FileOutputStream(Constant.fileName, true);
			 PrintStream ps = new PrintStream(os, true)) {
			e.printStackTrace(ps);
		} catch (IOException ex) {
			ex.printStackTrace();
		}
		return Response.error(e.getErrorMsg());
	}
}
```

