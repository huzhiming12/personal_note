# Java源码——hashcode



​	Java中的hashcode()方法返回的是该对象的hash值，一个对象每次调用hashCode()方法时返回的值都是相同的。

​	如果两个对象通过equals（Object o）比较相等，那么 这两个对象的hashCode()方法返回的值也是相等的 ；反过来如果两个对象的hashcode()值相等，但是这个对象调用equals()方法却不一定是相等的。

​	Set类中可以把每个hashCode与一个容器相对应，同时每个容器可以存放多个对象。由于Set的特性是存放不同的对象，而且是无序的。当添加对象时，通过对象的hashcode查找 相应的容器，然后再与这个容器中的每个对象比较，如果相同则不存放，不相同则放入该容器中。这样做的好处是不用和Set中的每个容器进行比较，极大的提高了效率。



```Java
public V put(K key, V value) {
        if (key == null)
            return putForNullKey(value);
        int hash = hash(key.hashCode());
        int i = indexFor(hash, table.length);
        for (Entry<K,V> e = table[i]; e != null; e = e.next) {
            Object k;
            if (e.hash == hash && ((k = e.key) == key || key.equals(k))) {
                V oldValue = e.value;
                e.value = value;
                e.recordAccess(this);
                return oldValue;
            }
        }
 
        modCount++;
        addEntry(hash, key, value, i);
        return null;
    }
```

