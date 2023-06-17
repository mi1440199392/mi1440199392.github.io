# 前言

<font face="幼圆">

> Map接口解析

</font>

```java 
package java.util;
import java.util.function.BiConsumer;
import java.util.function.BiFunction;
import java.util.function.Function;
import java.io.Serializable;

public interface Map<K,V> {
    
    /**
     * 返回Map的大小
     *
     * @return 
     */
    int size();

    /**
     * Map是否为空
     *
     * @return
     */
    boolean isEmpty();

    /**
     * 是否包含当前key
     *
     * @param
     * @return
     */
    boolean containsKey(Object key);

    /**
     * 是否包含当前value值
     *
     * @param
     * @return
     */
    boolean containsValue(Object value);

    /**
     * 根据key，获取value值
     *
     * @param
     * @return
     */
    V get(Object key);

    /**
     * 添加元素
     *
     * @return
     */
    V put(K key, V value);

    /**
     * 根据key，删除元素
     *
     * @param
     * @return
     */
    V remove(Object key);

    /**
     * 添加Map集合到当前Map
     *
     * @param
     * @throws UnsupportedOperationException if the <tt>putAll</tt> operation is not supported by this map
     * @throws ClassCastException if the class of a key or value in the specified map prevents it from being stored in this map
     * @throws NullPointerException if the specified map is null, or if this map does not permit null keys or values, and the specified map contains null keys or values
     * @throws IllegalArgumentException if some property of a key or value in the specified map prevents it from being stored in this map
     */
    void putAll(Map<? extends K, ? extends V> m);

    /**
     * 清空Map
     */
    void clear();

    /**
     * 返回Map的key集合
     *
     * @return
     */
    Set<K> keySet();

    /**
     * 返回value值的集合
     *
     * @return
     */
    Collection<V> values();

    /**
     * 返回Entry<K, V>对象的集合
     *
     * @return
     */
    Set<Map.Entry<K, V>> entrySet();

    /**
     * Entry<K,V>对象
     *
     */
    interface Entry<K,V> {
        /**
         * 返回Entry对象的key
         *
         * @return
         * @throws IllegalStateException implementations may, but are not required to, throw this exception if the entry has been removed from the backing map.
         */
        K getKey();

        /**
         * 返回Entry对象的value值
         *
         * @return
         * @throws IllegalStateException implementations may, but are not required to, throw this exception if the entry has been removed from the backing map.
         */
        V getValue();

        /**
         * 设置Entry对象的value值
         *
         * @param
         * @return
         * @throws UnsupportedOperationException if the <tt>put</tt> operation is not supported by the backing map
         * @throws ClassCastException if the class of the specified value prevents it from being stored in the backing map
         * @throws NullPointerException if the backing map does not permit null values, and the specified value is null
         * @throws IllegalArgumentException if some property of this value prevents it from being stored in the backing map
         * @throws IllegalStateException implementations may, but are not required to, throw this exception if the entry has been removed from the backing map.
         */
        V setValue(V value);

        /**
         * 比较是否相等
         *
         * @param
         * @return
         */
        boolean equals(Object o);

        /**
         * Entry对象的hashCode
         *
         * @return
         */
        int hashCode();

        /**
         * 返回一个比较器 (比较key)
         *
         * @param
         * @param
         * @return
         */
        public static <K extends Comparable<? super K>, V> Comparator<Map.Entry<K,V>> comparingByKey() {
            return (Comparator<Map.Entry<K, V>> & Serializable)
                (c1, c2) -> c1.getKey().compareTo(c2.getKey());
        }

        /**
         * 返回一个比较器 (比较value)
         *
         * @param
         * @param
         * @return
         */
        public static <K, V extends Comparable<? super V>> Comparator<Map.Entry<K,V>> comparingByValue() {
            return (Comparator<Map.Entry<K, V>> & Serializable)
                (c1, c2) -> c1.getValue().compareTo(c2.getValue());
        }

        /**
         * 根据指定的比较器，比较key；返回一个新的比较器
         *
         * @param
         * @param
         * @param 
         * @return 
         */
        public static <K, V> Comparator<Map.Entry<K, V>> comparingByKey(Comparator<? super K> cmp) {
            Objects.requireNonNull(cmp);
            return (Comparator<Map.Entry<K, V>> & Serializable)
                (c1, c2) -> cmp.compare(c1.getKey(), c2.getKey());
        }

        /**
         * 根据指定的比较器，比较value；返回一个新的比较器
         *
         * @param
         * @param 
         * @param 
         * @return
         */
        public static <K, V> Comparator<Map.Entry<K, V>> comparingByValue(Comparator<? super V> cmp) {
            Objects.requireNonNull(cmp);
            return (Comparator<Map.Entry<K, V>> & Serializable)
                (c1, c2) -> cmp.compare(c1.getValue(), c2.getValue());
        }
    }


    /**
     * 比较Map是否相等
     *
     * @param
     * @return
     */
    boolean equals(Object o);

    /**
     * 返回Map的hashCode
     *
     * @return
     */
    int hashCode();

    /**
     * 根据key获取value值，如果value值不为空则返回，如果value值为空，则返回指定默认值
     *
     * @param
     * @param
     * @return 
     */
    default V getOrDefault(Object key, V defaultValue) {
        V v;
        return (((v = get(key)) != null) || containsKey(key))
            ? v
            : defaultValue;
    }

    /**
     * 遍历Entry对象，取得每一个Entry对象key、value；遍历时，指定匿名函数的accept(key,value)方法处理逻辑
     *
     * @param
     * @throws NullPointerException if the specified action is null
     * @throws ConcurrentModificationException if an entry is found to be removed during iteration
     */
    default void forEach(BiConsumer<? super K, ? super V> action) {
        Objects.requireNonNull(action);
        
        // 获取Entry对象集合，遍历集合
        for (Map.Entry<K, V> entry : entrySet()) {
            K k;
            V v;
            try {
                k = entry.getKey();
                v = entry.getValue();
            } catch(IllegalStateException ise) {
                throw new ConcurrentModificationException(ise);
            }
            
            // 获取Entry对象key、value，传参给匿名函数的accept(key,value)方法处理逻辑
            action.accept(k, v);
        }
    }

    /**
     * 遍历Entry对象，取得每一个Entry对象key、value；遍历时，指定匿名函数的apply(key,value)方法处理逻辑
     *
     * @param
     * @throws UnsupportedOperationException if the {@code set} operation is not supported by this map's entry set iterator.
     * @throws ClassCastException if the class of a replacement value prevents it from being stored in this map
     * @throws NullPointerException if the specified function is null, or the specified replacement value is null, and this map does not permit null values
     * @throws ClassCastException if a replacement value is of an inappropriate type for this map
     * @throws NullPointerException if function or a replacement value is null and this map does not permit null keys or values
     * @throws IllegalArgumentException if some property of a replacement value  prevents it from being stored in this map
     * @throws ConcurrentModificationException if an entry is found to be removed during iteration
     */
    default void replaceAll(BiFunction<? super K, ? super V, ? extends V> function) {
        Objects.requireNonNull(function);
        
        // 获取Entry对象集合，遍历集合
        for (Map.Entry<K, V> entry : entrySet()) {
            K k;
            V v;
            try {
                k = entry.getKey();
                v = entry.getValue();
            } catch(IllegalStateException ise) {
                throw new ConcurrentModificationException(ise);
            }
            
            // 获取Entry对象key、value，传参给匿名函数的apply(key,value)方法处理逻辑，返回value新值
            v = function.apply(k, v);

            try {
                // 设置value新值
                entry.setValue(v);
            } catch(IllegalStateException ise) {
                throw new ConcurrentModificationException(ise);
            }
        }
    }

    /**
     * 如果key不存在，则插入新元素
     *
     * @param
     * @param
     * @return
     * @throws UnsupportedOperationException if the {@code put} operation is not supported by this map
     * @throws ClassCastException if the key or value is of an inappropriate type for this map
     * @throws NullPointerException if the specified key or value is null and this map does not permit null keys or values
     * @throws IllegalArgumentException if some property of the specified key or value prevents it from being stored in this map
     */
    default V putIfAbsent(K key, V value) {
        V v = get(key);
        
        // key不存在
        if (v == null) {
            // 插入新元素
            v = put(key, value);
        }

        return v;
    }

    /**
     * 根据key、value删除元素，如果key不存在 或者 value新值和value旧值不匹配，则删除失败
     *
     * @param
     * @param
     * @return
     * @throws UnsupportedOperationException if the {@code remove} operation is not supported by this map
     * @throws ClassCastException if the key or value is of an inappropriate type for this map
     * @throws NullPointerException if the specified key or value is null and this map does not permit null keys or values
     */
    default boolean remove(Object key, Object value) {
        // 根据key，获取value值
        Object curValue = get(key);
        
        // 如果value旧值和value新值不相等 或者 key不存在，则删除失败
        if (!Objects.equals(curValue, value) || (curValue == null && !containsKey(key))) {
            return false;
        }
        
        // 删除成功
        remove(key);
        return true;
    }

    /**
     * 根据key、value替换新值，如果key不存在 或者 value旧值和oldValue值不匹配，则不替换value新值
     *
     * @param
     * @param
     * @param
     * @return
     * @throws UnsupportedOperationException if the {@code put} operation is not supported by this map
     * @throws ClassCastException if the class of a specified key or value prevents it from being stored in this map
     * @throws NullPointerException if a specified key or newValue is null and this map does not permit null keys or values
     * @throws NullPointerException if oldValue is null and this map does not  permit null values
     * @throws IllegalArgumentException if some property of a specified key  or value prevents it from being stored in this map
     */
    default boolean replace(K key, V oldValue, V newValue) {
        // 根据key，获取value值
        Object curValue = get(key);
        
        // 如果value旧值和oldValue值不匹配 或者 key不存在，则不替换value值
        if (!Objects.equals(curValue, oldValue) || (curValue == null && !containsKey(key))) {
            return false;
        }
        
        // 替换value值成功
        put(key, newValue);
        return true;
    }

    /**
     * 如果key存在，则替换value新值
     *
     * @param
     * @param
     * @return
     * @throws UnsupportedOperationException if the {@code put} operation  is not supported by this map
     * @throws ClassCastException if the class of the specified key or value  prevents it from being stored in this map
     * @throws NullPointerException if the specified key or value is null and this map does not permit null keys or values
     * @throws IllegalArgumentException if some property of the specified key or value prevents it from being stored in this map
     */
    default V replace(K key, V value) {
        V curValue;
        
        // 如果key存在，则替换value新值
        if (((curValue = get(key)) != null) || containsKey(key)) {
            curValue = put(key, value);
        }
        return curValue;
    }

    /**
     * value旧值等于空的时候，匿名函数Function根据key算出value新值，value新值不等于空则设置value新值
     *
     * @param
     * @param 
     * @return
     */
    default V computeIfAbsent(K key, Function<? super K, ? extends V> mappingFunction) {
        Objects.requireNonNull(mappingFunction);
        V v;
        
        // 如果value旧值等于空
        if ((v = get(key)) == null) {
            V newValue;
            
            // 匿名函数apply(key)方法，根据key算出value新值，value新值不等于空，则设置value新值
            if ((newValue = mappingFunction.apply(key)) != null) {
                put(key, newValue);
                return newValue;
            }
        }

        return v;
    }

    /**
     * 匿名函数apply(key, oldValue)，根据key、value旧值计算出value新值，如果value新值不等于空，则设置value新值
     *
     * @param 
     * @param 
     * @return
     */
    default V computeIfPresent(K key, BiFunction<? super K, ? super V, ? extends V> remappingFunction) {
        Objects.requireNonNull(remappingFunction);
        V oldValue;
        
        // 如果value旧值不等于空
        if ((oldValue = get(key)) != null) {
        
            // 匿名函数apply(key, oldValue)，根据key、value旧值计算出value新值，如果value新值不等于空，则设置value新值
            V newValue = remappingFunction.apply(key, oldValue);
            if (newValue != null) {
                put(key, newValue);
                return newValue;
            } else {
                
                // 如果计算出的value新值等于空，则删除该元素
                remove(key);
                return null;
            }
        } else {
            return null;
        }
    }

    /**
     * 匿名函数apply(key, oldValue)，根据key、value旧值计算出value新值，如果value新值不等于空，则设置value新值
     *
     * @param 
     * @param
     * @return
     */
    default V compute(K key, BiFunction<? super K, ? super V, ? extends V> remappingFunction) {
        Objects.requireNonNull(remappingFunction);
        
        // 根据key，获取value旧值
        V oldValue = get(key);

        // 匿名函数apply(key, oldValue)，根据key、value旧值计算出value新值
        V newValue = remappingFunction.apply(key, oldValue);
        
        // 如果value新值等于空，则删除该元素
        if (newValue == null) {
            if (oldValue != null || containsKey(key)) {
                remove(key);
                return null;
            } else {
                return null;
            }
        } else {
            
            // 如果value新值不等于空，则设置value新值
            put(key, newValue);
            return newValue;
        }
    }

    /**
     * 匿名函数apply(key, oldValue)计算出value新值，并更新value值
     *
     * @param
     * @return
     */
    default V merge(K key, V value,  BiFunction<? super V, ? super V, ? extends V> remappingFunction) {
        Objects.requireNonNull(remappingFunction);
        Objects.requireNonNull(value);
        
        // 根据key，获取value旧值
        V oldValue = get(key);
        
        // 匿名函数apply(key, oldValue)，根据指定value值、value旧值计算出value新值；如果value旧值等于空，value新值则取指定value值，否则取计算出的value新值
        V newValue = (oldValue == null) ? value : remappingFunction.apply(oldValue, value);
        
        // 如果value新值等于空，则删除该元素
        if(newValue == null) {
            remove(key);
        } else {
            // 否则设置value新值
            put(key, newValue);
        }
        return newValue;
    }
}
```
