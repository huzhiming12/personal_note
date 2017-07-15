---
title: Java 注解Annotation
date: 2017-05-21 21:35:02
categories: Java基础
tags: 注解
---

> 从JDK5开始，Java增加了Annotation(注解)，Annotation是代码里的特殊标记，这些标记可以在编译、类加载、运行时被读取，并执行相应的处理。通过使用Annotation，开发人员可以在不改变原有逻辑的情况下，在源文件中嵌入一些补充的信息。

### 注解作用

1. 生成文档。这是最常见的，也是[Java](http://lib.csdn.net/base/javase) 最早提供的注解。常用的有@see @param @return 等
2. 跟踪代码依赖性，实现代替配置文件的功能。比较常见的是[spring](http://lib.csdn.net/base/javaee) 2.5 开始的基于注解配置。作用就是减少配置。现在的框架基本都使用了这种配置来减少配置文件的数量。
3. 在编译时进行格式检查。如@override 放在方法前，如果你这个方法并不是覆盖了超类方法，则编译时就能检查出。



### 注解Annotation定义

定义新的注解类型时使用@interface，与接口定义很相似只是多了一个@符号

```Java
public @interface FirstAnnotation
{
}
```

定义完Annotation之后就可以在程序中使用该Annotation。Annotation一般放在所有修饰符之前，并且单独一行

```java
@FirstAnnotation
public class Test
{
}
```

#### Annotation成员变量

​	Annotation只有成员变量，没有方法。Annotation的成员变量在Annotation定义中以“无形参的方法”形式来声明，其方法名定义了该成员变量的名字，其返回值定义了该成员变量的类型。

```Java
public @interface FirstAnnotation
{
	public String name();
	public int age();
}
```

​	一旦在Annotation中定义了成员变量之后，使用该Annotation时就应该为该Annotation的成员变量指定值。

```Java
@FirstAnnotation(name = "李四", age = 16)
public class Test
{
}
```

​	Annotation定义成员变量时可以指定默认值，指定默认值之后。使用Annotation时，已经指定默认值得变量若没有指定值，则会直接使用默认值。

```Java
public @interface FirstAnnotation
{
	public String name() default "张三";
	public int age();
}
```

​	使用含有默认值的Annotation，如上面name指定了默认值，使用的时候就可以不再对其赋值；而age没有指定默认值，使用时必须对其赋值。

```Java
@FirstAnnotation(age = 16)
public class Test
{
}
```



根据Annotation是否包含成员变量，可以把Annotation分为如下两类： 

- 标记Annotation：没有成员变量的Annotation被称为标记。这种Annotation仅用自身的存在与否来为我们提供信息，例如@override等。 
- 元数据Annotation：包含成员变量的Annotation。因为它们可以接受更多的元数据，因此被称为元数据Annotation。



#### 元注解

在定义Annotation时，也可以使用JDK提供的元注解来修饰Annotation定义。JDK提供了如下4个元注解（注解的注解，不是上述的”元数据Annotation“）： 

- @Retention
- @Target
- @Documented
- @Inherited



##### @Retention

@Retention用于指定Annotation可以保留多长时间。 @Retention包含一个名为“value”的成员变量，该value成员变量是RetentionPolicy枚举类型。使用@Retention时，必须为其value指定值。

- RetentionPolicy.SOURCE：Annotation只保留在源代码中，编译器编译时，直接丢弃这种Annotation。
- RetentionPolicy.CLASS：编译器把Annotation记录在class文件中。当运行Java程序时，JVM中不再保留该Annotation。
- RetentionPolicy.RUNTIME：编译器把Annotation记录在class文件中。当运行Java程序时，JVM会保留该Annotation，程序可以通过反射获取该Annotation的信息。

```java
@Retention(RetentionPolicy.RUNTIME)
public @interface FirstAnnotation
{
}
```



##### @Target

@Target指定Annotation用于修饰哪些程序元素。@Target也包含一个名为”value“的成员变量，该value成员变量类型为ElementType[ ]，ElementType为枚举类型，值有如下几个： 

- ElementType.TYPE：能修饰类、接口或枚举类型
- ElementType.FIELD：能修饰成员变量
- ElementType.METHOD：能修饰方法
- ElementType.PARAMETER：能修饰参数
- ElementType.CONSTRUCTOR：能修饰构造器
- ElementType.LOCAL_VARIABLE：能修饰局部变量
- ElementType.ANNOTATION_TYPE：能修饰注解
- ElementType.PACKAGE：能修饰包



##### @Documented

如果定义注解A时，使用了@Documented修饰定义，则在用javadoc命令生成API文档后，所有使用注解A修饰的程序元素，将会包含注解A的说明。



##### @Inherited

@Inherited指定Annotation具有继承性。

```java
package com.demo2;

import java.lang.annotation.ElementType;
import java.lang.annotation.Inherited;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Inherited
public @interface MyTag{

}
```

```java
package com.demo2;

@MyTag
public class Base {

}
```

```java
package com.demo2;

//SubClass只是继承了Base类
//并未直接使用@MyTag注解修饰
public class SubClass extends Base {
	public static void main(String[] args) {
		System.out.println(SubClass.class.isAnnotationPresent(MyTag.class));
	}
}
```

示例中Base使用@MyTag修饰，SubClass继承Base，而且没有直接使用@MyTag修饰，但是因为MyTag定义时，使用了@Inherited修饰，具有了继承性，所以运行结果为true。 

如果MyTag注解没有被@Inherited修饰，则运行结果为：false。



#### 基本Annotation

JDK默认提供了如下几个基本Annotation： 

- **@Override **

限定重写父类方法。对于子类中被@Override 修饰的方法，如果存在对应的被重写的父类方法，则正确；如果不存在，则报错。@Override 只能作用于方法，不能作用于其他程序元素。 

- **@Deprecated**

用于表示某个程序元素（类、方法等）已过时。如果使用被@Deprecated修饰的类或方法等，编译器会发出警告。 

- **@SuppressWarning**

抑制编译器警告。指示被@SuppressWarning修饰的程序元素（以及该程序元素中的所有子元素，例如类以及该类中的方法.....）取消显示指定的编译器警告。例如，常见的@SuppressWarning（value="unchecked"） 

- **@SafeVarargs**

@SafeVarargs是JDK 7 专门为抑制“堆污染”警告提供的。