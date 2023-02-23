```java
package com.hundsun.demo.list.e;
import org.apache.commons.lang3.StringUtils;
import java.util.function.Predicate;

/**
 * @Desc defaultIfNull满足条件返回默认值
 * @Date 2022/9/26
 * @Created root
 */
public class E_104 {
    public static void main(String[] args) throws Exception {

        String str = "";
        String defaultValue = "1";

        String result = defaultIfTrue(s -> StringUtils.isEmpty(str), str, defaultValue);
        System.out.println("result = " + result);
    }

    public static <T> T defaultIfNull(final T object, final T defaultValue) {
        return object != null ? object : defaultValue;
    }

    public static <T> T defaultIfTrue(Predicate<T> predicate, T object, T defaultValue) {
        return !predicate.test(object) ? object : defaultValue;
    }
}
```

> 控制台打印

```java
result = 1
```