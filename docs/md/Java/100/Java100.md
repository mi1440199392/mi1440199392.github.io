# 前言

<font face="幼圆">

> SpringMVC 组件执行链路

</font>

# Controller执行链路

```java 
Servlet.service(ServletRequest req, ServletResponse res)
	|
HttpServlet.service(HttpServletRequest req, HttpServletResponse resp)
	|
FrameworkServlet.service(HttpServletRequest request, HttpServletResponse response)
	|
HttpServlet.service(HttpServletRequest req, HttpServletResponse resp)
	|
FrameworkServlet.doGet(HttpServletRequest request, HttpServletResponse response)
	|
FrameworkServlet.processRequest(HttpServletRequest request, HttpServletResponse response)
	|
DispatcherServlet.doService(HttpServletRequest request, HttpServletResponse response)
	|
DispatcherServlet.doDispatch(HttpServletRequest request, HttpServletResponse response)
	|
AbstractHandlerMethodAdapter.handle(HttpServletRequest request, HttpServletResponse response, Object handler)
	|
RequestMappingHandlerAdapter.handleInternal(HttpServletRequest request,HttpServletResponse response, HandlerMethod handlerMethod)
	|
RequestMappingHandlerAdapter.invokeHandlerMethod(HttpServletRequest request,HttpServletResponse response, HandlerMethod handlerMethod)
	|
ServletInvocableHandlerMethod.invokeAndHandle(ServletWebRequest webRequest, ModelAndViewContainer mavContainer, Object... providedArgs) 
	|
InvocableHandlerMethod.invokeForRequest(NativeWebRequest request, @Nullable ModelAndViewContainer mavContainer, Object... providedArgs)
	|
InvocableHandlerMethod.doInvoke(Object... args)
	|
Method.invoke(getBean(), args) // 执行目标方法
```

# HandlerInterceptor注册链路

```java 
HttpServlet.service(ServletRequest req, ServletResponse res)
	|
FrameworkServlet.service(HttpServletRequest request, HttpServletResponse response)
	|
HttpServlet.service(ServletRequest req, ServletResponse res)
	|
FrameworkServlet.doGet(HttpServletRequest request, HttpServletResponse response)
	|
FrameworkServlet.processRequest(HttpServletRequest request, HttpServletResponse response)
	|
DispatcherServlet.doService(HttpServletRequest request, HttpServletResponse response)
	|
DispatcherServlet.doDispatch(HttpServletRequest request, HttpServletResponse response)
	|
DispatcherServlet.getHandler(HttpServletRequest request)
	|
AbstractHandlerMapping.getHandler(HttpServletRequest request)
	|
AbstractHandlerMapping.getHandlerExecutionChain(Object handler, HttpServletRequest request)
	|
new HandlerExecutionChain(handler) // 此时这里并没有注册拦截器
	|
AbstractHandlerMapping.List<HandlerInterceptor> adaptedInterceptors // 适配拦截器里取到拦截器
	|
拦截器MappedInterceptor，封装了自定义的拦截器，MappedInterceptor.HandlerInterceptor interceptor
	|
AbstractHandlerMapping 实现了 ApplicationContextAware容器
	|
AbstractHandlerMapping.initApplicationContext()
	|
AbstractHandlerMapping.initInterceptors()
```

# HandlerInterceptor注册时机，容器启动时注册

```java 
WebMvcAutoConfiguration.welcomePageHandlerMapping(ApplicationContext applicationContext, FormattingConversionService mvcConversionService, ResourceUrlProvider mvcResourceUrlProvider)
	|
WebMvcAutoConfiguration.createWelcomePageHandlerMapping(ApplicationContext applicationContext, FormattingConversionService mvcConversionService,ResourceUrlProvider mvcResourceUrlProvider, WelcomePageHandlerMappingFactory<T> factory)
	|
WebMvcConfigurationSupport.getInterceptors(FormattingConversionService mvcConversionService, ResourceUrlProvider mvcResourceUrlProvider)
	|
DelegatingWebMvcConfiguration.addInterceptors(InterceptorRegistry registry)
	|
WebMvcConfigurerComposite.addInterceptors(InterceptorRegistry registry)
	|
WebMvcConfigurerComposite.List<WebMvcConfigurer> delegates
	|
(自定义的拦截器配置类)InterceptorWebConfig.addInterceptors(InterceptorRegistry registry)
	|
AbstractHandlerMapping.setInterceptors
```

# (自定义的拦截器配置类)InterceptorWebConfig，注册逻辑

```java 
@Configuration
DelegatingWebMvcConfiguration.WebMvcConfigurerComposite configurers
	|
@Autowired
DelegatingWebMvcConfiguration.addWebMvcConfigurers(List<WebMvcConfigurer> configurers)
```
