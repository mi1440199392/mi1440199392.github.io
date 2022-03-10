###### *Lambda*表达式概念

*任何接口，如果只包含唯一一个抽象方法，那么它就是一个函数式接口*

###### *Lambda*表达入门

```java

package com.hundsun.demo.service;

/**
 * 定义一个接口，有且仅有一个抽象方法，称为函数式接口
 */
public interface UserService {

    boolean addUser();
}

```

```java

package com.hundsun.demo.filter;
import com.hundsun.demo.service.UserService;

public class FilterUser {

    /**
     * 定义一个方法，入参是函数式接口
     * 
     * @param userService
     * @return
     */
    public boolean filterUser(UserService userService) {
        return true;
    }
}


```

```java

package com.hundsun.demo.domain;
import com.hundsun.demo.filter.FilterUser;
import com.hundsun.demo.service.UserService;

public class MainApp {

    public static void main(String[] args) {
        FilterUser user = new FilterUser();

        // 匿名内部类
        boolean b = user.filterUser(new UserService() {
            @Override
            public boolean addUser() {
                return false;
            }
        });

        // Lambda表达式
        boolean b1 = user.filterUser(() -> false);
    }
}

```