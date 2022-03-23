```java

package com.hundsun.demo.proxy.aop;

/**
 * 增强类
 */
public class Transactional {

    public void enableTransactional() {
        System.out.println("开启事务\n");
    }

    public void commit() {
        System.out.println("提交事务\n");
    }

    public void cancle() {
        System.out.println("回滚事务\n");
    }
}

```

```java

package com.hundsun.demo.proxy.service;
import com.hundsun.demo.domain.User;

/**
 * 目标接口
 */
public interface UserService {

    void addUser(User user);

    void updateUser(User user);

    void deleteUser(int id);

    User getUser(int id);
}

```

```java

package com.hundsun.demo.proxy.service.impl;
import com.hundsun.demo.domain.User;
import com.hundsun.demo.proxy.service.UserService;

/**
 * 目标类
 */
public class UserServiceImpl implements UserService {

    @Override
    public void addUser(User user) {
        System.out.println("添加客户");
    }

    @Override
    public void updateUser(User user) {
        System.out.println("修改客户");
    }

    @Override
    public void deleteUser(int id) {
        System.out.println("删除客户");
    }

    @Override
    public User getUser(int id) {
        System.out.println("查询客户");
        return new User();
    }
}

```

```java

package com.hundsun.demo.proxy.proxy;
import com.hundsun.demo.domain.User;
import com.hundsun.demo.proxy.aop.Transactional;
import com.hundsun.demo.proxy.service.UserService;

/**
 * 代理类
 */
public class ProxyUserService implements UserService {

    private Transactional aop;
    private UserService target;

    public ProxyUserService(Transactional aop, UserService target) {
        this.aop = aop;
        this.target = target;
    }

    @Override
    public void addUser(User user) {
        try {
            aop.enableTransactional(); // 开启事务
            target.addUser(user);
            aop.commit(); // 提交事务
        } catch (Exception e) {
            aop.cancle(); // 回滚事务
        }

    }

    @Override
    public void updateUser(User user) {
        try {
            aop.enableTransactional(); // 开启事务
            target.updateUser(user);
            aop.commit(); // 提交事务
        } catch (Exception e) {
            aop.cancle(); // 回滚事务
        }
    }

    @Override
    public void deleteUser(int id) {
        try {
            aop.enableTransactional(); // 开启事务
            target.deleteUser(id);
            aop.commit(); // 提交事务
        } catch (Exception e) {
            aop.cancle(); // 回滚事务
        }
    }

    @Override
    public User getUser(int id) {
        return null;
    }
}

```

```java

/**
    * 测试静态代理
    */
@Test
void contextLoads11() {

    Transactional aop = new Transactional();
    UserService target = new UserServiceImpl();
    ProxyUserService proxyUserService = new ProxyUserService(aop, target);

    proxyUserService.addUser(new User());
    proxyUserService.updateUser(new User());
    proxyUserService.deleteUser(10);
}

```

> 控制台打印

```xml

开启事务
添加客户
提交事务

开启事务
修改客户
提交事务

开启事务
删除客户
提交事务

```
