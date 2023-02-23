```xml

<!--汉语转拼音-->
<dependency>
    <groupId>com.belerweb</groupId>
    <artifactId>pinyin4j</artifactId>
    <version>2.5.1</version>
</dependency>

```

```java

package com.hundsun.customerGroup.util;

import net.sourceforge.pinyin4j.PinyinHelper;
import net.sourceforge.pinyin4j.format.HanyuPinyinCaseType;
import net.sourceforge.pinyin4j.format.HanyuPinyinOutputFormat;
import net.sourceforge.pinyin4j.format.HanyuPinyinToneType;
import net.sourceforge.pinyin4j.format.HanyuPinyinVCharType;
import net.sourceforge.pinyin4j.format.exception.BadHanyuPinyinOutputFormatCombination;

public class Pinyin4jUtil {
    public static void main(String[] args) throws BadHanyuPinyinOutputFormatCombination {

        String resource = "吕布java";
        HanyuPinyinOutputFormat format = new HanyuPinyinOutputFormat();
        format.setCaseType(HanyuPinyinCaseType.UPPERCASE); // 转换成大写
        format.setToneType(HanyuPinyinToneType.WITHOUT_TONE); // 是否忽略拼音的音标
        format.setVCharType(HanyuPinyinVCharType.WITH_V); // 设置拼音u转换为v

        /**
         * @param1 需要转换的字符串
         * @param2 格式设置器
         * @param3 用指定字符串拼接
         * @param4 转换后是否显示不是汉字的字符
         * @param1
         */
        String pinyinString = PinyinHelper.toHanYuPinyinString(resource, format, "-", true);
        System.out.println("pinyinString = " + pinyinString);
    }
}

```