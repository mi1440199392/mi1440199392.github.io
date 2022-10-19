```java
package com.hundsun.demo.list.c;
import java.util.function.Predicate;

/**
 * @Desc 职责链模式
 * @Date 2022/9/6
 * @Created root
 */
public class C_105 {
    public static void main(String[] args) {

        ProductObjectHandler handlerA = new ProductObjectHandlerA(); // 处理者A
        ProductObjectHandler handlerB = new ProductObjectHandlerB(); // 处理者B
        ProductObjectHandler handlerC = new ProductObjectHandlerC(); // 处理者C

        ProductObject productObject1 = new ProductObject(1, "食品", 100); // 消息对象
        ProductObject productObject2 = new ProductObject(2, "家具", 1000); // 消息对象
        ProductObject productObject3 = new ProductObject(3, "汽车", 100000); // 消息对象

        handlerA.setNextHandler(handlerB); // 设置下一个处理者
        handlerB.setNextHandler(handlerC); // 设置下一个处理者

        handlerA.handler(productObject2); // 处理消息
        handlerB.handler(productObject3); // 处理消息
    }

    // 消息对象
    static class ProductObject {
        private int id;
        private String name;
        private int price;

        public ProductObject(int id, String name, int price) {
            this.id = id;
            this.name = name;
            this.price = price;
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

        public int getPrice() {
            return price;
        }

        public void setPrice(int price) {
            this.price = price;
        }

        @Override
        public String toString() {
            return "ProductObject{" +
                    "id=" + id +
                    ", name='" + name + '\'' +
                    ", price=" + price +
                    '}';
        }
    }

    // 处理者抽象
    static abstract class ProductObjectHandler {
        protected ProductObjectHandler nextHandler;

        public void setNextHandler(ProductObjectHandler nextHandler) {
            this.nextHandler = nextHandler;
        }

        protected abstract void handler(ProductObject productObject);
    }

    // 条件抽象
    interface ProductObjectCondition {

        boolean check(int price, Predicate<Integer> predicate);
    }

    static class ProductObjectHandlerA extends ProductObjectHandler implements ProductObjectCondition {

        @Override
        protected void handler(ProductObject productObject) {
            boolean flag = check(productObject.getPrice(), (price -> price >= 0 && price <= 100));

            if (flag) {
                System.out.println("productObject = " + productObject);
                System.out.println("ProductObjectHandlerA 处理了这条消息...");
            } else {
                System.out.println("ProductObjectHandlerA 无法处理这条消息... ,转发给下一个处理者");
                super.nextHandler.handler(productObject);
            }
        }


        @Override
        public boolean check(int price, Predicate<Integer> predicate) {
            return predicate.test(price);
        }
    }

    static class ProductObjectHandlerB extends ProductObjectHandler implements ProductObjectCondition {

        @Override
        protected void handler(ProductObject productObject) {
            boolean flag = check(productObject.getPrice(), (price -> price > 100 && price <= 1000));

            if (flag) {
                System.out.println("productObject = " + productObject);
                System.out.println("ProductObjectHandlerB 处理了这条消息...");
            } else {
                System.out.println("ProductObjectHandlerB 无法处理这条消息... ,转发给下一个处理者");
                super.nextHandler.handler(productObject);
            }
        }


        @Override
        public boolean check(int price, Predicate<Integer> predicate) {
            return predicate.test(price);
        }
    }

    static class ProductObjectHandlerC extends ProductObjectHandler implements ProductObjectCondition {

        @Override
        protected void handler(ProductObject productObject) {
            boolean flag = check(productObject.getPrice(), (price -> price > 1000));

            if (flag) {
                System.out.println("productObject = " + productObject);
                System.out.println("ProductObjectHandlerC 处理了这条消息...");
            } else {
                System.out.println("ProductObjectHandlerC 无法处理这条消息... ,转发给下一个处理者");
                super.nextHandler.handler(productObject);
            }
        }


        @Override
        public boolean check(int price, Predicate<Integer> predicate) {
            return predicate.test(price);
        }
    }
}
```

> 控制台打印

```java
ProductObjectHandlerA 无法处理这条消息... ,转发给下一个处理者
productObject = ProductObject{id=2, name='家具', price=1000}
ProductObjectHandlerB 处理了这条消息...
ProductObjectHandlerB 无法处理这条消息... ,转发给下一个处理者
productObject = ProductObject{id=3, name='汽车', price=100000}
ProductObjectHandlerC 处理了这条消息...
```