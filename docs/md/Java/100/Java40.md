```java
package com.hundsun.demo.list.b;
import java.util.ArrayList;
import java.util.List;

/**
 * @Description 组合模式
 * @Date 2022/7/29
 * @Created root
 */
public class B_106 {

    public static void main(String[] args) {

        TextFile textFile = new TextFile("text");
        SQLFile sqlFile = new SQLFile("SQL");
        JavaFile javaFile = new JavaFile("Java");

        Forder forder = new Forder("编程语言");
        Forder PHPchildren = new Forder("PHP文档");
        Forder Cchildren = new Forder("C文档");
        Forder Phthonchildren = new Forder("Phthon文档");
        forder.add(textFile);
        forder.add(sqlFile);
        forder.add(javaFile);
        forder.add(PHPchildren);
        forder.add(Cchildren);
        forder.add(Phthonchildren);
        forder.kill();
    }


    interface Component {
        void kill();
    }

    static abstract class File implements Component {

    }

    static class TextFile extends File {
        private String name;

        public TextFile(String name) {
            this.name = name;
        }

        public String getName() {
            return name;
        }

        @Override
        public void kill() {
            System.out.println("文件：" + getName() + "开始杀毒扫描");
        }
    }

    static class SQLFile extends File {
        private String name;

        public SQLFile(String name) {
            this.name = name;
        }

        public String getName() {
            return name;
        }

        @Override
        public void kill() {
            System.out.println("文件：" + getName() + "开始杀毒扫描");
        }
    }

    static class JavaFile extends File {
        private String name;

        public JavaFile(String name) {
            this.name = name;
        }

        public String getName() {
            return name;
        }

        @Override
        public void kill() {
            System.out.println("文件：" + getName() + "开始杀毒扫描");
        }
    }

    static class Forder implements Component {
        private String name;
        private List<Component> components = new ArrayList<>(10);

        public Forder(String name) {
            this.name = name;
        }

        public String getName() {
            return name;
        }

        public void remove(int index) {
            components.remove(index);
        }

        public void add(Component component) {
            components.add(component);
        }

        public boolean isEmpty() {
            return components.isEmpty();
        }

        @Override
        public void kill() {
            System.out.println("文件夹：" + getName() + "开始杀毒扫描");

            for (Component component : components) {
                component.kill();
            }
        }
    }
}
```

> 控制台打印

```java
jsonString = {"flag":true,"name":"张三","id":1,"age":20,"status":true}
student = Student{id=1, name='张三', age=20, status=true}
```