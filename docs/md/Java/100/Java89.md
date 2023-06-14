# 前言

<font face="幼圆">

> Character 判断字符是否为空格

</font>

# Character 判断字符是否为空格

```java 
package com.hundsun.hswealth.csm;

public class Main {
    public static void main(String[] args) {

        char character = '1';
        System.out.println("character = " + Character.isWhitespace(character));

        char characterWhitespace = ' ';
        System.out.println("characterWhitespace = " + Character.isWhitespace(characterWhitespace));
    }
}
```

---

```text
character = false
characterWhitespace = true
```

# 常见应用：StringUtils.isBlank()

<font face="幼圆">

> org.apache.commons.lang3.StringUtils.isBlank()
> 
> 

</font>

```java
public static boolean isBlank(final CharSequence cs) {
    int strLen;
    if (cs == null || (strLen = cs.length()) == 0) {
        return true;
    }
    for (int i = 0; i < strLen; i++) {
        // 判断字符是否为空格
        if (!Character.isWhitespace(cs.charAt(i))) {
            return false;
        }
    }
    return true;
}
```
