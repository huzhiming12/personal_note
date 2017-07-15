# Java源码——Object

Object类位于Java.lang核心包中，是所有类的祖先。Java.lang包含了Java最基础最核心的类，编译时会自动导入。

#### 1.构造函数 Object()

Java源码中并没有Object构造函数的定义，但其实Object类中其实应该是有这个构造函数的。Java中规定，若一个类没有声明构造函数，则系统会自动为其添加一个没有带参数的构造函数。

#### 2.private static native void registerNatives();

在Java中native修饰的方法表明该方法的实现并不在Java源码中，其实现是由C/C++完成。用native修饰，表示系统需要提供此方法供Java本身使用。

registerNatives()方法本身主要的作用是将C/C++中的方法映射到Java中的native方法中，实现方法名的解耦。

#### 3.protected native Object clone() throws CloneNotSupportedException

克隆方法，该方法返回一个一模一样的对象引用，但是返回的对象和原对象是两个不同的对象。Java术语表述:clone函数返回的是一个引用，指向的是新的clone出来的对象，此对象与原对象分别占用不同的堆空间。

由于clone()函数是protected，因此只能被同一包中的类和子类访问。同时子类还必须实现了Cloneable接口。

Cloneable接口仅是一个表示接口，接口本身不包含任何方法，用来指示Object.clone()可以合法的被子类引用所调用。

```java
package com.object;

public class MainTest implements Cloneable
{
	public static void main(String[] args)
	{
		MainTest mainTest = new MainTest();
		try
		{
			MainTest clone = (MainTest) mainTest.clone();
			System.out.println(mainTest.toString());
			System.out.println(clone.toString());
		} catch (CloneNotSupportedException e)
		{
			// TODO Auto-generated catch block
			e.printStackTrace();
		}	
	}
}

```

#### 4.**public** **final** **native** Class<?> **getClass**();

返回此Object对象的类对象（Class）/运行时对象。Class对象用于描述类的所有属性方法等信息。



#### 5.public boolean equals(Object obj);

==表示的是变量值完成相同（对于基础类型，地址中存储的是值，引用类型则存储指向实际对象的地址）；

equals：equals表示的是对象的内容完全相同，此处的内容多指对象的特征/属性。

object中equals方法定义如下，即判断两个值是否相等：

```Java
public boolean equals(Object obj) {
     return (this == obj); 
}
```

在object类中，此标尺即为==。当然，这个标尺不是固定的，其他类中可以按照实际的需要对此标尺含义进行重定义。但要注意，重新定义equals方法时，必须重新定义Hashcode方法。

#### 6.public native int hashCode();

Hashcode返回一个整形值，表示该对象的hash码值。

hashCode()具有如下约定：

1).在Java应用程序程序执行期间，对于同一对象多次调用hashCode()方法时，其返回的哈希码是相同的，前提是将对象进行equals比较时所用的标尺信息未做修改。在Java应用程序的一次执行到另外一次执行，同一对象的hashCode()返回的哈希码无须保持一致；

2).如果两个对象相等（依据：调用equals()方法），那么这两个对象调用hashCode()返回的哈希码也必须相等；

3).反之，两个对象调用hasCode()返回的哈希码相等，这两个对象不一定相等。

Hashcode方法主要是增强hash表的性能。

以集合类中，以Set为例，当新加一个对象时，需要判断现有集合中是否已经存在与此对象相等的对象，如果没有hashCode()方法，需要将Set进行一次遍历，并逐一用equals()方法判断两个对象是否相等，此种算法时间复杂度为o(n)。通过借助于hasCode方法，先计算出即将新加入对象的哈希码，然后根据哈希算法计算出此对象的位置，直接判断此位置上是否已有对象即可。（注：Set的底层用的是Map的原理实现）

#### 7.public String toString();

toString放回对象的字符串表示。

```Java
public String toString() {
        return getClass().getName() + "@" + Integer.toHexString(hashCode());
}
```



#### wait(……)/notify()/notifiyAll()

wait()：调用此方法所在的当前线程等待，直到在其他线程上调用此方法的主调（某一对象）的notify()/notifyAll()方法。

wait(long timeout)/wait(long timeout, int nanos)：调用此方法所在的当前线程等待，直到在其他线程上调用此方法的主调（某一对象）的notisfy()/notisfyAll()方法，或超过指定的超时时间量。

notify()/notifyAll()：唤醒在此对象监视器上等待的单个线程/所有线程。

1、wait(...)方法调用后当前线程将立即阻塞，且适当其所持有的同步代码块中的锁，直到被唤醒或超时或打断后且重新获取到锁后才能继续执行；

2、notify()/notifyAll()方法调用后，其所在线程不会立即释放所持有的锁，直到其所在同步代码块中的代码执行完毕，此时释放锁，因此，如果其同步代码块后还有代码，其执行则依赖于JVM的线程调度。



#### 13.protected void finalize();

```Java
protected void finalize() throws Throwable { }
```

Object中定义finalize方法表明Java中每一个对象都将具有finalize这种行为，其具体调用时机在：JVM准备对此对形象所占用的内存空间进行垃圾回收前，将被调用。由此可以看出，此方法并不是由我们主动去调用的（虽然可以主动去调用，此时与其他自定义方法无异）。









