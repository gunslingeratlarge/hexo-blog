title: 设计模式 part5 结构模式（下）
author: gsal
tags:
  - 设计模式
  - Java
categories:
  - Java
date: 2018-05-31 16:07:00
---
## 外观模式（Facade）
外观模式很简单。一个比较好的比喻就是基金和股票。以前需要用户自己去买股票，需要了解很多关于股票啊，国债之类的信息，后来由基金经理统一管理，这里的基金就是Facade，这样用户只需要和基金打交道就可以了。非常舒服。  
什么时候用外观模式？  
有遗留老系统，新的开发团队分两组，一组在老系统之上写一个Facade，另一组基于新的Facade接口作开发。  

![upload successful](/images/facade.png)
``` java

Camara.java
 
package facade;  
  
public class Camara {  
    public void turnOn()  
    {  
        System.out.println("开启摄像头！");  
    }  
      
    public void turnOff()  
    {  
        System.out.println("关闭摄像头！");  
    }  
}  
   
Light.java
 
package facade;  
  
public class Light {  
    public void turnOn()  
    {  
        System.out.println("开灯！");  
    }  
      
    public void turnOff()  
    {  
        System.out.println("关灯！");  
    }  
}  
 
Sensor.java
 

package facade;  
  
public class Sensor {  
    public void activate()  
    {  
        System.out.println("开启感应器！");  
    }  
      
    public void deactivate()  
    {  
        System.out.println("关闭感应器！");  
    }  
}  
 
MyFacade.java
 

package facade;  
  
public class MyFacade {  
    private static Camara c1, c2;  
    private static Light l1, l2, l3;  
    private static Sensor s;  
      
    static  
    {  
        c1 = new Camara();  
        c2 = new Camara();  
        l1 = new Light();  
        l2 = new Light();  
        l3 = new Light();  
        s = new Sensor();  
    }  
      
    public static void activate()  
    {  
        c1.turnOn();  
        c2.turnOn();  
          
        l1.turnOn();  
        l2.turnOn();  
        l3.turnOn();  
          
        s.activate();  
    }  
      
    public static void deactivate()  
    {  
        c1.turnOff();  
        c2.turnOff();  
          
        l1.turnOff();  
        l2.turnOff();  
        l3.turnOff();  
          
        s.deactivate();  
    }  
}  
 
ClientTest.java
 
package facade;  
  
public class ClientTest {  
    /** 
     * @param args 
     */  
    public static void main(String[] args) {  
        // TODO Auto-generated method stub  
        //打开  
        MyFacade.activate();  
        //关闭  
        MyFacade.deactivate();  
    }  
  
}  
 

```
<!--more-->

## 享元模式
享元模式使得我们可以共享一些轻量级的对象，跟多例模式有点像。但是享元模式会把对象的状态分为内部状态（所有的享元都不会变的）和外部状态（同一个享元的不同之处）。共享的其实是享元的内部状态。外部状态作为参数传入进享元中。    
举个围棋的例子，围棋很多棋子，如果每一粒棋子都是对象，那么一盘围棋就有几百个对象。所以棋子能不能就创建两个对象呢？一个黑棋，一个白棋，然后将棋子的坐标作为外部状态传入到享元中，这样就用两个对象，但是使用这些棋子的时候有不同的位置了。   
享元模式由客户端，抽象享元类，具体享元类，和享元工厂组成的。对象由享元工厂制造。有时候会使得系统比较复杂，并且需要将状态外部化，可能比较复杂。  
享元模式与多例模式的区别？ 享元模式状态分为外部状态和状态，共享之间也可以有不同，但是多例模式共享是相同的。

![upload successful](/images/flyweight.png)

