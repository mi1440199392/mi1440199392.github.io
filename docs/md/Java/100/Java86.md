# 前言

<font face="幼圆">

> `MessageFormatter`字符串格式化，来自依赖org.slf4j:slf4j-api

</font>

```java 
package com.hundsun.hswealth.csm;
import org.slf4j.helpers.FormattingTuple;
import org.slf4j.helpers.MessageFormatter;

public class Main {
    public static void main(String[] args) {
        
        // 字符串格式化 = String.format()
        String formatted = MessageFormatter.format("打印日志 = {}", "hello world").getMessage();
        System.out.println("formatted = " + formatted);

        // 数组格式化
        String[] arrayString = {"aaa", "bbb", "ccc"};
        FormattingTuple arrayFormat = MessageFormatter.arrayFormat("{}{}{}", arrayString);
        String formatMessage = arrayFormat.getMessage();
        System.out.println("formatMessage = " + formatMessage);
    }
}
```

```shell
formatted = 打印日志 = hello world
formatMessage = aaabbbccc
```
