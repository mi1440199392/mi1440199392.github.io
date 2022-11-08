### *经常遇到的事务失效情况*

<details>
<summary>经常遇到的事务失效情况（点击展开）</summary>

- 加 *@Transaction* 批注的方法必须是 *public*，否则失效 *protected* 也不成
- 如使用 *mysql* 且引擎是 *MyISAM*，则事务会不起作用，原因是* MyISAM* 不支持事务，可以改成 *InnoDB* 引擎
- 没有被 *Spring* 容器管理到，最常见的是没有在服务类上加 *@Service* 注解
- 异常被捕获，没有抛出来
- 异常不在 *spring* 默认捕获异常中，*spring*　默认捕获不受控异常
- 在同一个类中方法间调用方式不恰当，造成事务失效

</details>

### *同类中方法间调用方式*
#### *在同一个类中方法间调用方式不恰当，造成事务失效*

这点比较有意思，这里特别说明下，这里先举个例子说明这种情况：
```java
// 保存父方法
public void saveParentMethod() {
    // 插入 parent
    StockInfo stockInfo = new StockInfo();
    stockInfo.setProductId("parent");
    this.stockInfoMapper.insertSelective(stockInfo);
    // 插入 child
    this.saveChildStockInfo();

}

// 保存子方法
@Transactional(rollbackFor = Exception.class)
public void saveChildStockInfo() {
    StockInfo stockInfo = new StockInfo();
    stockInfo.setProductId("child");
    this.stockInfoMapper.insertSelective(stockInfo);
    // 制造异常
    int i = 1 / 0;
}
```
这里首先可以看到 *saveParentMethod* 方法是没有事务的，而 *saveChildStockInfo* 却是有事务的；
当测试类调用 *saveParentMethod* 方法后，你会发现事务完全不起作用了。可能在我们的理解中：*parent* 应该入库而 *child* 不应该入库；
然而实际情况是：*child* 也入库了，明显是事务失效了。

#### *原因*

*spring* 基于 *AOP* 机制实现事务的管理；*spring* 在扫描 *bean* 的时候会扫描方法上是否包含 *@Transactional* 注解，
若是包含，*spring* 会为这个 *bean* 动态地生成一个子类（即代理类，proxy），代理类是继承原来那个 *bean* 的。
此时，当这个有注解的方法被调用的时候，其实是由代理类来调用的，代理类在调用以前就会启动 *transaction*。然而，
若是这个有注解的方法是被同一个类中的其余方法调用的，那么该方法的调用并无经过代理类，而是直接经过原来的那个 *bean* ，因此就不会启动 *transaction*，
最后看到的现象就是 *@Transactional* 注解无效。

### *解决同类中方法间调用事务不起作用的方式*
#### *两个方法都有事务*

就拿开始的例子如果 *saveParentMethod* 上有 *@Transactional* 注解，自然就不会出现不起作用的情况了

#### *把两个方法分在不同的 service 中*

这个方法虽然也有点看着愚蠢，但是的确很多情况下很实用；尤其是如果你的 *service* 叫 *XXService*，那么分出的有
事务的方法可以放在 *XXServiceHelper* 类中。既能解决事务不起作用的问题，同样可以使你的主 *Service* 变的很清爽。所以这个方法，在某些情况下反而非常适合

#### *本类中注入自己*

```java
@Service
public class OuterBean {

    @Resource
    private StockInfoMapper stockInfoMapper;
    //自己注入自己
    @Resource
    private OuterBean outerBean;

    public void saveParentMethod() {
        // 插入 parent
        StockInfo stockInfo = new StockInfo();
        stockInfo.setProductId("parent");
        this.stockInfoMapper.insertSelective(stockInfo);
        //插入 child，这里相当于调用代理的方法
        this.outerBean.saveChildStockInfo();
    }

    @Transactional(rollbackFor = Exception.class)
    public void saveChildStockInfo() {
        StockInfo stockInfo = new StockInfo();
        stockInfo.setProductId("child");
        this.stockInfoMapper.insertSelective(stockInfo);
        // 制造异常
        int i = 1 / 0;
    }
}
```

可能有人会担心这样会有循环依赖的问题，事实上，*spring* 通过三级缓存解决了循环依赖的问题，所以上面的写法不会有循环依赖问题。
但是！！！，这不代表 *spring* 永远没有循环依赖的问题（*@Async* 导致循环依赖了解下）

#### *通过 ApplicationContext 获得代理类*

