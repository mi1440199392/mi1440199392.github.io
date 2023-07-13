# 前言

<font face="幼圆">

> ArrayList源码解析

</font>

# ArrayList常见方法及扩容机制

```java 
public class ArrayList<E> extends AbstractList<E> implements List<E>, RandomAccess, Cloneable, java.io.Serializable {
    // 默认初始容量
    private static final int DEFAULT_CAPACITY = 10;
    
    // 空元素数组
    private static final Object[] EMPTY_ELEMENTDATA = {};
    
    // 默认空容量数组
    private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};
    
    // 存放元素的数组
    transient Object[] elementData;
    
    // 集合存放元素的数量
    private int size;
    
    
    /**
     * 初始化集合
     *
     * @param  initialCapacity 设置集合的容量
     * @throws IllegalArgumentException
     */
    public ArrayList(int initialCapacity) {
        if (initialCapacity > 0) {
            this.elementData = new Object[initialCapacity]; // 设置elementData=固定长度的数组
        } else if (initialCapacity == 0) {
            this.elementData = EMPTY_ELEMENTDATA;
        } else {
            throw new IllegalArgumentException("Illegal Capacity: "+ initialCapacity);
        }
    }
    
    
    /**
     * 初始化集合
     *
     */
    public ArrayList() {
        this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA; // 设置存放元素的elementData值为空数组
    }
    
    
    /**
     * 添加元素
     *
     * @param 
     * @return
     */
    public boolean add(E e) {
        ensureCapacityInternal(size + 1);  // 数组扩容，将新元素的数量（size+1）传入扩容方法，elementData此时设置为长度10的新数组
        
        elementData[size++] = e; // 1、集合元素数量size + 1  2、将新增的元素存放 elementData 数组
        return true;
    }
    
    /**
     * 扩容方法总入口
     *
     * @param  minCapacity = 元素数量（size + 1）
     * @return
     */
    private void ensureCapacityInternal(int minCapacity) {
        
        // 1、第一次add()元素时，calculateCapacity(elementData, minCapacity) 比较元素数量（size+1）和 默认容量10，取最大值；2、否则容量阈值取元素数量size+1
        ensureExplicitCapacity(calculateCapacity(elementData, minCapacity));
    }
    
    /**
     * 1、空参数构造条件下；比较数组长度 和 集合元素数量 + 1，取最大值
     * 2、否则后续操作，直接返回 集合元素数量 + 1
     * @param  minCapacity = 元素数量（size + 1）
     * @return
     */
    private static int calculateCapacity(Object[] elementData, int minCapacity) {
    
        // 1、如果调用ArrayList<>()构造方法，elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA
        if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
            
            // 1、默认容量DEFAULT_CAPACITY = 10；2、添加新元素数量（size+1）和 默认容量10比较，取最大值作为集合容量
            return Math.max(DEFAULT_CAPACITY, minCapacity);
        }
        return minCapacity;
    }
    
    
    /**
     * 判断数组扩容的时机
     *
     * @param  minCapacity = 元素数量（size + 1）
     * @return
     */
    private void ensureExplicitCapacity(int minCapacity) {
        modCount++;  // modCount：集合数据被修改的次数，默认：0

        // 1、new ArrayList()，第一次添加元素add()时，elementData={}，数组长度0，条件成立；2、当elementData[10]的元素存满时，再存放新元素的时候，开始扩容
        if (minCapacity - elementData.length > 0)
            // 扩容的核心实现
            // 1、minCapacity值为10；2、数组扩容的核心方法；3、第一次add()元素时，elementData值为长度10的数组
            grow(minCapacity);
    }
    


    /**
     * 扩容的核心实现
     *
     * @param 1、new ArrayList()，第一次add()时，容量值为10
     */
    private void grow(int minCapacity) {
        
        // 1、new ArrayList()，oldCapacity的值为0；2、当elementData[10]元素存放的时候，再添加新元素开始扩容
        int oldCapacity = elementData.length;
        
        // 原数组长度1.5倍扩容
        int newCapacity = oldCapacity + (oldCapacity >> 1); 
        
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;  // 1、new ArrayList()，添加元素add()，执行当前这个if分支，将新容量设置为10

        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        
        // 1、数组扩容，elementData = 新数组；2、将旧数组{}的元素 复制 给 长度为10的新数组里；3、如果是第一次添加add元素的条件下
        elementData = Arrays.copyOf(elementData, newCapacity);
    }
    
    
    
    /**
     * 获取元素
     *
     * @param 
     * @return
     * @throws
     */
    public E get(int index) {
    
        // 1、检查索引是否越界
        rangeCheck(index);

        // 返回 elementData 数组该索引位置的元素
        return elementData(index);
    }
    
    
    /**
     * 检查索引是否越界
     * 
     */
    private void rangeCheck(int index) {
        if (index >= size)
            throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
    }
    
    
    /**
     * 根据索引，设置新值
     *
     * @param 
     * @param 
     * @return
     * @throws
     */
    public E set(int index, E element) {
        // 检查索引是否越界
        rangeCheck(index);
        
        E oldValue = elementData(index);
        
        // 设置 elementData 数组该索引位置的新值
        elementData[index] = element;
        
        // 返回旧值
        return oldValue;
    }
    
    
    
    /**
     * 在该索引位置，插入元素
     *
     * @param 
     * @param 
     * @throws
     */
    public void add(int index, E element) {
    
        // 1、校验索引位置是否越界
        rangeCheckForAdd(index);

        // 1、扩容方法；2、将modCount值 + 1，记录集合被改修的次数
        ensureCapacityInternal(size + 1);
        
        // 1、将该索引位置及后面的所有元素，往后移动一个索引；
        System.arraycopy(elementData, index, elementData, index + 1, size - index);
        
        // 1、移动一个索引位后，当前这个索引位置设置新插入的元素
        elementData[index] = element;
        
        // 1、集合存放元素数量 + 1
        size++;
    }
    
    
    /**
     * 校验索引位置是否越界
     *
     */
    private void rangeCheckForAdd(int index) {
        if (index > size || index < 0)
            throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
    }
    
    
    
    /**
     * 根据索引，删除元素
     *
     * @param
     * @return
     * @throws
     */
    public E remove(int index) {
    
        // 1、校验索引位置是否越界
        rangeCheck(index);
        
        modCount++; // 1、将modCount+1，记录集合被修改的次数
        E oldValue = elementData(index); // 1、拿到该索引位置的元素

        // 1、numMoved = 当前索引位置 至 最后一个索引位置的距离；2、如果当前索引是最后一个有元素的索引位置，则 numMoved=0
        int numMoved = size - index - 1;
        
        // 1、将改索引后的所有元素，向左移动一个索引位置
        if (numMoved > 0)
            System.arraycopy(elementData, index+1, elementData, index, numMoved);
        
        // 1、numMoved=0情况下，说明index是最后一个有元素的索引位置，size--是最后一个有元素的索引位置，将其设置null，就是删除该元素；2、-- size，每删除一个元素，集合元素的数量-1
        elementData[--size] = null; 
        
        // 返回旧值
        return oldValue;
    }
    
    

    /**
     * 删除该元素
     *
     * @param
     * @return
     */
    public boolean remove(Object o) {
        if (o == null) {
            // 1、遍历元素数量以内的数组索引位置
            for (int index = 0; index < size; index++)
                if (elementData[index] == null) {
                    // 1、遍历数组，删除该元素，如果数组中相同的元素多，每删除一个元素就执行一次fastRemove()方法；fastRemove()的实现：每删除一个元素就要涉及数组的复制，比较消耗性能
                    fastRemove(index);
                    return true;
                }
        } else {
            for (int index = 0; index < size; index++)
                if (o.equals(elementData[index])) {
                    // 1、遍历数组，删除该元素，如果数组中相同的元素多，每删除一个元素就执行一次fastRemove()方法；fastRemove()的实现：每删除一个元素就要涉及数组的复制，比较消耗性能
                    fastRemove(index);
                    return true;
                }
        }
        return false;
    }
    



    /*
     * 删除元素核心实现
     * 
     */
    private void fastRemove(int index) {
        modCount++; // 1、记录集合数据被修改的次数
        
        // 1、numMoved = 当前索引位置 至 最后一个索引位置的距离；2、如果当前索引是最后一个有元素的索引位置，则 numMoved=0
        int numMoved = size - index - 1;
        
        // 1、将改索引后的所有元素，向左移动一个索引位置
        if (numMoved > 0)
            System.arraycopy(elementData, index+1, elementData, index, numMoved);
            
        // 1、numMoved=0情况下，说明index是最后一个有元素的索引位置，size--是最后一个有元素的索引位置，将其设置null，就是删除该元素
        elementData[--size] = null;
    }



    /**
     * 清空集合元素
     *
     */
    public void clear() {
        modCount++; // 记录集合数据被修改的次数

        // 1、遍历数组，设置元素为null，说明并不会改变elementData数组长度
        for (int i = 0; i < size; i++)
            elementData[i] = null;

        // 1、集合存放元素的数量设置 0
        size = 0;
    }



    /**
     * 添加新集合
     *
     * @param
     * @return
     * @throws NullPointerException
     */
    public boolean addAll(Collection<? extends E> c) {
    
        // 复制旧数组，返回新数组
        Object[] a = c.toArray();
        
        // 新集合的长度
        int numNew = a.length;
        
        // 1、扩容方法，size + numNew = 原集合的元素数量 + 新集合元素的数量，触发扩容后elementData的长度为原集合的元素数量 + 新集合元素的数量
        ensureCapacityInternal(size + numNew); 
        
        // 1、从elementData数组最后一个有元素的下个索引位置开始，复制新集合的元素进来；2、当前size：最后一个有元素的索引位置，也就是原集合的元素数量
        System.arraycopy(a, 0, elementData, size, numNew);
        
        // 1、添加新集合进来，将size元素数量 + 新集合的元素数量 = 合并后的元素数量
        size += numNew;
        
        return numNew != 0;
    }

    /**
     * 复制旧数组，返回新数组
     *
     * @param
     * @return
     * @throws NullPointerException
     */
    public Object[] toArray() {
        return Arrays.copyOf(elementData, size);
    }



    /**
     * 在指定索引位置后，添加新集合
     *
     * @param 
     * @param 
     * @return
     * @throws IndexOutOfBoundsException
     * @throws NullPointerException
     */
    public boolean addAll(int index, Collection<? extends E> c) {
        // 检查索引是否越界
        rangeCheckForAdd(index);

        Object[] a = c.toArray();
        int numNew = a.length; // 新集合的长度
        
        // 1、扩容方法，size + numNew = 原集合的元素数量 + 新集合元素的数量，触发扩容后elementData的长度为原集合的元素数量 + 新集合元素的数量
        ensureCapacityInternal(size + numNew);
        
        // 1、numMoved = （当前索引位置） 至  （elementData数组最后一个有元素的下一个索引位置）的距离
        int numMoved = size - index;
        
        // 1、将该索引位置后的元素，向后移动（新集合元素数量）个索引位置
        if (numMoved > 0)
            System.arraycopy(elementData, index, elementData, index + numNew, numMoved);
        
        // 1、将新集合的元素，从当前索引位置起开始复制，复制（新集合元素数量）个索引位置
        System.arraycopy(a, 0, elementData, index, numNew);
        
        // 1、添加新集合进来，将size元素数量 + 新集合的元素数量 = 合并后的元素数量
        size += numNew;
        
        return numNew != 0;
    }
}
```

