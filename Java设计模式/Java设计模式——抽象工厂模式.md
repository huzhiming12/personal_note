---
title: Java设计模式——抽象工厂模式
date: 2017-06-11 12:07:14
categories: Java设计模式
tags: 抽象工程模式
---

现实生活中一个工厂可能生产多个产品，而工厂方法模式中一个具体的工厂类只负责生产一种产品，随着产品的逐渐增多工厂类也会随之增多，这显然是不合理的。

抽象工厂模式是在工厂方法模式的基础之上增强了工厂类，一个工厂类可以生产多个产品。

抽象工厂模式组成：

1. 抽象工厂：这是工厂方法模式的核心，是具体工厂必须实现的接口或继承的父类。
2. 具体工厂：负责生产具体的产品实例，实现了抽象工厂接口。
3. 抽象产品：具体产品的父类或必须实现的接口，定义了产品必须含有的方法或属性
4. 具体产品类：这是具体工厂创建的实例。



产品类：Button

```java
interface Button
{
    void btnClick();
}
class WinButton implements Button
{
    public void btnClick()
    {
        System.out.println("Windos button clicked!");
    }
}
class LinuxButton implements Button
{
    public void btnClick()
    {
        System.out.println("Linux button clicked!");
    }
}
```

产品类：Text

```Java
interface Text
{
    void textWrite();
}
class WinText implements Text
{
    public void textWrite()
    {
        System.out.println("windows text written!");
    }
}
class LinuxText implements Text
{
    public void textWrite()
    {
        System.out.println("Linux text written!");
    }
}
```

工厂类：

```Java
interface ComponentFactory
{
    Button createButton();
    Text createText();
}
//window组件制造工厂
class WindowsFactory implements ComponentFactory
{
    public Button createButton()
    {
        return new WinButton();
    }
    public Text createText()
    {
        return new WinText();
    }
}
//Linux组件制造工厂
class LinuxFactory implements ComponentFactory
{
    public Button createButton()
    {
        return new LinuxButton();
    }
    public Text createText()
    {
        return new LinuxText();
    }
```

测试类：

```Java
public class AbstractFactoryDemo
{
    public static void main(String[] args)
    {
        ComponentFactory linuxFactory = new LinuxFactory();
        ComponentFactory windowsFactory = new WindowsFactory();
        Button winButton = windowsFactory.createButton();
        Button linuxButton = linuxFactory.createButton();
        winButton.btnClick();
        linuxButton.btnClick();
        Text winText = windowsFactory.createText();
        Text linuxText = linuxFactory.createText();
        winText.textWrite();
        linuxText.textWrite();
    }
}
```



**工厂方法模式和抽象工厂模式对比**

1. 首先两者都有抽象的工厂类、具体的工厂类、抽象的产品类和具体的产品类
2. 工厂方法模式只有一个产品类，而抽象工厂模式中有多个产品类
3. 工厂方法模式中一个具体的工厂类只能生产一个具体产品类；而在抽象工厂模式中，一个共产类可以生产出多个具体的产品类。