# Java知识点

#### 1. 小知识点

##### 1.1 数字前加0表示八进制数，加0x表示16进制。

```java
package com.object;
public class StaticTest
{
	public static void main(String[] args)
	{
		int a = 0123;
      	int b=123;
		System.out.println(a+" "+b);
    }
}
```

> 输入：83 123

##### 1.2 Java类的成员函数名可以和构造函数名相同

```Java
public class TestConStructor
{
    public TestConStructor()
    {
        System.out.println("constructor");
    }
    public void TestConStructor()
    {
        System.out.println("not constructor");
    }
}
```

##### 1.3 JVM 内存配置参数

Xmx10240m：代表最大堆

 -Xms10240m：代表最小堆

 -Xmn5120m：代表新生代

 -XXSurvivorRatio=3：代表Eden:Survivor = 3    根据Generation-Collection算法(目前大部分JVM采用的算法)，一般根据对象的生存周期将堆内存分为若干不同的区域，一般情况将新生代分为Eden ，两块Survivor；    计算Survivor大小， Eden:Survivor = 3，总大小为5120,3x+x+x=5120  x=1024

##### 1.4 自动拆装箱的考题(自动拆装箱JDK需在1.5上）

1. 基本型和基本型封装型进行“==”运算符的比较，基本型封装型将会自动拆箱变为基本型后再进行比较，因此Integer(0)会自动拆箱为int类型再进行比较，显然返回true；

2. 两个Integer类型进行“==”比较，如果其值在-128至127，那么返回true，否则返回false, 这跟Integer.valueOf()的缓冲对象有关，这里不进行赘述。

3. 两个基本型的封装型进行equals()比较，首先equals()会比较类型，如果类型相同，则继续比较值，如果值也相同，返回true

4. 基本型封装类型调用equals(),但是参数是基本类型，这时候，先会进行自动装箱，基本型转换为其封装类型，再进行3中的比较。

   ```Java
   int a=257;
   Integer b=257;
   Integer c=257;
   Integer b2=57;
   Integer c2=57;
   System.out.println(a==b);
   //System.out.println(a.equals(b));  编译出错，基本型不能调用equals()
   System.out.println(b.equals(257.0));
   System.out.println(b==c);
   System.out.println(b2==c2);
   ```

   因此上面的代码的结果因此为 true, false, false, true

---



#### 2. Java的类成员访问权限

|    修饰词    |  本类  | 同一个包中的类 | 继承类  |  其他  |
| :-------: | :--: | :-----: | :--: | :--: |
|  private  |  √   |    ×    |  ×   |  ×   |
|    无修饰    |  √   |    √    |  ×   |  ×   |
| protected |  √   |    √    |  √   |  ×   |
|  public   |  √   |    √    |  √   |  √   |

* 无修饰词的类成员具有"包访问权限"，即位于同一个包中的类可以访问。

* protected 为“继承权限”，即该类的子类和位于同一个包中的类具有访问权限。

* 类的实例对象可以访问除了private 修饰的成员方法和变量

  ```java
  class Test
  {
  	private int c;
  	int a;
  	protected int b;
  }
  public class StaticTest
  {
  	public static void main(String[] args)
  	{
  		Test test = new Test();
  		System.out.println(test.a);
  		System.out.println(test.b);
        	System.out.println(test.c);//报错
  	}
  }
  ```


**子类继承父类的方法是，控制符必须大于或等于父类的访问控制符**

---



#### 3. sleep与wait区别

* sleep：sleep是正在执行的线程告诉CPU去执行其他线程，当过了sleep指定的时间后再回到这个线程继续执行。但是如果sleep加上了同步锁，它并不会释放同步锁。即使其他线程获得了CPU，但是被同步锁挡住也无法执行
* wait：就是进入同步锁的线程，调用wait函数先释放同步锁供其他线程使用。当其他线程调用notify函数时，就告诉原先调用wait函数的线程可以参与获取同步锁的资源竞争了，但并不是马上得到锁，因为同步锁还在其他线程的手上，必须等那个占用锁的线程执行完毕，或者调用wait()函数才能获得同步锁。


---




#### 4. Vector与ArrayList的区别

Vector和arrayList都实现了List接口，是有序集合，集合内的数据元素都是有序的，可通过位置索引号取出相应的元素值。同时集合内的数据允许重复

区别：

* vector是线程同步的，也就是线程安全的。二ArrayList是线程不同步的，是线程不安全的。如果只有一个线程会访问到集合，那最好还是使用ArrayList，因为不考虑线程安全所以效率会更高。如果是多线程同时访问到集合，最好用Vector，因为不需要我们自己去考虑和编写线程安全代码。
* vector和Hashtable是Java一诞生就提供的，他们是线程安全的。ArrayList和hashMap是Java2才提供的是线程不安全的。
* 数据增长，vector默认是增加2倍，也可以自己设置增长的空间大小。而arrayList默认是1.5倍增长，且没有提供设置增长空间的方法。


---




#### 5. HashMap 与 HashTable的区别

* 历史原因:Hashtable 是基于陈旧的 Dictionary 类的，HashMap 是 Java 1.2引进的 Map接口的一个实现
* 同步性:Hashtable 是线程安全的，也就是说是同步的，而 HashMap 是线程序不安全的，不是同步的
* 值:只有 HashMap 可以让你将空值作为一个表的条目的 key 或 value


