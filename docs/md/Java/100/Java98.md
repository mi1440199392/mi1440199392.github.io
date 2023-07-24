# 前言

<font face="幼圆">

> 防重复提交

</font>

# 防重复提交注解

```java 
package com.alibaba.frame.web.annotation;
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

/**
 * @description: 防重复提交
 * @date: 2023-07-23
 */
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface RepeatSubmit {

	// 规定时间内（单位：毫秒），不允许重复提交
	int timeMillis() default 1000;

	String message() default "不允许重复提交，请稍后再试";
}
```

# 拦截器

```java 
package com.alibaba.frame.web.interceptor;
import java.lang.reflect.Method;
import java.nio.charset.StandardCharsets;
import java.util.LinkedHashMap;
import java.util.Objects;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;
import org.springframework.stereotype.Component;
import org.springframework.util.CollectionUtils;
import org.springframework.web.method.HandlerMethod;
import org.springframework.web.servlet.HandlerInterceptor;
import com.alibaba.fastjson.JSON;
import com.alibaba.frame.web.annotation.RepeatSubmit;

/**
 * @description: 防重复提交拦截器
 *
 * @date: 2023-07-23
 */
@Component
public class RepeatSubmitInterceptor implements HandlerInterceptor {
	// 防重复提交 Session 数据
	private static final String REPEAT_SUBMIT_SESSION_KEY = "repeat_submit_data";
	// 请求参数
	private static final String REPEAT_SUBMIT_PARAMS = "repeat_submit_params";
	// 请求时间
	private static final String REPEAT_SUBMIT_REQUEST_TIME = "repeat_submit_request_time";

	@Override
	public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {

		if (handler instanceof HandlerMethod) {
			Method method = ((HandlerMethod) handler).getMethod();
			RepeatSubmit annotation = method.getAnnotation(RepeatSubmit.class);
			if (!Objects.isNull(annotation)) {
				// 判断是否重复提交
				if (isRepeatSubmit(request, annotation)) {
					response.getOutputStream().write(annotation.message().getBytes(StandardCharsets.UTF_8));
					return false;
				}
			}
		}
		return true;
	}

	/**
	 * 判断是否重复提交：
	 * 1、判断请求url和数据是否和上一次相同
	 * 2、比较当前请求参数 和 上次请求参数是否相等
	 * 3、并且，是否在有效时间范围内提交的
	 * @param request
	 * @param annotation
	 * @return
	 */
	@SuppressWarnings("unchecked")
	private boolean isRepeatSubmit(HttpServletRequest request, RepeatSubmit annotation) {
		// 本次请求参数及请求时间
		LinkedHashMap<String, Object> newData = CollectionUtils.newLinkedHashMap(2);
		newData.put(REPEAT_SUBMIT_PARAMS, JSON.toJSONString(request.getParameterMap()));
		newData.put(REPEAT_SUBMIT_REQUEST_TIME, System.currentTimeMillis());

		HttpSession session = request.getSession();
		Object sessionData = session.getAttribute(REPEAT_SUBMIT_SESSION_KEY);
		if (Objects.isNull(sessionData)) {
			// Session中没有（重复提交）相关的数据
			LinkedHashMap<String, Object> initSessionMap = CollectionUtils.newLinkedHashMap(16);
			initSessionMap.put(request.getRequestURI(), newData);
			session.setAttribute(REPEAT_SUBMIT_SESSION_KEY, initSessionMap);
			return false;
		}

		// Session 中有 “防重复提交” 数据
		LinkedHashMap<String, Object> sessionMap = (LinkedHashMap<String, Object>) sessionData;
		// 请求地址（作为存放 Session 的key值）
		if (sessionMap.containsKey(request.getRequestURI())) {
			LinkedHashMap<String, Object> oldData = (LinkedHashMap<String, Object>) sessionMap.get(request.getRequestURI());
			// 请求参数是否相等 并且 有效时间内
			if (compareParams(newData, oldData) && compareTime(newData, oldData, annotation)) {
				return true;
			}
		}

		// 如果不包含当前 url 的数据，添加到 sessionMap 中
		sessionMap.put(request.getRequestURI(), newData);
		return false;
	}

	private boolean compareParams(LinkedHashMap<String, Object> newData, LinkedHashMap<String, Object> oldData) {
		return Objects.equals(newData.get(REPEAT_SUBMIT_PARAMS), oldData.get(REPEAT_SUBMIT_PARAMS));
	}

	private boolean compareTime(LinkedHashMap<String, Object> newData, LinkedHashMap<String, Object> oldData, RepeatSubmit annotation) {
		Long newRequestTime = (Long) newData.get(REPEAT_SUBMIT_REQUEST_TIME);
		Long oldRequestTime = (Long) oldData.get(REPEAT_SUBMIT_REQUEST_TIME);
		if ((newRequestTime - oldRequestTime) < annotation.timeMillis()) {
			return true;
		}
		return false;
	}
}
```

# WebMvcConfigurer注册拦截器

```java 
package com.alibaba.frame.web.config;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;
import com.alibaba.frame.web.interceptor.RepeatSubmitInterceptor;

/**
 * @description: 注册拦截器
 *
 * @date: 2023-07-24
 */
@Configuration
public class InterceptorWebConfig implements WebMvcConfigurer {
	@Autowired
	private RepeatSubmitInterceptor repeatSubmitInterceptor;

	@Override
	public void addInterceptors(InterceptorRegistry registry) {
		// 注册拦截器
		registry.addInterceptor(repeatSubmitInterceptor).addPathPatterns("/user/*");
	}
}
```

# Controller控制层

```java 
package com.alibaba.frame.web.controller;
import com.alibaba.frame.web.annotation.RepeatSubmit;
import com.alibaba.frame.web.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping(value = "/user")
public class UserController {
	@Autowired
	private UserService userService;

	@GetMapping("/getUserInfo")
	@RepeatSubmit
	public ResponseEntity<String> getUserInfo() {
		return new ResponseEntity<>(userService.getUserInfo(), HttpStatus.OK);
	}

	@GetMapping("/insertUserInfo")
	@RepeatSubmit
	public ResponseEntity<Integer> insertUserInfo() {
		return new ResponseEntity<>(userService.insertUserInfo(null), HttpStatus.OK);
	}
}
```
