```java
package com.hundsun.demo.list.c;

/**
 * @Desc 中介者模式
 * @Date 2022/9/8
 * @Created root
 */
public class C_110 {
    public static void main(String[] args) {
        ProxyObject proxyObjectA = new ProxyObjectA();
        ObjectA objectA = new ObjectA(proxyObjectA);
        ObjectB objectB = new ObjectB(proxyObjectA);
        ObjectC objectC = new ObjectC(proxyObjectA);

        ((ProxyObjectA) proxyObjectA).setObjectA(objectA);
        ((ProxyObjectA) proxyObjectA).setObjectB(objectB);
        ((ProxyObjectA) proxyObjectA).setObjectC(objectC);

        objectB.invoke();

    }

    static abstract class AbstractObject {
        protected ProxyObject proxyObject;

        public abstract void invoke();
    }

    static abstract class ProxyObject {

        public abstract void println(AbstractObject abstractObject);
    }

    static class ProxyObjectA extends ProxyObject {
        private ObjectA objectA;
        private ObjectB objectB;
        private ObjectC objectC;


        public void setObjectA(ObjectA objectA) {
            this.objectA = objectA;
        }

        public void setObjectB(ObjectB objectB) {
            this.objectB = objectB;
        }

        public void setObjectC(ObjectC objectC) {
            this.objectC = objectC;
        }

        @Override
        public void println(AbstractObject abstractObject) {
            if (abstractObject == objectA) {
                this.objectB.printlnB();
                this.objectC.printlnC();
            } else if (abstractObject == objectB) {
                this.objectA.printlnA();
                this.objectC.printlnC();
            } else {
                this.objectB.printlnB();
                this.objectC.printlnC();
            }
        }
    }

    static class ObjectA extends AbstractObject {

        public ObjectA(ProxyObject proxyObject) {
            this.proxyObject = proxyObject;
        }

        public void printlnA() {
            System.out.println("类ObjectA 执行 println()方法");
        }

        @Override
        public void invoke() {
            this.proxyObject.println(this);
        }
    }

    static class ObjectB extends AbstractObject {

        public ObjectB(ProxyObject proxyObject) {
            this.proxyObject = proxyObject;
        }


        public void printlnB() {
            System.out.println("类ObjectB 执行 println()方法");
        }

        @Override
        public void invoke() {
            this.proxyObject.println(this);
        }
    }

    static class ObjectC extends AbstractObject {

        public ObjectC(ProxyObject proxyObject) {
            this.proxyObject = proxyObject;
        }

        public void printlnC() {
            System.out.println("类ObjectC 执行 println()方法");
        }

        @Override
        public void invoke() {
            this.proxyObject.println(this);
        }
    }
}
```

> 控制台打印

```java
类ObjectA 执行 println()方法
类ObjectC 执行 println()方法
```