### 示例代码
``` java
package com.zcr.flyweight2;

public class User
{
    private String name;
    
    public User(String name)
    {
        this.name = name;
    }

    public String getName()
    {
        return name;
    }

    public void setName(String name)
    {
        this.name = name;
    }
    
    
}

 

package com.zcr.flyweight2;

//享元设计模式实现

//网站抽象类
public abstract class WebSite
{
    //“使用”方法需要传递“用户”对象
    public abstract void Use(User user);
}

 

 

 


package com.zcr.flyweight2;

//具体的网站类
public class ConcreteWebSite extends WebSite
{
    private String name = "";
    
    public ConcreteWebSite(String name)
    {
        this.name = name;
    }
    
    @Override
    public void Use(User user)
    {
        System.out.println("网站分类："+name+"用户名："+user.getName());

    }

}

 

 


package com.zcr.flyweight2;

import java.util.Hashtable;

//网站工厂
public class WebsiteFactory
{
    private Hashtable<String,WebSite> flyweights = new Hashtable<String, WebSite>();
    
    //获得网站分类
    public WebSite GetWebSiteCategory(String key)
    {
        //判断是否存在这个对象，如果存在则直接返回，若不存在，则实例化它再返回
        if(!flyweights.contains(key))
        {
            flyweights.put(key, new ConcreteWebSite(key));
        }
        return flyweights.get(key);
    }
    
    //获取网站分类的总数
    public int GetWebSiteCount()
    {
        return flyweights.size();
    }
}

 


package com.zcr.flyweight2;

public class FlyweightTest
{
    public static void main(String[] args)
    {
        WebsiteFactory f = new WebsiteFactory();
        
        WebSite fx = f.GetWebSiteCategory("产品展示");
        fx.Use(new User("小菜"));
        
        WebSite fy = f.GetWebSiteCategory("产品展示");
        fy.Use(new User("大烟笼"));
        
        WebSite fz = f.GetWebSiteCategory("产品展示");
        fz.Use(new User("大菜"));
        
        WebSite fl = f.GetWebSiteCategory("博客");
        fl.Use(new User("老顽童"));
        
        WebSite fm = f.GetWebSiteCategory("博客");
        fm.Use(new User("肥菜"));
        
        WebSite fn = f.GetWebSiteCategory("博客");
        fn.Use(new User("小小斌"));
        
        System.out.println("网站分类总数为：" + f.GetWebSiteCount());
    }
}

```
### 运行结果

![upload successful](/images/result.png)

## 代理模式（非常重要）
### 代理模式与装饰者模式的区别
Proxy and Decorator both have the same interface as their wrapped types, but the proxy creates an instance under the hood, whereas the decorator takes an instance in the constructor    
即代理模式在初始化的时候自己创建了一个被代理对象，而装饰者模式是动态地给装饰者类传入了被装饰对象。所以这就导致了装饰者模式可以动态地对对象进行装饰，而代理模式则是在编译级别就确定好了对谁进行代理。  
用户不知道自己是给谁发的消息，因为都实现了同一个接口。  

### 使用场景
虚拟代理：如插入图像，可以先不加载。代理先打出"正在加载。。"，等到真正需要这个图片的时候再去读取这个图片。起到一个延迟加载的功能。 
远程代理
安全代理：起到一些安全检查的功能

### 动态代理
比较好的参考资料：https://www.bilibili.com/video/av23094347/?p=7  
运行时生成一个类（不是对象），java中的InvocationHandler就是一个生成代理类的接口。
为什么要用动态代理：用户访问需要代理进行权限检查，所以有User和UserProxy，订单访问可能也需要代理进行权限检查，所以有Order和OrderProxy，文件访问也需要进行权限检查，所以有File和FileProxy，而UserProxy和OrderProxy以及FileProxy都进行的是权限检查功能，所以功能比较重复，因此我们想将它们简化到 一起，从而出现了动态代理。  
获得动态的代理对象—— 运行时动态地为Target对象（被代理对象）创建一个虚拟的代理对象。  
之所以说是动态的，是因为不再是在代码编写的层面来修改类了，而是在运行时通过反射在内存中动态地去修改Class字节码对象的一些功能。 
Proxy类（reflect包下）的类有一个静态方法叫newProxyInstance(),返回一个代理对象（这个代理对象就是动态生成的了），而它是谁的代理对象呢，根据参数来确定。   
我们先看一个简单的例子：这里的Target是目标对象的类，TargetInterface是目标对象实现的接口，我们动态生成的代理对象也应该是这个接口。（要不然没法代理）。所以要告诉Proxy的newProxyInstance这个静态方法，用哪个类加载器，有哪些接口，以及我方法怎么样去扩充（动态生成的代理对象是哪个类（by InvocationHandler））。

