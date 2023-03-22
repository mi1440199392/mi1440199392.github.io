# 前言

<font face="幼圆">

> 对象`copy`工具类`ObjectUtil`

</font>

# 对象copy工具类ObjectUtil

```java
package com.hundsun.dto;
import java.beans.PropertyDescriptor;
import java.util.HashSet;
import java.util.List;
import java.util.Objects;
import java.util.Set;
import org.springframework.beans.BeanUtils;
import org.springframework.beans.BeanWrapper;
import org.springframework.beans.BeanWrapperImpl;
import org.springframework.util.StringUtils;

/**
 * 对象copy工具类ObjectUtil
 * 
 * @author hspcadmin
 * @version 1.0
 * @description
 * @date
 */
public class ObjectUtil {

    /**
     * copy集合
     * 
     * @param targetList 目标集合
     * @param srcList 源集合
     * @param tClass 目标类对象
     * @param <T> 泛型
     */
    public static <T> void copyList(List<T> targetList, List<?> srcList, Class<T> tClass) {
        if (srcList != null && srcList.size() != 0) {
            if (targetList != null) {
                for (int i = 0; i < srcList.size(); ++i) {
                    T instance = null;

                    try {
                        instance = tClass.newInstance();
                    } catch (InstantiationException | IllegalAccessException var6) {
                        var6.printStackTrace();
                    }

                    targetList.add(instance);
                    BeanUtils.copyProperties(srcList.get(i), instance, new String[0]);
                }
            }
        }
    }

    /**
     * copy集合 (过滤掉空值、空串属性)
     *
     * @param targetList 目标集合
     * @param srcList 源集合
     * @param tClass 目标类对象
     * @param <T> 泛型
     */
    public static <T> void copyListIgnoreNullObj(List<T> targetList, List<?> srcList, Class<T> tClass) {
        if (srcList != null && srcList.size() != 0) {
            if (targetList != null) {
                for (int i = 0; i < srcList.size(); ++i) {
                    T instance = null;

                    try {
                        instance = tClass.newInstance();
                    } catch (InstantiationException | IllegalAccessException var6) {
                        var6.printStackTrace();
                    }

                    targetList.add(instance);
                    copyIgnoreNullObj(instance, srcList.get(i));
                }
            }
        }
    }

    /**
     * copy对象 (过滤掉空值属性)
     *
     * @param target 目标对象
     * @param src 源对象
     */
    public static void copyIgnoreNull(Object target, Object src) {
        BeanUtils.copyProperties(src, target, getNullPropertyNames(src));
    }

    /**
     * copy对象 (过滤掉空值、空串属性)
     *
     * @param target 目标对象
     * @param src 源对象
     */
    public static void copyIgnoreNullObj(Object target, Object src) {
        BeanUtils.copyProperties(src, target, getNullObjPropertyNames(src));
    }

    /**
     * 获取对象所有的属性 (空值的属性)
     *
     * @param source 源对象
     * @return
     */
    public static String[] getNullPropertyNames(Object source) {
        BeanWrapper src = new BeanWrapperImpl(source);
        PropertyDescriptor[] properties = src.getPropertyDescriptors();
        Set<String> emptyNames = new HashSet<>();
        for (PropertyDescriptor property : properties) {
            Object srcValue = src.getPropertyValue(property.getName());
            if (!Objects.nonNull(srcValue)) {
                emptyNames.add(property.getName());
            }
        }
        return emptyNames.toArray(new String[0]);
    }

    /**
     * 获取对象所有的属性 (空值、空串的属性)
     *
     * @param source 源对象
     * @return
     */
    public static String[] getNullObjPropertyNames(Object source) {
        BeanWrapper src = new BeanWrapperImpl(source);
        PropertyDescriptor[] properties = src.getPropertyDescriptors();
        Set<String> emptyNames = new HashSet<>();
        for (PropertyDescriptor property : properties) {
            Object srcValue = src.getPropertyValue(property.getName());
            if (!checkNotNullObj(srcValue)) {
                emptyNames.add(property.getName());
            }
        }
        return emptyNames.toArray(new String[0]);
    }

    /**
     * 判断对象是否为空、空串
     *
     * @param obj 源对象
     * @return
     */
    public static boolean checkNotNullObj(Object obj) {
        if (Objects.isNull(obj)) {
            return false;
        }

        if (obj instanceof Character || obj instanceof String) {
            if (StringUtils.isEmpty(obj.toString())) {
                return false;
            }
        }
        return true;
    }
}
```
