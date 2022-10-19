```java
package com.hundsun.demo.list.c;
import com.alibaba.fastjson.JSON;
import java.util.HashMap;
import java.util.Map;

/**
 * @Description Map转Bean
 * @Date 2022/8/26
 * @Created root
 */
public class C_102 {
    public static void main(String[] args) {


        Map<String, Object> beanDefineMap = new HashMap<>(16);
        beanDefineMap.put("id", 1);
        beanDefineMap.put("name", "张三");
        beanDefineMap.put("age", 20);
        beanDefineMap.put("status", true);
        beanDefineMap.put("flag", true);

        String jsonString = JSON.toJSONString(beanDefineMap);
        System.out.println("jsonString = " + jsonString);

        Student student = JSON.parseObject(jsonString, Student.class);
        System.out.println("student = " + student);

    }


    static class Student {
        private int id;
        private String name;
        private int age;
        private boolean status;

        @Override
        public String toString() {
            return "Student{" +
                    "id=" + id +
                    ", name='" + name + '\'' +
                    ", age=" + age +
                    ", status=" + status +
                    '}';
        }

        public int getId() {
            return id;
        }

        public void setId(int id) {
            this.id = id;
        }

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }

        public int getAge() {
            return age;
        }

        public void setAge(int age) {
            this.age = age;
        }

        public boolean isStatus() {
            return status;
        }

        public void setStatus(boolean status) {
            this.status = status;
        }
    }
}
```

> 控制台打印

```java
jsonString = {"flag":true,"name":"张三","id":1,"age":20,"status":true}
student = Student{id=1, name='张三', age=20, status=true}
```