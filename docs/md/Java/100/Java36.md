```java
package com.hundsun.demo.list.d;
import org.apache.commons.lang3.ObjectUtils;
import java.util.ArrayList;
import java.util.List;

/**
 * @Desc 观察者模式
 * @Date 2022/9/8
 * @Created root
 */
public class D_101 {
    public static void main(String[] args) {

        // 战队成员
        TargetObjectA targetObjectA = new TargetObjectA() {{
            setName("张三");
        }};
        TargetObjectB targetObjectB = new TargetObjectB() {{
            setName("李四");
        }};
        TargetObjectC targetObjectC = new TargetObjectC() {{
            setName("王五");
        }};
        TargetObjectD targetObjectD = new TargetObjectD() {{
            setName("赵六");
        }};


        // 控制中心实现
        ControlCenterObjectA controlCenterObjectA = new ControlCenterObjectA() {{
            setName("梦之蓝国家队");
            add(targetObjectA);
            add(targetObjectB);
            add(targetObjectC);
            add(targetObjectD);
        }};

        // 战队成员, 受到攻击, 通知其他成员前来支援
        targetObjectB.dos(controlCenterObjectA);
    }


    // 控制中心抽象
    static abstract class AbstractControlCenterObject {
        // 战队成员集合
        protected static List<AbstractTargetObject> abstractTargetObjects = new ArrayList<>(10);
        // 战队名称
        private String name;

        // 添加战队成员
        public void add(AbstractTargetObject abstractTargetObject) {
            abstractTargetObjects.add(abstractTargetObject);
        }

        // 删除战队成员
        public void remove(AbstractTargetObject abstractTargetObject) {
            abstractTargetObjects.remove(abstractTargetObject);
        }

        public void setName(String name) {
            this.name = name;
        }

        // 支援
        public abstract void notifyObject(String name);
    }

    // 控制中心实现
    static class ControlCenterObjectA extends AbstractControlCenterObject {

        @Override
        public void notifyObject(String name) {
            for (AbstractTargetObject abstractTargetObject : abstractTargetObjects) {
                if (ObjectUtils.notEqual(name, abstractTargetObject.name)) {
                    abstractTargetObject.help();
                }
            }
        }
    }

    // 战队成员抽象
    static abstract class AbstractTargetObject {
        // 战队成员名称
        protected String name;

        public void setName(String name) {
            this.name = name;
        }

        // 支援
        public abstract void help();

        // 受到攻击
        public abstract void dos(AbstractControlCenterObject abstractControlCenterObject);
    }

    // 战队成员实现
    static class TargetObjectA extends AbstractTargetObject {

        @Override
        public void help() {
            System.out.println("战队成员：" + this.name + ", 前来支援");
        }

        @Override
        public void dos(AbstractControlCenterObject abstractControlCenterObject) {
            System.out.println("战队成员：" + this.name + ", 收到攻击, 通知其它战队成员前来支援");
            abstractControlCenterObject.notifyObject(this.name);
        }
    }

    // 战队成员实现
    static class TargetObjectB extends AbstractTargetObject {

        @Override
        public void help() {
            System.out.println("战队成员：" + this.name + ", 前来支援");
        }

        @Override
        public void dos(AbstractControlCenterObject abstractControlCenterObject) {
            System.out.println("战队成员：" + this.name + ", 收到攻击, 通知其它战队成员前来支援");
            abstractControlCenterObject.notifyObject(this.name);
        }
    }

    // 战队成员实现
    static class TargetObjectC extends AbstractTargetObject {

        @Override
        public void help() {
            System.out.println("战队成员：" + this.name + ", 前来支援");
        }

        @Override
        public void dos(AbstractControlCenterObject abstractControlCenterObject) {
            System.out.println("战队成员：" + this.name + ", 收到攻击, 通知其它战队成员前来支援");
            abstractControlCenterObject.notifyObject(this.name);
        }
    }

    // 战队成员实现
    static class TargetObjectD extends AbstractTargetObject {

        @Override
        public void help() {
            System.out.println("战队成员：" + this.name + ", 前来支援");
        }

        @Override
        public void dos(AbstractControlCenterObject abstractControlCenterObject) {
            System.out.println("战队成员：" + this.name + ", 收到攻击, 通知其它战队成员前来支援");
            abstractControlCenterObject.notifyObject(this.name);
        }
    }
}
```

> 控制台打印

```java
战队成员：李四, 收到攻击, 通知其它战队成员前来支援
战队成员：张三, 前来支援
战队成员：王五, 前来支援
战队成员：赵六, 前来支援
```