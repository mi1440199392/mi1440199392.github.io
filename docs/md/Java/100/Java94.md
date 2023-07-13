# 前言

<font face="幼圆">

> HashMap简单实现

</font>

# 定义Map<K, V>， Entry<K, V>

```java 
interface Map<K, V> {

    void put(K key, V value);

    V get(K key);

    int size();


    interface Entry<K, V> {
        K getKey();

        V getValue();
    }
}
```

# HashMap<K, V>实现Map<K, V>，Entry<K, V>实现Map.Entry<K, V>

```java 
class HashMap<K, V> implements Map<K, V> {
    Entry<K, V>[] table;
    int size;
    int modCount;

    public HashMap() {
        this.table = new Entry[16];
    }


    @Override
    public void put(K key, V value) {
        // 根据key计算除索引位置
        int index = hash(key);

        Entry<K, V> entry = table[index];
        if (entry == null) {
            // 如果当前索引位置为null，就新建一个Entry对象放在该索引位置，链表的next节点设置null
            table[index] = new Entry<>(key, value, index, null);
            size++; // 元素数量+1
            modCount++; // 修改次数+1
        } else {
            // 如果当前索引位置有值，就新建一个Entry对象放在该索引位置，将新Entry对象的next节点指向原entry对象
            table[index] = new Entry<>(key, value, index, entry);
            size++; // 元素数量+1
            modCount++; // 修改次数+1
        }
    }


    /**
     * 计算hashcode 并进行取模
     * @param key
     * @return
     */
    private int hash(K key) {
        // table长度为16，取模16，保证索引位置index在 (0 -15)的范围内
        return Math.abs(key.hashCode() % (16 - 1));
    }

    @Override
    public V get(K key) {
        // 根据key计算除索引位置
        int index = hash(key);

        // 如果该索引位置为null，返回
        Entry<K, V> entry = table[index];
        if (entry == null) {
            return null;
        }

        // 如果索引位置有值，则递归链表里的entry对象节点，判断当前key是否和entry对象节点的key相等，相等则返回
        return findEntry(entry, key).getValue();
    }

    /**
     * 递归链表里的entry对象节点，判断当前key是否和entry对象节点的key相等，相等则返回
     * @param entry
     * @param key
     * @return
     */
    Entry<K, V> findEntry(Entry<K, V> entry, K key) {
        // 判断当前key是否和entry对象节点的key相等，相等则返回
        if (key == entry.getKey() || key.equals(entry.getKey())) {
            return entry;
        }

        // 如果不相等，继续判断entry对象的next节点，比较当前key是否和next节点的key相等，相等则返回
        Entry<K, V> next = entry.next;
        return findEntry(next, key);
    }

    @Override
    public int size() {
        return size;
    }

    
    class Entry<K, V> implements Map.Entry<K, V> {
        K key; // key值
        V value; // value值
        int hash; // hashcode
        Entry<K, V> next; // 链表的下个节点

        public Entry(K key, V value, int hash, Entry<K, V> next) {
            this.key = key;
            this.value = value;
            this.hash = hash;
            this.next = next;
        }

        @Override
        public K getKey() {
            return key;
        }

        @Override
        public V getValue() {
            return value;
        }
    }
}
```

# 自定义HashMap测试

```java 
package com.alibaba.frame.jvm;

/**
 * @projectName: frame
 * @packageName: com.alibaba.frame.jvm
 * @className: HashMapDemo
 * @description: HashMap简单实现
 * @author: admin
 * @date: 2023-07-13
 * @version: 1.0
 */
public class HashMapDemo {
	public static void main(String[] args) {

		Map<String, String> hashMap = new HashMap<>();
		hashMap.put("张三", "张三");
		hashMap.put("李四", "李四");
		hashMap.put("王五", "王五");
		hashMap.put("赵六", "赵六");

		System.out.println(hashMap.get("张三"));
		System.out.println(hashMap.get("李四"));
		System.out.println(hashMap.get("王五"));
		System.out.println(hashMap.get("赵六"));
		System.out.println(hashMap.size());
	}
}
```

# 控制台打印

```text
张三
李四
王五
赵六
4
```