# ArrayList的迭代器iterator()

## 迭代器Itr常见方法

```java 
public class ArrayList<E> extends AbstractList<E> implements List<E>, RandomAccess, Cloneable, java.io.Serializable {

    /**
     *  返回一个迭代器Itr：ArrayList内部类
     *
     * @return
     */
    public Iterator<E> iterator() {
        return new Itr();
    }

    /**
     * Itr迭代器：ArrayList内部类
     *
     * @return
     */
    private class Itr implements Iterator<E> {
        int cursor;      // 1、要返回的下一个元素的索引，迭代器要迭代下一个元素的索引
        int lastRet = -1; // 1、返回的最后一个元素的索引；-1表示不存在元素
        int expectedModCount = modCount; // 1、记录集合数据被修改的次数：默认值同步了modCount的值

        Itr() {}
        
        
        /**
         * 1、是否不等于集合元素的数量；等于集合元素的数量，说明最后一个元素遍历完了
         *
         * @return
         */
        public boolean hasNext() {
            return cursor != size;
        }
        
        
        /**
         * 获取下一个元素
         *
         * @return
         */
        @SuppressWarnings("unchecked")
        public E next() {
        
            // 1、检查是否协同修改，比较modCount != expectedModCount
            checkForComodification();
            
            // 要返回的下一个元素的索引，迭代器要迭代下一个元素的索引，初始值 0
            int i = cursor;
            if (i >= size) throw new NoSuchElementException();
            Object[] elementData = ArrayList.this.elementData;
            if (i >= elementData.length) throw new ConcurrentModificationException();
            
            // 迭代元素后，指针 + 1指向下一个
            cursor = i + 1;
            
            // 获取当前迭代指针的元素
            return (E) elementData[lastRet = i];
        }


        /**
         * 删除元素
         *
         * @return
         */
        public void remove() {
            // lastRet初始值：-1，每次删除元素后，lastRet值都恢复为-1；在迭代器迭代元素过程中，会lastRet值设置为当前迭代的指针
            if (lastRet < 0)  throw new IllegalStateException();
            
            // 1、检查是否协同修改，比较modCount != expectedModCount
            checkForComodification();

            try {
                // 1、迭代器删除元素，是调用了ArrayList的删除方法；2、ArrayListy删除方法，会增加modCount的值
                ArrayList.this.remove(lastRet);
                
                // 将迭代指针的值恢复到当前指针上
                cursor = lastRet;
                
                // lastRet恢复到 -1 状态
                lastRet = -1;
                
                // 1、同步ArrayList最新的modCount值 = expectedModCount
                expectedModCount = modCount;
            } catch (IndexOutOfBoundsException ex) {
                throw new ConcurrentModificationException();
            }
        }



        /**
         * 1、遍历elementData数组的元素，作为入参传给Consumer函数处理
         *
         * @return
         */
        @Override
        @SuppressWarnings("unchecked")
        public void forEachRemaining(Consumer<? super E> consumer) {
            Objects.requireNonNull(consumer);
            final int size = ArrayList.this.size;
            int i = cursor;
            if (i >= size) {
                return;
            }
            final Object[] elementData = ArrayList.this.elementData;
            if (i >= elementData.length) {
                throw new ConcurrentModificationException();
            }
            while (i != size && modCount == expectedModCount) {
                consumer.accept((E) elementData[i++]);
            }
            
            cursor = i;
            lastRet = i - 1;
            checkForComodification();
        }
        
        
        final void checkForComodification() {
            // 1、检查是否协同修改
            if (modCount != expectedModCount)
                throw new ConcurrentModificationException();
        }
    }
}
```

