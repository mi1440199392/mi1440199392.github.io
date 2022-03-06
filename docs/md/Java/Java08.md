```xml

<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.hundsun</groupId>
    <artifactId>app</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
    </properties>

</project>

```

```java

package com.hundsun.domain;


import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target(ElementType.TYPE)
@Retention(RetentionPolicy.SOURCE)
public @interface Primary {
 // 标记注解，用于标记
}

```

```java

package com.hundsun.domain;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target(ElementType.TYPE)
@Retention(RetentionPolicy.SOURCE)
public @interface Two {
    // 标记注解，用于标记
}

```

```java

package com.hundsun.domain;


@Two
public enum Person {
    ZHANG_SAN,
    LI_SI,
    WANG_WU,
    ZHAO_LIU,
    TIAN_QI
}

```

```java

package com.hundsun.domain;


@Primary
public enum User {

    ZHANG_SAN("ZHANG_SAN", "ZHANG_SAN"), //
    LI_SI("LI_SI", "LI_SI"), //
    WANG_WU("WANG_WU", "WANG_WU"), //
    ZHAO_LIU("ZHAO_LIU", "ZHAO_LIU"), //
    TIAN_QI("TIAN_QI", "TIAN_QI");

    private final String userName;
    private final String password;

    User(String userName, String password) {
        this.userName = userName;
        this.password = password;
    }

    public String getUserName() {
        return userName;
    }

    public String getPassword() {
        return password;
    }
}

```

```java

package com.hundsun.controller;

import com.hundsun.domain.Person;
import com.hundsun.domain.User;

public class MainApp {
    public static void main(String[] args) {
        show01();
        show02();
    }

    static void show01() {
        Person person = Person.LI_SI;
        switch (person) {
            case ZHANG_SAN:
                throw new RuntimeException("ZHANG_SAN");
            case LI_SI:
                throw new RuntimeException("LI_SI");
            case WANG_WU:
                throw new RuntimeException("WANG_WU");
            case ZHAO_LIU:
                throw new RuntimeException("ZHAO_LIU");
            case TIAN_QI:
                throw new RuntimeException("TIAN_QI");
            default:
                throw new RuntimeException("default");
        }
    }

    static void show02() {
        final String userName = "ZHAO_LIU";

        if (userName.equals(User.ZHANG_SAN.getUserName())) {
            throw new RuntimeException("ZHANG_SAN");
        } else if (userName.equals(User.LI_SI.getUserName())) {
            throw new RuntimeException("LI_SI");
        } else if (userName.equals(User.WANG_WU.getUserName())) {
            throw new RuntimeException("WANG_WU");
        } else if (userName.equals(User.ZHAO_LIU.getUserName())) {
            throw new RuntimeException("ZHAO_LIU");
        } else if (userName.equals(User.TIAN_QI.getUserName())) {
            throw new RuntimeException("TIAN_QI");
        } else {
            throw new RuntimeException("default");
        }
    }
}

```