Hashtable和HashMap类有三个重要的不同之处。第一个不同主要是历史原因。Hashtable是基于陈旧的Dictionary类的，HashMap是Java 1.2引进的[Map接口](https://www.baidu.com/s?wd=Map%E6%8E%A5%E5%8F%A3&tn=44039180_cpr&fenlei=mv6quAkxTZn0IZRqIHckPjm4nH00T1dBPAc3rjbkPjK9ujRYufKYUHYzPW6vrjb3njR0mv4YUWYYPj01rHD3n7qWTZc0IgF_5y9YIZ0lQzqlpA-bmyt8mh7GuZR8mvqVQL7dugPYpyq8Q1csnWD3Pj6snjDknH63rjRvPf)的一个实现。　　

　　也许最重要的不同是Hashtable的方法是同步的，而HashMap的方法不是。这就意味着，虽然你可以不用采取任何特殊的行为就可以在一个多线程的应用程序中用一个Hashtable，但你必须同样地为一个HashMap提供外同步。一个方便的方法就是利用Collections类的静态的synchronizedMap()方法，它创建一个[线程安全](https://www.baidu.com/s?wd=%E7%BA%BF%E7%A8%8B%E5%AE%89%E5%85%A8&tn=44039180_cpr&fenlei=mv6quAkxTZn0IZRqIHckPjm4nH00T1dBPAc3rjbkPjK9ujRYufKYUHYzPW6vrjb3njR0mv4YUWYYPj01rHD3n7qWTZc0IgF_5y9YIZ0lQzqlpA-bmyt8mh7GuZR8mvqVQL7dugPYpyq8Q1csnWD3Pj6snjDknH63rjRvPf)的Map对象，并把它作为一个封装的对象来返回。这个对象的方法可以让你同步访问潜在的HashMap。这么做的结果就是当你不需要同步时，你不能切断Hashtable中的同步（比如在一个单线程的应用程序中），而且同步增加了很多处理费用。

　　第三点不同是，只有HashMap可以让你将空值作为一个表的条目的key或value。HashMap中只有一条记录可以是一个空的key，但任意数量的条目可以是空的value。这就是说，如果在表中没有发现搜索键，或者如果发现了搜索键，但它是一个空的值，那么get()将返回null。如果有必要，用containKey()方法来区别这两种情况。

---



#### 6. java 接口

```Java
package com.object;
public interface Test
{
	void name1();
	public void name2();
	abstract void name3();
	final static int a=0;
	static int b=0;
}
```

*  接口中方法默认修饰符 public abstract，默认的修饰符可以省略
*  接口中的变量 默认修饰符 public static final  默认的修饰符可以省略
*  接口不能再继承接口，一个类可以继承多个接口，但是只能继承一个类（包括抽象类）


---




#### 7. String、StringBuffer、StringBuilder  

string 是public final class String 无法继承。

**三者执行的效率：String<StringBuffer<StringBuilder**

String：字符串常量  

StringBuffer：字符串变量，线程安全的

StringBuilder：字符串变量，线程不安全

```java
String str1="abcd"+"efg"+"hijkl";
//上面这句话其实等价于下面这个代码
String str2="abcdefghijkl";
```

```java
String str2 = “This is only a”;
String str3 = “ simple”;
String str4 = “ test”;
String str1 = str2 +str3 + str4;
//这种方式Java虚拟机就会按原来的方式进行，故这种方式效率很低
```

---



#### 8. 方法重写和重载

##### 方法重载（overload）：

* 必须是同一个类
* 方法名（也可以叫函数）一样
* 参数类型不一样或参数数量不一样

##### 方法的重写（override）两同两小一大原则： 

* 方法名相同，参数类型相同
* 子类返回类型小于等于父类方法返回类型，
* 子类抛出异常小于等于父类方法抛出异常，
* 子类访问权限大于等于父类方法访问权限。


---



#### 9.volatile

**内存可见性**：通俗来说就是，线程A对一个volatile变量的修改，对于其它线程来说是可见的，即线程每次获取volatile变量的值都是最新的。

---



#### 10.static

##### Java静态内部类：

static一般是不能修饰类的，只能修饰内部类。

- 一般情况下，**如果一个内部类不是被定义成静态内部类，那么在定义成员变量或者成员方法的时候，是不能够被定义成静态成员变量与静态成员方法的。**也就是说，在非静态内部类中不可以声明静态成员。
- 一般的非静态内部类，**可以随意的访问外部类中的成员变量与成员方法**。即使这些成员方法被修饰为private(私有的成员变量或者方法)，其非静态内部类都可以随意的访问。这是非静态内部类的特权。但是如果一个内部类被定义为静态的，那么在银用外部类的成员方法或则成员变量的时候，就会有诸多的限制。如不能够从静态内部类的对象中访问外部类的非静态成员(包括成员变量与成员方法)。如果在外部类中定义了两个变量，一个是非静态的变量，一个是静态的变量。那么在静态内部类中，无论在成员方法内部还是在其他地方，都只能够引用外部类中的静态的变量，而不能够访问非静态的变量。**在静态内部类中，可以定义静态的方法(也只有在静态的内部类中可以定义静态的方法)，在静态方法中引用外部类的成员。**但是无论在内部类的什么地方引用，有一个共同点，**即都只能够引用外部类中的静态成员方法或者成员变量。**对于那些非静态的成员变量与成员方法，在静态内部类中是无法访问的。
- **在创建静态内部类时不需要将静态内部类的实例绑定在外部类的实例上**