## 迭代器Itr常见问题

```java 
/**
 * @projectName: frame
 * @packageName: com.alibaba.frame.jvm
 * @className: ArrayAddressDemo
 * @description:
 * @author: admin
 * @date: 2023-07-11
 * @version: 1.0
 */
public class ArrayAddressDemo {
    public static void main(String[] args) throws Exception {
        
        // List<Object> objects = new ArrayList<>(2);
        // objects.add(100);
        // objects.add(200);
        // objects.add(300); // 第一次扩容，扩容后数组长度为3，size为3
        // objects.add(400); // size + 1又大于数组长度3，又要发生扩容来存放新元素400，扩容后3 * 1.5 为数组长度4（ 结果为4，浮点数向下取整）
        // objects.add(500); // size + 1又大于数组长度4，又要发生扩容来存放新元素500;，扩容后 4 * 1.5为数组长度6，此时存放元素后size数量为5
        // objects.add(600); // size + 1不大于数组长度6，不发生扩容，此时存放元素数量size为6
        // objects.add(700); // size + 1大于数组长度6，触发扩容机制，6 * 1.5为新数组长度9，此时存放元素数量为7
        // objects.add(800); // size + 1不大于数组长度9，不触发扩容机制，此时存放元素数量为8
        // objects.add(900); // size + 1不大于数组长度9，不发生扩容，此时存放元素数量size为9

        // 通过现象看出如果构造方法设置的容量小，频繁增加元素，会频繁出发扩容机制，以上设置初始容量2，添加9个元素扩容了4次，每次扩容就是数组的复制耗时操作

        // int i = (int) (3 * 1.5); // 结果为4，浮点数向下取整
        // System.out.println("i = " + i);


        List<String> elementData = new ArrayList<>(10);
        elementData.add("aaa");
        elementData.add("bbb");
        elementData.add("ccc");
        elementData.add("ddd");
        elementData.add("");
        elementData.add("");
        elementData.add("");

        // 获取迭代器，迭代元素时，调用迭代器的删除方法删除元素
        ArrayAddressDemo.iteratorAndRemove(elementData);

        // 获取迭代器，迭代元素时，调用集合的删除方法删除元素
        ArrayAddressDemo.iteratorAndListRemove(elementData);
    }
    
    
    
	/**
	 * 获取迭代器，迭代元素时，调用集合的删除方法删除元素
	 *
	 * @param elementData
	 */
	public static void iteratorAndListRemove(List<String> elementData) {
		// 1、用迭代器遍历集合元素时，用集合的删除方法删除元素，会报并发修改异常ConcurrentModificationException
		// 2、调用集合的删除方法操作，会对modCount的值+1，但不会同步给Iterator中的成员变量expectedModCount
		// 3、在调用Iterator.next()方法，迭代元素时，会先校验expectedModCount == modCount；在第2步操作，modCount的新值没有同步expectedModCount，导致不一样报错
		Iterator<String> iterator = elementData.iterator();
		while (iterator.hasNext()) {
			String item = iterator.next();
			elementData.remove(item);
		}
	}
	
	

	/**
	 * 获取迭代器，迭代元素时，调用迭代器的删除方法删除元素
	 *
	 * @param elementData
	 */
	public static void iteratorAndRemove(List<String> elementData) {
		// 获取集合的迭代器，遍历迭代器，调用迭代器的删除方法，删除集合中的元素
		Iterator<String> iterator = elementData.iterator();

		while (iterator.hasNext()) {
			// 在调用Iterator.next()方法，迭代元素时，会先校验expectedModCount == modCount
			iterator.next();
			// 迭代器的remove()实现，实际上调用集合的删除方法删除元素；在删除元素后，同步ArrayList最新的modCount值 = expectedModCount
			// 所以再下一次迭代元素时，校验expectedModCount == modCount不会报错
			iterator.remove();
		}
	}
}
```

