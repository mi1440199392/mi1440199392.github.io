```java
package com.hundsun.demo.list.c;

/**
 * @Desc 迭代器模式
 * @Date 2022/9/8
 * @Created root
 */
public class C_109 {

    // 聚合对象抽象
    interface List<E> {

        void add(E e);

        void remove(E e);

        E get(int index);

        // 迭代器工厂
        Iterator<E> iterator();
    }

    // 聚合对象实现
    static class ArrayList<E> implements List<E> {
        private Object[] element;
        private int size;

        public ArrayList(int capacity) {
            this.element = new Object[capacity];
        }

        @Override
        public void add(E o) {
            System.out.println("添加一个元素");
        }

        @Override
        public void remove(Object o) {
            System.out.println("删除一个元素");
        }

        @Override
        public E get(int index) {
            System.out.println("获取一个元素");
            return null;
        }

        @Override
        public Iterator<E> iterator() {
            return new IteratorA<E>();
        }
    }

    // 聚合对象实现
    static class LinkedList<E> implements List<E> {
        private Object[] element;
        private int size;

        public LinkedList(int capacity) {
            this.element = new Object[capacity];
        }

        @Override
        public void add(E o) {
            System.out.println("添加一个元素");
        }

        @Override
        public void remove(Object o) {
            System.out.println("删除一个元素");
        }

        @Override
        public E get(int index) {
            System.out.println("获取一个元素");
            return null;
        }

        @Override
        public Iterator<E> iterator() {
            return new IteratorB<E>();
        }
    }

    // 迭代器抽象
    static abstract class Iterator<E> {
        public abstract boolean hasNext();

        public abstract E next();

        public abstract boolean remove(E e);
    }

    // 迭代器实现
    static class IteratorA<E> extends Iterator<E> {
        private List<E> list;

        @Override
        public boolean hasNext() {
            return false;
        }

        @Override
        public E next() {
            return null;
        }

        @Override
        public boolean remove(E e) {
            return false;
        }
    }

    // 迭代器实现
    static class IteratorB<E> extends Iterator<E> {
        private List<E> list;

        @Override
        public boolean hasNext() {
            return false;
        }

        @Override
        public E next() {
            return null;
        }

        @Override
        public boolean remove(E e) {
            return false;
        }
    }
}

```