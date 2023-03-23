# 前言

<font face="幼圆">

!> 整合`dom4j`，解析`xml`字符串，转`JSON`格式

</font>

# 依赖

```xml
<!-- 读写xml文件 -->
<dependency>
    <groupId>org.dom4j</groupId>
    <artifactId>dom4j</artifactId>
    <version>2.1.3</version>
</dependency>
```

# 准备xml

```xml

<beans>

    <bean id="userService" name="userService" class="com.alibaba.frame.a.A_100$UserService" scope="prototype">
        <property name="id" value="10001"/>
        <property name="name" value="普通管理员"/>
        <!-- 引用代理类 -->
        <property name="userDao" ref="proxyUserDao"/>
    </bean>

    <!--  代理类  -->
    <bean id="proxyUserDao" name="proxyUserDao" class="com.alibaba.frame.a.A_100$ProxyBeanFactory"/>

    <!-- BeanPostProcessor 是在 Bean 对象实例化之后修改 Bean 对象，也可以替换 Bean 对象 -->
    <bean id="myBeanPostProcessor" name="myBeanPostProcessor"
          class="com.alibaba.frame.a.A_100$MyBeanPostProcessor"/>

    <!-- BeanDefinition 加载完成后，实例化 Bean 对象之前，提供修改 BeanDefinition 属性的机制 -->
    <bean id="myBeanFactoryPostProcessor" name="myBeanFactoryPostProcessor"
          class="com.alibaba.frame.a.A_100$MyBeanFactoryPostProcessor"/>

</beans>
```

# 整合dom4j，解析xml字符串，转JSON格式

```java
package com.alibaba.frame.a;
import com.alibaba.fastjson.JSON;
import org.dom4j.Attribute;
import org.dom4j.Document;
import org.dom4j.DocumentHelper;
import org.dom4j.Element;
import org.springframework.util.CollectionUtils;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

/**
 * @author hspcadmin
 * @version 1.0
 * @description 整合dom4j, 解析xml字符串, 转JSON格式
 */
public class A_119 {
	public static void main(String[] args) throws Exception {
		String text = "<beans>\n" + "\n" + "    <bean id=\"userService\" name=\"userService\" class=\"com.alibaba.frame.a.A_100$UserService\" scope=\"prototype\">\n" + "        <property name=\"id\" value=\"10001\"/>\n" + "        <property name=\"name\" value=\"普通管理员\"/>\n" + "        <!-- 引用代理类 -->\n" + "        <property name=\"userDao\" ref=\"proxyUserDao\"/>\n" + "    </bean>\n" + "\n" + "    <!--  代理类  -->\n" + "    <bean id=\"proxyUserDao\" name=\"proxyUserDao\" class=\"com.alibaba.frame.a.A_100$ProxyBeanFactory\"/>\n" + "\n" + "    <!-- BeanPostProcessor 是在 Bean 对象实例化之后修改 Bean 对象，也可以替换 Bean 对象 -->\n" + "    <bean id=\"myBeanPostProcessor\" name=\"myBeanPostProcessor\"\n" + "          class=\"com.alibaba.frame.a.A_100$MyBeanPostProcessor\"/>\n" + "\n" + "    <!-- BeanDefinition 加载完成后，实例化 Bean 对象之前，提供修改 BeanDefinition 属性的机制 -->\n" + "    <bean id=\"myBeanFactoryPostProcessor\" name=\"myBeanFactoryPostProcessor\"\n" + "          class=\"com.alibaba.frame.a.A_100$MyBeanFactoryPostProcessor\"/>\n" + "\n" + "</beans>";
		Document document = DocumentHelper.parseText(text);
		Element root = document.getRootElement();

		Map<Object, Object> rootHashMap = new HashMap<>(16);
		Map<String, Object> elementHasMap = new HashMap<>(16);
		toElementTree(root, elementHasMap);
		rootHashMap.put(root.getName(), elementHasMap);
		System.out.println(JSON.toJSONString(rootHashMap));
	}

	/**
	 * 构建树结构
	 *
	 * @param element       标签对象
	 * @param elementHasMap 标签对应的数据结构
	 */
	@SuppressWarnings("unchecked")
	public static void toElementTree(Element element, Map<String, Object> elementHasMap) {

		// 解析标签属性
		List<Attribute> attributes = element.attributes();
		if (!CollectionUtils.isEmpty(attributes)) {
			for (Attribute attribute : attributes) {
				String name = attribute.getName();
				String value = attribute.getValue();
				elementHasMap.put(name, value);
			}
		}

		// 解析子标签
		List<Element> elements = element.elements();
		if (!CollectionUtils.isEmpty(elements)) {
			// 构建集合, 名字相同的标签的数据归类在一起
			List<Map<String, Object>> elementList = new ArrayList<>(10);
			for (Element child : elements) {
				Map<String, Object> elementHashMap = new HashMap<>(16);
				// 递归解析标签
				toElementTree(child, elementHashMap);

				// 判断是否有名字相同的标签
				if (elementHasMap.containsKey(child.getName())) {
					// 标签名字相同时, 取出名字相同的第一个标签对应的数据, 归类到集合里
					if (CollectionUtils.isEmpty(elementList)) {
						Map<String, Object> first = (Map<String, Object>) elementHasMap.get(child.getName());
						elementList.add(first);
					}
					elementList.add(elementHashMap);
					elementHasMap.put(child.getName(), elementList);
				} else {
					elementHasMap.put(child.getName(), elementHashMap);
				}
			}
		}
	}
}
```

# 解析后JSON数据

```json
{
  "beans": {
    "bean": [
      {
        "scope": "prototype",
        "name": "userService",
        "property": [
          {
            "name": "id",
            "value": "10001"
          },
          {
            "name": "name",
            "value": "普通管理员"
          },
          {
            "ref": "proxyUserDao",
            "name": "userDao"
          }
        ],
        "id": "userService",
        "class": "com.alibaba.frame.a.A_100$UserService"
      },
      {
        "name": "proxyUserDao",
        "id": "proxyUserDao",
        "class": "com.alibaba.frame.a.A_100$ProxyBeanFactory"
      },
      {
        "name": "myBeanPostProcessor",
        "id": "myBeanPostProcessor",
        "class": "com.alibaba.frame.a.A_100$MyBeanPostProcessor"
      },
      {
        "name": "myBeanFactoryPostProcessor",
        "id": "myBeanFactoryPostProcessor",
        "class": "com.alibaba.frame.a.A_100$MyBeanFactoryPostProcessor"
      }
    ]
  }
}
```