# ArrayList的迭代器listIterator()

## 迭代器ListItr常见方法

```java 
public class ArrayList<E> extends AbstractList<E> implements List<E>, RandomAccess, Cloneable, java.io.Serializable {


    /**
     * 返回迭代器ListItr
     *
     * @see #listIterator(int)
     */
    public ListIterator<E> listIterator() {
        // 迭代器指针的起始位置
        return new ListItr(0);
    }


    /**
     *  返回迭代器ListItr
     *
     * @throws IndexOutOfBoundsException {@inheritDoc}
     */
    public ListIterator<E> listIterator(int index) {
        if (index < 0 || index > size) throw new IndexOutOfBoundsException("Index: "+index);
        
        // 迭代器指针的起始位置
        return new ListItr(index);
    }
    
    
    
    /**
     * ListItr迭代器：ArrayList内部类，同时继承了Itr的功能，扩展了set，add功能
     *
     * @return
     */
    private class ListItr extends Itr implements ListIterator<E> {
        ListItr(int index) {
            super();
            // 迭代器指针的起始位置，cursor是父类Itr的成员变量
            cursor = index;
        }

        public boolean hasPrevious() {
            return cursor != 0;
        }

        public int nextIndex() {
            return cursor;
        }

        public int previousIndex() {
            return cursor - 1;
        }

        @SuppressWarnings("unchecked")
        public E previous() {
            checkForComodification();
            int i = cursor - 1;
            if (i < 0)
                throw new NoSuchElementException();
            Object[] elementData = ArrayList.this.elementData;
            if (i >= elementData.length) throw new ConcurrentModificationException();
            cursor = i;
            return (E) elementData[lastRet = i];
        }
        
        

        /**
         *  迭代器，迭代元素过程中，根据迭代器指针位置，设置新值
         *
         */
        public void set(E e) {
            if (lastRet < 0) throw new IllegalStateException();
            
             // 1、检查是否协同修改，比较modCount != expectedModCount
            checkForComodification();

            try {
                // 调用ArrayList的方法设置新值
                ArrayList.this.set(lastRet, e);
            } catch (IndexOutOfBoundsException ex) {
                throw new ConcurrentModificationException();
            }
        }



        /**
         *  迭代器，迭代元素过程中，根据迭代器指针位置，新增元素
         *
         */
        public void add(E e) {
        
            // 1、检查是否协同修改，比较modCount != expectedModCount
            checkForComodification();

            try {
                int i = cursor;
                
                // 调用ArrayList的方法新增元素
                ArrayList.this.add(i, e);
                
                // 父类迭代器指针 + 1
                cursor = i + 1;
                
                // 父类lastRet恢复到 -1 状态
                lastRet = -1;
                
                
                // 1、同步ArrayList最新的modCount值 = expectedModCount
                expectedModCount = modCount;
            } catch (IndexOutOfBoundsException ex) {
                throw new ConcurrentModificationException();
            }
        }
    }
}
```

