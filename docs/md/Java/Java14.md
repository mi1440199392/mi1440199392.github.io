```xml

<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-collections4</artifactId>
    <version>4.3</version>
</dependency>

```

```java

package com.hundsun.customerGroup;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.function.Function;
import java.util.stream.Collectors;

import org.apache.commons.collections4.MapUtils;
import org.junit.jupiter.api.Test;
import org.springframework.boot.test.context.SpringBootTest;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import lombok.ToString;

@SpringBootTest
class CustomerGroupApplicationTests {

    @Data
    @NoArgsConstructor
    @AllArgsConstructor
    @ToString
    static
    class User{
        private Integer uid;
        private String uName;
    }

    @Test
    void contextLoads() {
    
        // 将权限列表以id为key，以权限对象为值转换成map
        Map<Long, UmsPermission> permissionMap = permissionList.stream()
            .collect(Collectors.toMap(permission -> permission.getId(), permission -> permission));
        
        Map<String, String> map = new HashMap<>();
        List<String> list = new ArrayList<>();
        list.add("10001");
        list.add("10002");
        list.add("10003");
        list.add("10004");
        list.add("10005");
        MapUtils.populateMap(map,list,String::toString);
        System.out.println(map);

        // key是对象中的某个属性值，value是对象本身（使用Function.identity()的简洁写法）
        Map<Integer, String> map1 = list.stream().collect(Collectors.toMap(String::hashCode, Function.identity()));
        Map<Integer, String> map2 = list.stream().collect(Collectors.toMap(String::hashCode, String::toString));
        System.out.println(map1);
        System.out.println(map2);

        Map<Integer, User> map3 = new HashMap<>();
        List<User> users = new ArrayList<>();
        users.add(new User(10001, "张三"));
        users.add(new User(10002, "李四"));
        users.add(new User(10003, "王五"));
        users.add(new User(10004, "赵六"));
        MapUtils.populateMap(map3,users,User::getUid);
        System.out.println(map3);
    }
}

```

```java

-- ---------------------------------------------------------------控制台打印---------------------------------------------------------------------------
{10002=10002, 10001=10001, 10004=10004, 10003=10003, 10005=10005}
{46730163=10002, 46730162=10001, 46730165=10004, 46730164=10003, 46730166=10005}
{46730163=10002, 46730162=10001, 46730165=10004, 46730164=10003, 46730166=10005}
{10001=CustomerGroupApplicationTests.User(uid=10001, uName=张三), 10002=CustomerGroupApplicationTests.User(uid=10002, uName=李四), 10003=CustomerGroupApplicationTests.User(uid=10003, uName=王五), 10004=CustomerGroupApplicationTests.User(uid=10004, uName=赵六)}

```