```java
package com.hundsun.demo.list.d;

/**
 * @Desc 策略模式
 * @Date 2022/9/9
 * @Created root
 */
public class D_103 {
    public static void main(String[] args) {
        Helicopter helicopter = new Helicopter("直升机");
        AbstractTargetObjectContext.setAbstractTargetObject(helicopter);
        AbstractTargetObjectContext.feature();

        AirPlane airPlane = new AirPlane("客机");
        AbstractTargetObjectContext.setAbstractTargetObject(airPlane);
        AbstractTargetObjectContext.feature();
    }

    // 上下文环境
    final static class AbstractTargetObjectContext {
        private static AbstractTargetObject abstractTargetObject;

        public static void setAbstractTargetObject(AbstractTargetObject abstractTargetObject) {
            AbstractTargetObjectContext.abstractTargetObject = abstractTargetObject;
        }

        public static void feature() {
            System.out.print(abstractTargetObject.name);
            abstractTargetObject.startFeature();
            abstractTargetObject.runFeature();
        }
    }


    // 飞机种类抽象
    static abstract class AbstractTargetObject {
        protected String name;

        // 起飞
        public abstract void startFeature();

        // 飞行
        public abstract void runFeature();
    }

    // 直升机
    static class Helicopter extends AbstractTargetObject {

        public Helicopter(String name) {
            this.name = name;
        }

        @Override
        public void startFeature() {
            System.out.print("垂直起飞,");
        }

        @Override
        public void runFeature() {
            System.out.println("亚音速飞行");
        }
    }

    // 客机
    static class AirPlane extends AbstractTargetObject {

        public AirPlane(String name) {
            this.name = name;
        }

        @Override
        public void startFeature() {
            System.out.print("长距离起飞,");
        }

        @Override
        public void runFeature() {
            System.out.println("亚音速飞行");
        }
    }

    // 歼击机
    static class Fighter extends AbstractTargetObject {

        public Fighter(String name) {
            this.name = name;
        }

        @Override
        public void startFeature() {
            System.out.println("长距离起飞,");
        }

        @Override
        public void runFeature() {
            System.out.println("超音速飞行");
        }
    }

    // 鹞式战斗机
    static class Harrier extends AbstractTargetObject {

        public Harrier(String name) {
            this.name = name;
        }

        @Override
        public void startFeature() {
            System.out.println("垂直起飞");
        }

        @Override
        public void runFeature() {
            System.out.println("超音速飞行");
        }
    }
}
```

> 控制台打印

```java
直升机垂直起飞,亚音速飞行
客机长距离起飞,亚音速飞行
```