## 迭代器ListItr常见问题

```java 
package com.alibaba.frame.jvm;
import java.util.ArrayList;
import java.util.Iterator;
import java.util.List;
import java.util.ListIterator;
import org.springframework.util.StopWatch;

/**
 * @projectName: frame
 * @packageName: com.alibaba.frame.jvm
 * @className: ArrayAddressDemo
 * @description: 
 * @author: admin
 * @date: 2023-07-11
 * @version: 1.0
 */
public class ArrayAddressDemo {
	public static void main(String[] args) throws Exception {


		// List<Object> objects = new ArrayList<>(2);
		// objects.add(100);
		// objects.add(200);
		// objects.add(300); // 第一次扩容，扩容后数组长度为3，size为3
		// objects.add(400); // size + 1又大于数组长度3，又要发生扩容来存放新元素400，扩容后3 * 1.5 为数组长度4（ 结果为4，浮点数向下取整）
		// objects.add(500); // size + 1又大于数组长度4，又要发生扩容来存放新元素500;，扩容后 4 * 1.5为数组长度6，此时存放元素后size数量为5
		// objects.add(600); // size + 1不大于数组长度6，不发生扩容，此时存放元素数量size为6
		// objects.add(700); // size + 1大于数组长度6，触发扩容机制，6 * 1.5为新数组长度9，此时存放元素数量为7
		// objects.add(800); // size + 1不大于数组长度9，不触发扩容机制，此时存放元素数量为8
		// objects.add(900); // size + 1不大于数组长度9，不发生扩容，此时存放元素数量size为9

		// 通过现象看出如果构造方法设置的容量小，频繁增加元素，会频繁出发扩容机制，以上设置初始容量2，添加9个元素扩容了4次，每次扩容就是数组的复制耗时操作

		// int i = (int) (3 * 1.5); // 结果为4，浮点数向下取整
		// System.out.println("i = " + i);


		List<String> elementData = new ArrayList<>(10);
		elementData.add("aaa");
		elementData.add("bbb");
		elementData.add("ccc");
		elementData.add("ddd");
		elementData.add("");
		elementData.add("");
		elementData.add("");

		// 获取迭代器，迭代元素时，设置新值
		ArrayAddressDemo.listItrAndSet(elementData);
		System.out.println(elementData);
	}

	/**
	 * 1、获取迭代器，迭代元素时，设置新值
	 * 2、迭代器过程中，调用List删除方法删除元素，会报错
	 *
	 * @param elementData
	 */
	public static void listItrAndSet(List<String> elementData) {
		ListIterator<String> listIterator = elementData.listIterator();

		while (listIterator.hasNext()) {
			String item = listIterator.next();
			if ("ccc".equals(item)) {
				listIterator.set("ccc_new");
			}
		}
	}
}
```

