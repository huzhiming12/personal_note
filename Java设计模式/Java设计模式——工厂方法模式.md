---
title: Java设计模式——工厂方法模式
date: 2017-06-11 11:07:39
categories: Java设计模式
tags: 工厂方法模式
---

工厂方法模式将简单工厂模式中的工厂类抽取出来，用子工厂具体负责生产具体类产品。这样在产品生产的压力可以由工厂方法模式中的不同工厂子类来分担。

工厂方法模式组成：

1. 抽象工厂：这是工厂方法模式的核心，是具体工厂必须实现的接口或继承的父类。
2. 具体工厂：负责生产具体的产品实例，实现了抽象工厂接口。
3. 抽象产品：具体产品的父类或必须实现的接口，定义了产品必须含有的方法或属性
4. 具体产品类：这是具体工厂创建的实例。



产品类：

```Java
//产品抽象接口
interface Color
{
    void fill();
}
//产品实例
class Green implements Color
{	//产品方法
    public void fill()
    {
        System.out.println("fill color Green!");
    }
}
class Red implements Color
{
    public void fill()
    {
        System.out.println("fill color Red!");
    }
}
```

工厂类：

```Java
//工厂抽象接口
interface ColorFactory
{
    Color createColor();
}
// 子工厂类，负责生产具体的实例
class GreenFactory implements ColorFactory
{
    public Color createColor()
    {
        return new Green();
    }
}

class RedFactory implements ColorFactory
{
    public Color createColor()
    {
        return new Red();
    }
}
```

测试类：

```Java
public class FactoryMethodDemo
{
    public static void main(String[] args)
    {
        ColorFactory gFactory = new GreenFactory();
        ColorFactory rFactory = new RedFactory();
        Color green = gFactory.createColor();
        Color red = rFactory.createColor();
        green.fill();
        red.fill();
    }
}
```


**简单工厂模式 vs. 工厂方法模式**

* 简单工厂模式：最大优点在于工厂类中包含了必要的逻辑判断，根据客户端的选择条件动态实例化相关的类，对于客户端来说，去除了与具体产品的依赖。
* 工厂方法模式：客户端需要决定实例化哪个工厂来实现运算类，选择判断的问题还是存在的。工厂方法吧内部逻辑判断移到了客户端 代码进行，想要增加功能，原先是修改工厂类，现在是修改客户端。







































