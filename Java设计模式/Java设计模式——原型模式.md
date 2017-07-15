---
title: Java设计模式——原型模式
date: 2017-06-15 08:51:20
categories: Java设计模式
tags: 原型模式
---

> 原型模式定义：用原型实例指定创建对象的种类，并且通过拷贝这些原型创建新的对象

### 原型模式

原型模式其实就是从一个对象再创建另外一个可定制的对象，而且不需要知道任何的创建细节。下面我们通过一个例子来说明，对用户信息的复制。

原型类：实现Cloneable接口，表明这个类是可以复制的，复制方法直接用调用本地的Native方法复制。

```Java
abstract class Prototype implements Cloneable
{
    @Override
    public Object clone()
    {
        try
        {
            return super.clone();
        } catch (CloneNotSupportedException e)
        {
            e.printStackTrace();
            return null;
        }
    }
}
```

用户信息类：UserInfo 继承了Prototype，clone方法直接调用Prototype的克隆方法

```Java
class UserInfo extends Prototype
{
    String name;
    String birthDate;
    String address;
    University university;
    public UserInfo(String birthDate, String address, String name, University university)
    {
        this.birthDate = birthDate;
        this.address = address;
        this.name = name;
        this.university = university;
    }
    @Override
    public String toString()
    {
        return "UserInfo{" +
                "name='" + name + '\'' +
                ", birthDate='" + birthDate + '\'' +
                ", address='" + address + '\'' +
                ", university=" + university +
                '}';
    }
    @Override
    public Object clone()
    {
        return super.clone();
    }
}
```

University类：

```Java
class University
{
    String uName;
    public University(String uName)
    {
        this.uName = uName;
    }
    @Override
    public String toString()
    {
        return "{" +
                "uName='" + uName + '\'' +
                '}';
    }
}
```

测试类：

```Java
public class PrototypeDemo
{
    public static void main(String[] args) throws CloneNotSupportedException
    {
        UserInfo info = new UserInfo("张三", "1990-03-23", "山东青岛");
        UserInfo infoCopy = (UserInfo) info.clone();
        System.out.println(info);
        System.out.println(infoCopy);
    }
}
```

输出结果：

```
UserInfo{name='山东青岛', birthDate='张三', address='1990-03-23', university={uName='青岛大学'}}
UserInfo{name='山东青岛', birthDate='张三', address='1990-03-23', university={uName='青岛大学'}}
```

**原型模式的优点及适用场景**

* 使用原型模式创建对象比直接new一个对象在性能上要好的多，因为Object类的clone方法是一个本地方法，它直接操作内存中的二进制流，特别是复制大对象时，性能的差别非常明显。
* 使用原型模式的另一个好处是简化对象的创建，使得创建对象就像我们在编辑文档时的复制粘贴一样简单。

​       因为以上优点，所以在需要重复地创建相似对象时可以考虑使用原型模式。比如需要在一个循环体内创建对象，假如对象创建过程比较复杂或者循环次数很多的话，使用原型模式不但可以简化创建过程，而且可以使系统的整体性能提高很多。

### 浅拷贝与深拷贝

#### 浅拷贝

Object类的clone方法只会拷贝对象中的基本的数据类型，对于数组、容器对象、引用对象等都不会拷贝，这就是浅拷贝。如上面的例子中，用户信息中有一个对象是所在的大学，对university字段进行如下修改：

```Java
UserInfo info = new UserInfo("张三", "1990-03-23", "山东青岛",new University("青岛大学"));
UserInfo infoCopy = (UserInfo) info.clone();
infoCopy.university.uName="中国海洋大学";
System.out.println(info);
System.out.println(infoCopy);
```

输出结果是：

```
UserInfo{name='山东青岛', birthDate='张三', address='1990-03-23', university={uName='中国海洋大学'}}
UserInfo{name='山东青岛', birthDate='张三', address='1990-03-23', university={uName='中国海洋大学'}}
```

infoCopy是info的一份拷贝，对infoCopy的修改原则上info中的信息是不会变得，但是Object中的clone方法只是一个浅拷贝，浅拷贝只会拷贝8种基本数据类型和String，对于数组、容器、引用对象只是拷贝对其的引用。

#### 深拷贝

深拷贝就是对对象中的所有数据进行拷贝，包括基本数据类型和引用对象。具体实现就是必须将原型模式中数组对象、引用对象、容器对象等进行单独的拷贝。

UserInfo对象中clone方法修改：

```java
class UserInfo extends Prototype
{
 	……
    @Override
    public Object clone()
    {
        UserInfo info = (UserInfo) super.clone();
        try
        {
            info.university = (University) this.university.clone();
        } catch (CloneNotSupportedException e)
        {
            e.printStackTrace();
        }
        return info;
    }
}
```

University类中添加clone方法：

```Java
class University implements Cloneable
{
    ……
    @Override
    protected Object clone() throws CloneNotSupportedException
    {
        return super.clone();
    }
}
```

输出结果：

```
UserInfo{name='山东青岛', birthDate='张三', address='1990-03-23', university={uName='青岛大学'}}
UserInfo{name='山东青岛', birthDate='张三', address='1990-03-23', university={uName='中国海洋大学'}}
```