# ArrayList截取subList()

## SubList集合常见方法

```java 
public class ArrayList<E> extends AbstractList<E> implements List<E>, RandomAccess, Cloneable, java.io.Serializable {


    /**
     * 截取集合，返回新集合SubList
     *
     * @throws IndexOutOfBoundsException
     * @throws IllegalArgumentException 
     */
    public List<E> subList(int fromIndex, int toIndex) {
        
        // 索引范围校验
        subListRangeCheck(fromIndex, toIndex, size);
        
        // 返回新集合SubList
        return new SubList(this, 0, fromIndex, toIndex);
    }

    
    /**
     * SubList：ArrayList内部类
     *
     */
    private class SubList extends AbstractList<E> implements RandomAccess {
        private final AbstractList<E> parent; // 被截取的目标集合
        private final int parentOffset; // 目标集合被截取的起始索引位置
        private final int offset; // 要截取的起始位置
        int size; // 要截取的长度
     
     
        
        SubList(AbstractList<E> parent, int offset, int fromIndex, int toIndex) { // offset：默认值 0
            this.parent = parent;  // 被截取的目标集合
            this.parentOffset = fromIndex;  // 目标集合被截取的起始索引位置
            this.offset = offset + fromIndex; // 要截取的起始位置
            this.size = toIndex - fromIndex; // 要截取的长度
            this.modCount = ArrayList.this.modCount;  1、同步被截取集合的modCount = SubList集合的modCount
        }
        
        
        /**
         * 根据索引，设置新值
         *
         */
        public E set(int index, E e) {
            // 1、校验子集合的索引，不能<0 或者超过子集合长度
            rangeCheck(index);
            
            // 1、检查是否协同修改，比较modCount != expectedModCount
            checkForComodification();
            
            // 1、获取子集合起始位置 + index的元素；2、实际上取的还是ArrayList.elementData数组的元素；3、这里的offset + index，像是SQL里的limit offset, index分页
            E oldValue = ArrayList.this.elementData(offset + index);
            
            // 1、设置的还是ArrayList.elementData数组的元素；2、SubList不存储元素，调用SubList的修改方法会影响ArrayList集合的值
            ArrayList.this.elementData[offset + index] = e;
            
            return oldValue;
        }
        
        
        /**
         * 查询元素
         *
         */
        public E get(int index) {
            // 1、校验子集合的索引，不能<0 或者超过子集合长度
            rangeCheck(index);
            
            // 1、检查是否协同修改，比较modCount != expectedModCount
            checkForComodification();
            
            // 1、获取子集合起始位置 + index的元素；2、实际上取的还是ArrayList.elementData数组的元素；3、这里的offset + index，像是SQL里的limit offset, index分页
            return ArrayList.this.elementData(offset + index);
        }
        
        
        /**
         * 查询SubList集合的大小
         *
         */
        public int size() {
            // 1、检查是否协同修改，比较modCount != expectedModCount
            checkForComodification();
            
            return this.size;
        }
        
        
        
        /**
         * 在指定索引位置，添加元素
         *
         */
        public void add(int index, E e) {
        
            // 校验索引位置范围
            rangeCheckForAdd(index);
            
            // 检查是否协同修改，比较modCount != expectedModCount
            checkForComodification();
            
            // 添加了ArrayList集合的元素，在抽象上看是SubList增加了元素；实际上是SubList的截取范围 + 1
            parent.add(parentOffset + index, e);
            
            // 同步ArrayList.modCount = SubList.modCount
            this.modCount = parent.modCount;
            
            // SubList的截取范围 + 1
            this.size++;
        }
        
        
        
        /**
         * 在指定索引位置，删除元素
         *
         */
        public E remove(int index) {
            // 校验索引位置
            rangeCheck(index);
            
            // 检查是否协同修改，比较modCount != expectedModCount
            checkForComodification();
            
            // ArrayList集合删除元素，在抽象上看是SubList删除了元素；实际上是SubList的截取范围 - 1
            E result = parent.remove(parentOffset + index);
            
            // 同步ArrayList.modCount = SubList.modCount
            this.modCount = parent.modCount;
            
            // SubList的截取范围 - 1
            this.size--;
            
            return result;
        }
        
        
        
        /**
         * 范围删除
         *
         */
        protected void removeRange(int fromIndex, int toIndex) {
            // 检查是否协同修改，比较modCount != expectedModCount
            checkForComodification();
            
            // 1、ArrayList在一定范围里删除了元素，抽象上看是SubList删除了自己的范围元素，实际上截取的范围减少了
            parent.removeRange(parentOffset + fromIndex, parentOffset + toIndex);
            
            // 同步ArrayList.modCount = SubList.modCount
            this.modCount = parent.modCount;
            
            //  SubList的截取范围缩小
            this.size -= toIndex - fromIndex;
        }
    }
}
```