既然我们知道 *@Transactional* 是通过 *AOP* 来实现的，这里就很容易想到：只要拿到代理我们 *Service* 的那个对象就可以了，于是就有了如下代码：

```java
@Service
public class OuterBean {

    @Resource
    private StockInfoMapper stockInfoMapper;
    @Autowired
    private ApplicationContext applicationContext;

    public void saveParentMethod() {
        // 插入 parent
        StockInfo stockInfo = new StockInfo();
        stockInfo.setProductId("parent");
        this.stockInfoMapper.insertSelective(stockInfo);
        //通过 ApplicationContext 获取代理对象
        this.applicationContext.getBean(OuterBean.class).saveChildStockInfo();
    }

    @Transactional(rollbackFor = Exception.class)
    public int saveChildStockInfo() {
        StockInfo stockInfo = new StockInfo();
        stockInfo.setProductId("child");
        int insert = this.stockInfoMapper.insertSelective(stockInfo);
        // 制造异常
        int i = 1 / 0;
        return insert;
    }
} 
```

#### *通过 AopContext 获取代理类*

和上面的方式原理差不多，只是获得代理类的方式不同，代码如下：

```java 
// 这里多加一个批注，不加会报错:Set 'exposeProxy' property on Advised to 'true' to make it available
@EnableAspectJAutoProxy(proxyTargetClass=true, exposeProxy=true)
@Service
public class OuterBean {

    @Resource
    private StockInfoMapper stockInfoMapper;

    public void saveParentMethod() {
        // 插入 parent
        StockInfo stockInfo = new StockInfo();
        stockInfo.setProductId("parent");
        this.stockInfoMapper.insertSelective(stockInfo);
        // 通过 AopContext 调用 saveChildStockInfo 方法
        ((OuterBean) AopContext.currentProxy()).saveParentMethod();
    }

    @Transactional(rollbackFor = Exception.class)
    public void saveChildStockInfo() {
        StockInfo stockInfo = new StockInfo();
        stockInfo.setProductId("child");
        this.stockInfoMapper.insertSelective(stockInfo);
        // 制造异常
        int i = 1 / 0;
    }
}
```

> 此方法还是非常推荐的。使用简单而且不会有循环依赖的问题，非常的nice

#### *利用 JDK8 新特性，写一个事务的 Handler*

```java 
// 新加一个 TransactionHandler 类
@Service
public class TransactionHandler {
    @Transactional(rollbackFor = Exception.class, propagation = Propagation.REQUIRED)
    public <T> T runInTransaction(Supplier<T> supplier) {
        return supplier.get();
    }

    @Transactional(rollbackFor = Exception.class, propagation = Propagation.REQUIRES_NEW)
    public <T> T runInNewTransaction(Supplier<T> supplier) {
        return supplier.get();
    }
}

// 改造 OuterBean
@Service
public class OuterBean {

    @Resource
    private StockInfoMapper stockInfoMapper;
    @Autowired
    private TransactionHandler transactionHandler;

    public void saveParentMethod() {
        // 插入 parent
        StockInfo stockInfo = new StockInfo();
        stockInfo.setProductId("parent");
        this.stockInfoMapper.insertSelective(stockInfo);
        //通过TransactionHandler调用saveChildStockInfo方法
        this.transactionHandler.runInTransaction(() -> saveChildStockInfo());
    }

    public int saveChildStockInfo() {
        StockInfo stockInfo = new StockInfo();
        stockInfo.setProductId("child");
        int insert = this.stockInfoMapper.insertSelective(stockInfo);
        // 制造异常
        int i = 1 / 0;
        return insert;
    }
}
```

这个方法看着有点麻烦，但是有几个优势是其他方式无法比拟的：
* 它可以应用于私有方法，因此，您不必为了满足 *Spring* 的限制而通过公开方法来破坏封装
* 可以在不同的事务传播中调用相同的方法，并且由调用方选择合适的方法，比较以下两行：

```java 
-- 你可以指定传播性
this.transactionHandler.runInTransaction(() -> saveChildStockInfo());
this.transactionHandler.runInNewTransaction(() -> saveChildStockInfo());
```
* 它是显式调用的，因此更具可读性

### *补充*

*Spring* 的 *AOP* 代理有 *jdk* 代理和 *cglib* 代理实现，通过如下代码来区分：

```java 
-- 是否代理对象
AopUtils.isAopProxy(AopContext.currentProxy());

-- 是否cglib 代理对象
AopUtils.isCglibProxy(AopContext.currentProxy());

-- 是否dk动态代理
AopUtils.isJdkDynamicProxy(AopContext.currentProxy());
```




