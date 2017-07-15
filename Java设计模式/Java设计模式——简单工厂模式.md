---
title: Java设计模式——简单工厂模式
date: 2017-06-10 14:15:39
categories: Java设计模式
tags: 简单工厂模式
---

> 工厂模式是最常见的一种设计模式之一，这种类型的设计模式属于创建型模式，提供了一种创建对象的最佳方式

简单工厂模式又称静态工厂方法模式，构建一个创建对象实例的工厂方法，创建对象是不会向用户暴露创建逻辑，通过用户传入的具体参数创建不同的对象。

**组成：**

1. 抽象产品类：它是具体产品类的父类或者实现接口
2. 具体产品类：工厂类创建的对象就是这些具体的产品类
3. 工厂类：负责具体产品的创建，是这个模式的核心。

**具体实例**：

1. 您需要一辆汽车，可以直接从工厂里面提货，而不用去管这辆汽车是怎么做出来的，以及这个汽车里面的具体实现。 
2. Hibernate 换数据库只需换方言和驱动就可以。



产品类：

```Java
//shape接口
interface Shape
{
    void draw();
}
//具体的shape实现类Rectangle
class Rectangle implements Shape
{
    public void draw()
    {
        System.out.println(this.getClass().getName() + ": draw method!");
    }
}
//具体的shape实现类Circle
class Circle implements Shape
{
    public void draw()
    {
        System.out.println(this.getClass().getName() + ": draw method!");
    }
}
```

工厂类：

```java
//工厂类
class Factory
{
    //工厂类中负责创建shape的工厂方法
    public Shape createShap(String tag)
    {
        if (tag.equals("Rectangle"))
        {
            return new Rectangle();
        } else if (tag.equals("Circle"))
        {
            return new Circle();
        }
        return null;
    }
}
```

测试类：

```java
public class FactoryDemo
{
    public static void main(String[] args)
    {
        Factory factory = new Factory();
        //通过工厂方法创建一个shape实例 Rectangle
        Shape rectangle = factory.createShap("Rectangle");
        Shape circle = factory.createShap("Circle");
        rectangle.draw();
        circle.draw();
    }
}
```