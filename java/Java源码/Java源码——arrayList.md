# Java知识点——arrayList

arrayList 非线程安全的

#### add(E e)  扩容机制

```java
 public boolean add(E e) 
 {
   		//确保数组容量充足
        ensureCapacityInternal(size + 1);  // Increments modCount!!
        elementData[size++] = e;
        return true;
}
```



```java
private void ensureCapacityInternal(int minCapacity)
{		//原数组为空
        if (elementData == EMPTY_ELEMENTDATA) {
          	//DEFAULT_CAPACITY=10
            minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
        }
        ensureExplicitCapacity(minCapacity);
}
```

原数组为空minCapacity=10，若不为空则minCapacity=size+1；

```java
private void ensureExplicitCapacity(int minCapacity) 
{
  		//数组被操作的次数
        modCount++;
        // 如果最小容量大于数组的长度 则进行扩容
        if (minCapacity - elementData.length > 0)
            grow(minCapacity);
}
```



```Java
private void grow(int minCapacity) 
{
        // overflow-conscious code
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        // minCapacity is usually close to size, so this is a win:
        elementData = Arrays.copyOf(elementData, newCapacity);
}
```

newCapacity = oldCapacity + (oldCapacity >> 1) 新的数组大小是原数组大小的1.5倍。若新数组大小小于minCapacity，新数组大小就等于minCapacity

```Java
private static int hugeCapacity(int minCapacity)
{
        if (minCapacity < 0) // overflow
            throw new OutOfMemoryError();
        return (minCapacity > MAX_ARRAY_SIZE) ?
            Integer.MAX_VALUE :
            MAX_ARRAY_SIZE;
}	
```

若数组大小超过int的最大值则抛出异常



#### add(int index, E element) 插入元素

```Java
public void add(int index, E element) 
{
  		//检查index是否越界
        rangeCheckForAdd(index);
		//确保数组大小足够
        ensureCapacityInternal(size + 1);  // Increments modCount!!
  		//将数组从index到数组结尾的元素向后移一位
        System.arraycopy(elementData, index, elementData, index + 1,
                         size - index);
        elementData[index] = element;
        size++;
}
```



#### remove(int index) 移除元素

```Java
public E remove(int index) 
{
  		//检查元素是否越界
        rangeCheck(index);
		//数组被操作的次数
        modCount++;
        E oldValue = elementData(index);
		//要移动元素的个数
        int numMoved = size - index - 1;
        if (numMoved > 0)
          	//将数组index+1到最后的元素前移一位
            System.arraycopy(elementData, index+1, elementData, index,
                             numMoved);
  		//元素最后一位置空
        elementData[--size] = null; // clear to let GC do its work
        return oldValue;
}
```