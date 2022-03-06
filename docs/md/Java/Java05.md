```java

-- application.properties配置信息映射

com.hundsun.domain.user.id=1001
com.hundsun.domain.user.name=张三
com.hundsun.domain.user.age=30

```

```java

package com.hundsun.customerGroup.domain;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import lombok.ToString;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

@Data
@NoArgsConstructor
@AllArgsConstructor
@ToString
@Component
@ConfigurationProperties(prefix = "com.hundsun.domain.user")
public class User<E, K, V> {

    private E id;
    private K name;
    private V age;
}

```

```java

package com.hundsun.customerGroup;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import com.hundsun.customerGroup.domain.User;

@SpringBootTest
class CustomerGroupApplicationTests {

    @Autowired
    private User user;

    @Test
    void contextLoads() {
        System.out.println(user.toString());
    }
}

```

```java

--------------------------------------------------------------控制台-------------------------------------------------------------------------
User{id=1001, name=张三, age=30}

```