# Java源码——LinkedList

public class LinkedList<E>extends AbstractSequentialList<E>

​					       implements List<E>, Deque<E>, Cloneable, java.io.Serializable

LinkedList是非线程安全的

```Java
private static class Node<E>
{      //结点信息
        E item;
        Node<E> next;
        Node<E> prev;

        Node(Node<E> prev, E element, Node<E> next) 
        {
            this.item = element;
            this.next = next;
            this.prev = prev;
        }
}
```

#### public E getFirst()  //获取第一个元素 

```Java
public E getFirst() 
{
        final Node<E> f = first;
        if (f == null)
            throw new NoSuchElementException();
        return f.item;
}
```

#### public E getLast()  //获取最后一个元素

```Java
public E getLast() 
{
        final Node<E> l = last;
        if (l == null)
            throw new NoSuchElementException();
        return l.item;
}
```

#### public E removeFirst()//移除第一个元素

```Java
public E removeFirst() 
{
        final Node<E> f = first;
        if (f == null)
            throw new NoSuchElementException();
        return unlinkFirst(f);
}
```

```Java
private E unlinkFirst(Node<E> f) 
{
        // assert f == first && f != null;
        final E element = f.item;
  		//next指向第二个元素
        final Node<E> next = f.next;
        f.item = null;
        f.next = null; // help GC
  		//指针first指向第二个元素
        first = next;
  		//如果第二个元素为空则List变为空，first和last都指向null;否则first->pre指向空
        if (next == null)
            last = null;
        else
            next.prev = null;
        size--;
        modCount++;
        return element;
}
```

#### public E removeLast()//移除List最后一个元素

```Java
public E removeLast() {
        final Node<E> l = last;
        if (l == null)
            throw new NoSuchElementException();
        return unlinkLast(l);
    }
```



```Java
private E unlinkLast(Node<E> l) 
{
        // assert l == last && l != null;
        final E element = l.item;
  		//pre指向倒数第二个元素
        final Node<E> prev = l.prev;
        l.item = null;
        l.prev = null; // help GC
  		//为指针向前移动一位
        last = prev;
  		//如果倒数第二个元素为空则List变为空，first和last都指向null;否则last->next指向空
        if (prev == null)
            first = null;
        else
            prev.next = null;
        size--;
        modCount++;
        return element;
}
```



#### public void addFirst(E e)//将元素添加到List队首

```Java
public void addFirst(E e) {
        linkFirst(e);
    }
```



```Java
private void linkFirst(E e) 
{
        final Node<E> f = first;
  		//新节点的前驱指向空，后继指向first
        final Node<E> newNode = new Node<>(null, e, f);
  		//队首指针指向新建的结点
        first = newNode;
  		//如果原先的队首为空，此时List为空，新添加的Node既是头结点也是尾结点；否则原头结点的前驱指向新		建结点
        if (f == null)
            last = newNode;
        else
            f.prev = newNode;
        size++;
        modCount++;
}
```



#### public void addLast(E e) //将元素e添加到队尾

```Java
public void addLast(E e) {
        linkLast(e);
    }
```

```Java
void linkLast(E e) 
{
        final Node<E> l = last;
  		//新节点的前驱指last，后继指向空
        final Node<E> newNode = new Node<>(l, e, null);
  		//last指针后移，指向新建的结点
        last = newNode;
        if (l == null)
            first = newNode;
        else
            l.next = newNode;
        size++;
        modCount++;
}
```



#### public boolean contains(Object o) //判断list是否包含元素o

```java
public boolean contains(Object o) {
        return indexOf(o) != -1;
    }
```



```java
public int indexOf(Object o) 
{
        int index = 0;
        if (o == null) {
            for (Node<E> x = first; x != null; x = x.next) {
                if (x.item == null)
                    return index;
                index++;
            }
        } else {
            for (Node<E> x = first; x != null; x = x.next) {
                if (o.equals(x.item))
                    return index;
                index++;
            }
        }
        return -1;
}
```



#### public boolean remove(Object o)//移除元素

```java
public boolean remove(Object o) {
        if (o == null) {
            for (Node<E> x = first; x != null; x = x.next) {
                if (x.item == null) {
                    unlink(x);
                    return true;
                }
            }
        } else {
            for (Node<E> x = first; x != null; x = x.next) {
                if (o.equals(x.item)) {
                    unlink(x);
                    return true;
                }
            }
        }
        return false;
    }
```



#### public boolean addAll(Collection<? extends E> c) //添加多个元素

```Java
 public boolean addAll(Collection<? extends E> c) {
        return addAll(size, c);
    }
```



```Java
public boolean addAll(int index, Collection<? extends E> c) {
        checkPositionIndex(index);

        Object[] a = c.toArray();
        int numNew = a.length;
        if (numNew == 0)
            return false;

        Node<E> pred, succ;
  		//尾部添加
        if (index == size) {
            succ = null;
            pred = last;
        } else {
          	//中间插入
            succ = node(index);
            pred = succ.prev;
        }
		//元素加入链表中
        for (Object o : a) {
            @SuppressWarnings("unchecked") E e = (E) o;
          	//新建结点的前驱指向pred
            Node<E> newNode = new Node<>(pred, e, null);
          	//若前驱为空，则新建的结点就是头结点
            if (pred == null)
                first = newNode;
            else
                pred.next = newNode;
            pred = newNode;
        }

        if (succ == null) {
            last = pred;
        } else {
            pred.next = succ;
            succ.prev = pred;
        }

        size += numNew;
        modCount++;
        return true;
    }
```



```Java
//返回位置为index的元素
Node<E> node(int index)
{
		//判断index离头结点还是尾结点更近
        if (index < (size >> 1)) {
            Node<E> x = first;
            for (int i = 0; i < index; i++)
                x = x.next;
            return x;
        } else {
            Node<E> x = last;
            for (int i = size - 1; i > index; i--)
                x = x.prev;
            return x;
        }
}
```