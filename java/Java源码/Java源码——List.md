# Java源码——List

public interface List<E> extends Collection<E>  接口



List 方法区：

#### int size();

```java
//获取List的长度
int size();
```

#### boolean isEmpty();

```java
//判断数组是否为空
boolean isEmpty();
```

#### boolean contains(Object o);

```Java
//判断容器中是否存在这个元素
boolean contains(Object o);
```

#### Iterator<E> iterator();

```Java
//返回List的迭代
Iterator<E> iterator();
```

#### Object[] toArray();

```Java
//List转化为Object数组
Object[] toArray();
```

#### <T> T[] toArray(T[] a);

```Java
//将List中的元素转换成T[]数组返回
<T> T[] toArray(T[] a);
```

#### boolean add(E e);

```Java
//添加元素
boolean add(E e);
```

#### boolean remove(Object o);

```Java
//移除元素
boolean remove(Object o);
```

#### boolean containsAll(Collection<?> c);

```Java
//判断是否包含C中的所有元素
boolean containsAll(Collection<?> c);
```

#### boolean addAll(int index, Collection<? extends E> c);

```Java
//在List数组index位置中添加C中的所有元素
boolean addAll(int index, Collection<? extends E> c);
```

#### boolean removeAll(Collection<?> c);

```Java
//移除List中所有在C中出现的元素
boolean removeAll(Collection<?> c);	
```

#### boolean retainAll(Collection<?> c);

```Java
//移除List中所有没有在C中出现的元素
boolean retainAll(Collection<?> c);
```

#### void clear();

```Java
//清空数组
void clear();
```

#### boolean equals(Object o);

```Java
//判断List是否和Object相等
boolean equals(Object o);
```

#### int hashCode();

```java
//放回List的hashCode
int hashCode();
```

#### E get(int index);

```java
//获取List中下标为index的元素
E get(int index);
```

#### E set(int index, E element);

```Java
//将元素element插入List的index位置中
E set(int index, E element);
```

#### E remove(int index);

```Java
//移除List中index位置的元素
E remove(int index);
```

#### int indexOf(Object o);

```Java
//获取元素o在List中第一次出现的位置
int indexOf(Object o);
```

#### int lastIndexOf(Object o);

```Java
//获取元素o在List中最后一次出现的位置
int lastIndexOf(Object o);
```

#### ListIterator<E> listIterator();

```Java
//返回List的List迭代
 ListIterator<E> listIterator();
```

#### ListIterator<E> listIterator(int index);

```Java
//返回List数组从index开始的list迭代
ListIterator<E> listIterator(int index);
```

#### List<E> subList(int fromIndex, int toIndex);

```Java
//保留list中位置从fromIndex到toIndex的元素
List<E> subList(int fromIndex, int toIndex);
```