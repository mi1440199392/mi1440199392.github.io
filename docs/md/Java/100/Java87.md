# 前言

<font face="幼圆">

> 模拟 Servlet Filter 过滤器

</font>

# Filter过滤器

```java 
// 过滤器
interface Filter {

    void doFilter(HttpRequestEntity entity, FilterChain filterChain) throws Exception;

}
```

# 过滤器：参数校验Filter

```java 
// 过滤器：参数校验
static class HttpParameterFilter implements Filter {

    @Override
    public void doFilter(HttpRequestEntity entity, FilterChain filterChain) throws Exception {
        // 处理自己的业务逻辑
        System.out.println("参数校验处理 entity 消息 " + entity);

        // 继续走责任链的下一个filter
        filterChain.doFilter(entity);
    }

    public static Filter getInstance() {
        return new HttpParameterFilter();
    }
}
```

# 过滤器：认证校验Filter

```java 
// 过滤器：认证校验
static class HttpAuthFilter implements Filter {

    @Override
    public void doFilter(HttpRequestEntity entity, FilterChain filterChain) throws Exception {
        // 处理自己的业务逻辑
        System.out.println("认证校验处理 entity 消息 " + entity);

        // 继续走责任链的下一个filter
        filterChain.doFilter(entity);
    }

    public static Filter getInstance() {
        return new HttpAuthFilter();
    }
}
```

# 责任链 [Filter的上下文环境]

```java 
// 责任链 [Filter的上下文环境]
interface FilterChain {

    void doFilter(HttpRequestEntity entity) throws Exception;
}
```

# 责任链实现 [Filter的上下文环境]，也可以理解为包装器

```java 
// 责任链实现 [Filter的上下文环境]，也可以理解为包装器
static class FilterChainWrapper implements FilterChain {
    // 所有 Filter 组件
    private final List<Filter> filters;
    // 记录当前执行到了第几个 Filter 的坐标
    private static final ThreadLocal<Integer> positionLocal = ThreadLocal.withInitial(() -> 0);

    @Override
    public void doFilter(HttpRequestEntity entity) throws Exception {
        // 获取当前 FilterChain 执行的 Filter 坐标
        Integer position = positionLocal.get();

        if (position < filters.size()) {
            position++;
            positionLocal.set(position);
            Filter filter = filters.get(position - 1);
            filter.doFilter(entity, this);
        }
    }

    // 供外部调用的主方法
    public void executor(HttpRequestEntity entity) throws Exception {
        this.doFilter(entity);
    }

    // 责任链执行完，清除ThreadLocal，避免内存泄露
    public void reset() {
        positionLocal.remove();
        positionLocal.set(0);
    }


    public static FilterChainWrapper getInstance(List<Filter> filters) {
        return new FilterChainWrapper(filters);
    }

    private FilterChainWrapper(List<Filter> filters) {
        this.filters = filters;
    }
}
```

# 责任链管理器

```java 
static class FilterChainManager {
    private static FilterChainWrapper filterChain;


    public static void init() {
        // 初始化 Filter 组件
        List<Filter> filters = new ArrayList<>(10);
        filters.add(HttpParameterFilter.getInstance());
        filters.add(HttpAuthFilter.getInstance());

        // Filter 组件，注册到 FilterChain
        FilterChainManager.filterChain = FilterChainWrapper.getInstance(filters);
    }

    public static void executor(HttpRequestEntity entity) {
        try {
            filterChain.executor(entity);
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // 责任链执行完，清除坐标信息，重置为0
            filterChain.reset();
        }
    }
}
```

# 业务模型

```java 
// 业务模型
static class HttpRequestEntity {
    private int id;
    private String name;

    private HttpRequestEntity(int id, String name) {
        this.id = id;
        this.name = name;
    }

    public static HttpRequestEntity getInstance(int id, String name) {
        return new HttpRequestEntity(id, name);
    }

    @Override
    public String toString() {
        return "[" + "id:" + id + ", name:" + name + ']';
    }
}
```
# 控制台输出

```text
HttpParameterFilter 处理 entity 消息 [id:1001, name:张三]
HttpAuthFilter 处理 entity 消息 [id:1001, name:张三]
HttpParameterFilter 处理 entity 消息 [id:1002, name:李四]
HttpAuthFilter 处理 entity 消息 [id:1002, name:李四]
```


