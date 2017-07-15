# Java源码——Stack

Stack 线程安全的。先进后出机制，

Stack<E> extends Vector<E>{} Stack 继承至vector

#### public E push(E item) 入栈

```Java
//添加元素
public E push(E item) 
{
  	addElement(item);
    return item;
}		
```



```Java
//Vector
public synchronized void addElement(E obj) 
{	//数组修改次数
     modCount++;
  	//elementCount 数组元素的个数
     ensureCapacityHelper(elementCount + 1);
     elementData[elementCount++] = obj;
}
```

​	synchronized 给该方法区加上同步锁，两个线程同时执行该代码块时，只有一个线程可以执行该代码块，另一个线程必须等待前一个线程执行完成之后才能执行。

```Java
//Vector
private void ensureCapacityHelper(int minCapacity) 
{
     // overflow-conscious code
     if (minCapacity - elementData.length > 0)
         grow(minCapacity);
}
```



```Java
//Vector
private void grow(int minCapacity) 
{
        // overflow-conscious code
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + ((capacityIncrement > 0) ?
                                         capacityIncrement : oldCapacity);
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        elementData = Arrays.copyOf(elementData, newCapacity);
}
```

​	capacityIncrement 如果为0，则stack的容量成倍增长newCapacity = oldCapacity + oldCapacity ；否则newCapacity = oldCapacity + capacityIncrement



#### public synchronized E pop() 出栈

```Java
public synchronized E pop() 
{
     E obj;
     int len = size();
  	//取出栈顶元素
     obj = peek();
  	//移除栈顶元素
     removeElementAt(len - 1);
     return obj;
}
```



```Java
//获取栈顶元素 但不移除栈顶元素
public synchronized E peek() 
{
     int len = size();
     if (len == 0)
        throw new EmptyStackException();
     return elementAt(len - 1);
}
```



```Java
//Vector
public synchronized void removeElementAt(int index) 
{
        modCount++;
        if (index >= elementCount) {
            throw new ArrayIndexOutOfBoundsException(index + " >= " +
                                                     elementCount);
        }
        else if (index < 0) {
            throw new ArrayIndexOutOfBoundsException(index);
        }
        int j = elementCount - index - 1;
        if (j > 0) {
          	//将index以后的元素整体往前移一位
            System.arraycopy(elementData, index + 1, elementData, index, j);
        }
        elementCount--;
        elementData[elementCount] = null; /* to let gc do its work */
}
```


#### public boolean empty() 判断元素是否为空

```Java
public boolean empty() {
        return size() == 0;
    }	
```



#### public synchronized int search(Object o) 元素搜索

```Java
public synchronized int search(Object o) 
{
        int i = lastIndexOf(o);
        if (i >= 0) {
            return size() - i;
        }
        return -1;
}
```



```Java
//Vector
public synchronized int lastIndexOf(Object o) 
{
        return lastIndexOf(o, elementCount-1);
}
```



```Java
//Vector
public synchronized int lastIndexOf(Object o, int index) 
{
        if (index >= elementCount)
            throw new IndexOutOfBoundsException(index + " >= "+ elementCount);

        if (o == null) {
            for (int i = index; i >= 0; i--)
                if (elementData[i]==null)
                    return i;
        } else {
            for (int i = index; i >= 0; i--)
                if (o.equals(elementData[i]))
                    return i;
        }
        return -1;
}
```



