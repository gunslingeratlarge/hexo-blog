title: java类加载器
author: gsal
tags:
  - Java
categories:
  - Java
date: 2018-06-02 13:02:00
---
## 概念
类加载器就是加载.class字节码文件，有三种classloader，bootstrap，extclassloader和appclassloader，其中最后一个类加载器就是用来加载用户和第三方jar包的类加载器。当Class对象被加载到内存中以后，我们可以通过Class对象拿到它的类加载器，即Class对象记得是谁加载它的。而我们怎么获得字节码对象呢？（即Class对象）。回顾反射，有三种方式，`Object.getClass()`,`类名.class`，`Class.forName()`。  
通过`ClassLoader 字节码对象.getClassLoader()`获得类加载器，能够获得src（对应的存放class文件的位置：classes文件夹/bin文件夹）下任何一个资源文件。  
`classLoader.getResource("")`,而这个参数是对于classLoader来说的，所以是相对于src的，所以src下的一个jdbc.properties可以直接用`getResource("jdbc.properties")`来得到。那么classloader的根目录是哪里呢？是null，即不能这样写。  
<!--more-->
我们有时还会看到这样一种用法，Class.getResource()，这又是怎么一回事呢？它又是从何处加载的呢？让我们一起来揭开它的神秘面纱吧。  
1. path不以’/'开头时，默认是从此类所在的包下取资源 
1. path  以’/'开头时，则是从ClassPath(即web项目的classes目录，java项目的bin目录)根下获取

我们有这样的目录结构：
```
project
    -src
        -package lab11
            -Demo.java
            -person.txt

```
下面这三种方式都可以获取到person.txt
``` java
package lab11;

public class Demo {
	public static void main(String[] args) {
		Class<Demo> demoClazz = Demo.class;
		ClassLoader classLoader = demoClazz.getClassLoader();
		String path = classLoader.getResource("lab11/person.txt").getPath();
		System.out.println(path);
		
		String path2 = demoClazz.getResource("person.txt").getPath();
		System.out.println(path2);
	
		String path3 = demoClazz.getResource("/lab11/person.txt").getPath();
		System.out.println(path3);
	}
}

```
这里，我们就对怎样通过classloader加载资源搞清楚了。鼓掌！