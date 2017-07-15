---
title: Java设计模式——代理模式模式 
date: 2017-06-14 09:24:02 
categories: Java设计模式
tag: 代理模式
---



代理（proxy）提供对目标对象的另外一种访问方式，即通过代理对象访问目标对象。这样做的好处就是：可以在目标对象的基础之上增强额外的功能操作，即扩展目标对象的功能。

编程思想：不要随便修改别人已经写好的功能方法，如果要修改可以通过代理的方式来扩展该方法。

#### 静态代理

静态代理代理对象和被代理对象一起实现相同的接口，或者是继承相同的父类。

**接口父类：**

```java
public interface IUserDAO{
  	void save();
}
```

**目标对象：**

```Java
public class UserDAO implement IUserDAO{
  	public void save(){
      	System.out.println("数据正在保存！");
  	}
}
```

代理对象：

```Java
public class UserDAOProxy implement IUserDAO{
  	private IUserDAO target；
    public UserDAOProxy(IUserDAO target){
      	this.target=target;
    }
  	public void save(){
      	System.out.println("数据校验完毕，开始保存数据！");
      	target.save(); //执行目标方法
      	System.out.println("数据保存完毕！");
  	}
}
```

测试类：

```java
public class Main{
  	public static void main(String[]args){
      	//创建目标对象
      	IUserDAO target = new UserDAO();
      	//代理对象，把目标对象传给代理对象，建立代理关系
      	UserDAOProxy proxy = new UserDAOProxy(target);
      	proxy.save();
  	}
}
```



静态代理可以在不修改目标对象的前提下实现对目标对象的扩展，

**缺点：**但是静态代理必须要实现与目标对象一样的接口，这样就会产生很多代理类，类很多。一旦接口方法增加，目标对象和代理对象都要进行维护修改。



#### 动态代理

动态代理主要有两种方法，一种是JDK动态代理，一种是第三方库 cglib动态代理。

##### JDK动态代理

jdk动态代理是基于Java反射机制实现的。

Proxy代理类，通过newInstance()方法，创建代理类。

```Java
public static Object newProxyInstance(ClassLoader loader,
                                          Class<?>[] interfaces,
                                          InvocationHandler h)
        throws IllegalArgumentException
```

ClassLoader loader：被代理对象的类加载器

Class<?>[] interfaces:被代理对象中实现的接口类型

InvocationHandler h：事件处理,执行目标对象的方法时,会触发事件处理器的方法,会把当前执行目标对象的方法作为参数传入

下面是测试样例

父类接口：

```Java
interface IUserDAO
{
	void save();
	void delete();
}
```

目标对象，实现父类接口

```Java
class UserDAO implements IUserDAO
{

	@Override
	public void save()
	{
		System.out.println("user info saving...");
	}

	@Override
	public void delete()
	{
		System.out.println("user is delete!");
	}
}
```

InvocationHandler类，每个代理 的实例都关联了一个handler，每个代理对象调用方法时候，都会转变为有InvocationHandler调用invoke()方法。

```Java
class ProxyHandler implements InvocationHandler
{
	private Object target;

	public ProxyHandler(Object target)
	{
		this.target = target;
	}

	@Override
	public Object invoke(Object proxy, Method method, Object[] args) throws Throwable
	{
		System.out.println("代理方法 " + method.getName() + " 开始");
		Object res = method.invoke(target, args);
		System.out.println("代理方法 " + method.getName() + " 结束");
		return res;
	}
}
```

测试类

```Java
public class JDKProxy
{
	public static void main(String[] args)
	{
      	//目标对象，即被代理对象
		IUserDAO target = new UserDAO();
      	//我们要代理哪个真实对象，就将该对象传进去，最后是通过该真实对象来调用其方法的
		ProxyHandler proxyHandler = new ProxyHandler(target);
		IUserDAO userDAO = 
          		(IUserDAO）Proxy.newProxyInstance(JDKProxy.class.getClassLoader(),
				target.getClass().getInterfaces(),
                proxyHandler);
		userDAO.save();
		userDAO.delete();
	}

}
```

> 代理方法 save 开始
>
> user info saving...
>
> 代理方法 save 结束
>
> 代理方法 delete 开始
>
> user is delete!
>
> 代理方法 delete 结束

jdk动态代理生成代理对象速度快，但是有一个局限就是被代理对象必须实现接口，若被代理对象没有实现接口，将不能进行jdk动态代理。

##### 第三方库CGLib动态代理

CGLib采用了非常底层的字节码技术，其原理是通过字节码技术为一个类创建子类，并在子类中采用方法拦截的技术拦截所有父类方法的调用，顺势织入横切逻辑。

被代理对象：

```Java
class UserDaoImpl
{
	void save()
	{
		System.out.println("info saved!");
	}
}
```



```Java
class CglibProxy implements MethodInterceptor
{
	private Object target;

	public CglibProxy()
	{
	}

	public CglibProxy(Object target)
	{
		this.target = target;
	}

	@Override
	public Object intercept(Object arg0, Method method, Object[] arg2, MethodProxy proxy)
      	throws Throwable
	{
		System.out.println("before proxy!");
		// 两种方式调用都行
		// 第一种 JDK反射机制
		method.invoke(target, arg2);
		// 第二种  这种方法速度更快
		Object object = proxy.invokeSuper(arg0, arg2);
		System.out.println("end proxy!");
		return object;
	}
}
```



```Java
public static void main(String[] args)
	{
		Enhancer enhancer = new Enhancer();
		CglibProxy proxy = new CglibProxy(new UserDaoImpl());

		enhancer.setCallback(proxy);
		enhancer.setSuperclass(UserDaoImpl.class);

		UserDaoImpl userDaoImpl = (UserDaoImpl) enhancer.create();
		userDaoImpl.save();

	}
```