## SubList集合常见问题

```java 
package com.alibaba.frame.jvm;
import java.util.ArrayList;
import java.util.Iterator;
import java.util.List;
import java.util.ListIterator;

/**
 * @projectName: frame
 * @packageName: com.alibaba.frame.jvm
 * @className: ArrayAddressDemo
 * @description: 
 * @author: admin
 * @date: 2023-07-11
 * @version: 1.0
 */
public class ArrayAddressDemo {
	public static void main(String[] args) throws Exception {

		// List<Object> objects = new ArrayList<>(2);
		// objects.add(100);
		// objects.add(200);
		// objects.add(300); // 第一次扩容，扩容后数组长度为3，size为3
		// objects.add(400); // size + 1又大于数组长度3，又要发生扩容来存放新元素400，扩容后3 * 1.5 为数组长度4（ 结果为4，浮点数向下取整）
		// objects.add(500); // size + 1又大于数组长度4，又要发生扩容来存放新元素500;，扩容后 4 * 1.5为数组长度6，此时存放元素后size数量为5
		// objects.add(600); // size + 1不大于数组长度6，不发生扩容，此时存放元素数量size为6
		// objects.add(700); // size + 1大于数组长度6，触发扩容机制，6 * 1.5为新数组长度9，此时存放元素数量为7
		// objects.add(800); // size + 1不大于数组长度9，不触发扩容机制，此时存放元素数量为8
		// objects.add(900); // size + 1不大于数组长度9，不发生扩容，此时存放元素数量size为9

		// 通过现象看出如果构造方法设置的容量小，频繁增加元素，会频繁出发扩容机制，以上设置初始容量2，添加9个元素扩容了4次，每次扩容就是数组的复制耗时操作

		// int i = (int) (3 * 1.5); // 结果为4，浮点数向下取整
		// System.out.println("i = " + i);


		 List<String> elementData = new ArrayList<>(10);
		 elementData.add("aaa");
		 elementData.add("bbb");
		 elementData.add("ccc");
		 elementData.add("ddd");
		 elementData.add("");
		 elementData.add("");
		 elementData.add("");

 		 // 获取子集合
		 ArrayAddressDemo.subList(elementData);
	}



	/**
	 * 截取集合，获取子集合
	 * @param elementData
	 */
	public static void subList(List<String> elementData) {

		// 获取子集合，会同步ArrayList.modCount = SubList.modCount
		List<String> subList = elementData.subList(1, 3);

         
        // 第 2 步：
        // ArrayList的迭代器，迭代元素的过程中删除元素，ArrayList.modCount的值会修改
		// Iterator<String> iterator = elementData.iterator();
		// while (iterator.hasNext()) {
		// iterator.next();
		// iterator.remove();
		// }
		
		// 第 2 步：
		// elementData.remove(0); // ArrayList删除元素时，modCount的值会修改
		// elementData.add(""); // ArrayList新增元素时，modCount的值会修改
		// ArrayList其他修改数据的方法，都会触发modCount的值修改


        // subList在set元素前，会比较modCount != expectedModCount，此时 第 2 步操作会影响ArrayList.modCount的值，所以会报并发异常ConcurrentModificationException
        // 结论：在获取集合subList之后，迭代ArrayList集合时切记不要删除元素，否则后续的subList的修改元素方法会报错
		subList.set(1, "aaa_new");
	}
}
```