``` java
public class ProxyTest {

	@Test
	public void test1() {
		
		//现在要动态地为Target创建一个代理，在Target的method前后进行一些操作
		TargetInterface newProxyInstance = (TargetInterface) Proxy.newProxyInstance(
				Target.class.getClassLoader(),
				new Class[]{TargetInterface.class},
				 new InvocationHandler() {
					
					@Override
					public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
						//这里proxy是干嘛的先不考虑，不要一步吃成胖子
						System.out.println("调用目标对象的方法前我做一些事情...");
						//method就是对应的目标对象的那个方法（的字节码对象?），这里你要告诉method调用哪个对象的这个方法c
						Object invoke = method.invoke(new Target(),args);
						System.out.println("调用目标对象的方法后我做一些事情...");
						//返回目标对象的方法
						return invoke;
					}
				}
				);
		
		newProxyInstance.method1();
		String method2 = newProxyInstance.method2();
		System.out.println(method2);
	}
}


```
也可以用target.getClass().getInterfaces()来获得接口的Class\[\]数组。代理对象调用接口响应的方法都是调用在InvocationHandler的匿名内部类中定义的invode方法。  
invoke的三个参数：proxy：就是代理对象。为什么要传入这个代理对象？若想知道，请看下面这篇文章，若不想知道，也不必过多管它。  简单来说就是你可以返回这个proxy以达成连续调用这个代理对象，因为如果不这样做的话你就没办法去得到它（this返回的是这个new InvocationHandler()对象）。实际开发中不会使用到。
https://blog.csdn.net/bu2_int/article/details/60150319    
method：代表的是目标方法的字节码对象   
args:method所传入的参数  

匿名内部类调用外部的局部变量需要定义为final，即不可被修改。（jdk1.8后可以不定义为final，但是还是不可被修改）
我们还可以对method进行判断，实现对特定的方法进行功能增强。  
JDK的Proxy方法实现动态代理只能对接口进行动态代理，CGLIB可以实现对类的动态代理。   
下面给出一个动态代理的示例代码，有两个类分别是对数据库和磁盘进行增删改查，他们的代理类具有同样的功能即对查询方法进行增强，去cache中查找是否命中，若不命中再去对应的数据库或者磁盘上查找。代码如下：
``` java
第五周作业
1.AbstractDbOperator.java
package hm5;

public interface AbstractDbOperator {
	public abstract void query();
	public abstract void insert();
	public abstract void delete();
	public abstract void update();
}

2.AbstractFileOperator.java


package hm5;

public interface AbstractFileOperator {
	public abstract void query();
	public abstract void insert();
	public abstract void delete();
	public abstract void update();
}

3.CacheHandler.java

package hm5;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;

public class CacheHandler implements InvocationHandler{

	private Object target;
	
	public CacheHandler(Object target) {
		this.target = target;
	}
	
	@Override
	public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
		System.out.println("判断是否是查询数据...");
		String methodName = method.getName();
		if (methodName.equals("query")) {
			System.out.println("是查询数据，对cache进行查询。");
			//这里其实应该是if(cache为命中)
			System.out.println("cache未命中,执行原有方法");
			return method.invoke(target, args);
			
		} else {
			System.out.println("不是查询数据，正常执行。");
			
			return method.invoke(target, args);
		}
		
	}
}


4.FileOperator.java

package hm5;

public class FileOperator implements AbstractFileOperator{
	@Override
	public void query() {
		System.out.println("对磁盘数据进行查询...");
	}
	@Override
	public void insert() {
		System.out.println("对磁盘进行插入...");
	}
	@Override
	public void update() {
		System.out.println("对磁盘进行更新...");
	}
	@Override
	public void delete() {
		System.out.println("对磁盘进行删除...");
	}
}



5.MysqlOperator.java

package hm5;

public class MysqlOperator implements AbstractDbOperator{
	@Override
	public void query() {
		System.out.println("对mysql数据库进行查询...");
	}
	@Override
	public void insert() {
		System.out.println("对mysql数据库进行插入...");
	}
	@Override
	public void update() {
		System.out.println("对mysql数据库进行更新...");
	}
	@Override
	public void delete() {
		System.out.println("对mysql数据库进行删除...");
	}
}


6.Test.java

package hm5;

import java.lang.reflect.Proxy;

public class Test {

	public static void main(String[] args) {
		
		AbstractDbOperator dbOperator = new MysqlOperator();
		AbstractFileOperator fileOperator = new FileOperator();
		//动态生成的代理对象
		AbstractDbOperator dbProxy= (AbstractDbOperator) Proxy.newProxyInstance(
				dbOperator.getClass().getClassLoader(),
				dbOperator.getClass().getInterfaces(), 
				new CacheHandler(dbOperator)
		);
		AbstractFileOperator fileProxy= (AbstractFileOperator)Proxy.newProxyInstance(
				fileOperator.getClass().getClassLoader(),
				fileOperator.getClass().getInterfaces(), 
				new CacheHandler(fileOperator)
				);
		dbProxy.update();
		fileProxy.update();
		dbProxy.delete();
		fileProxy.delete();
		dbProxy.query();
		fileProxy.query();
	}

}



```