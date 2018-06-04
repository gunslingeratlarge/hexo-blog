title: java构造方法调用
author: gsal
tags:
  - Java
categories:
  - Java
date: 2018-06-02 16:36:00
---
无论是子类的有参构造方法还是无参构造方法都会隐含调用（如果没有显式调用）super()即父类的无参构造方法。请看代码示例。
``` java
package superDemo;

public class Father {
	public Father() {
		System.out.println("father的无参数构造方法");
	}
	
	public Father(String name) {
		System.out.println("Father的有参构造方法");
	}
}



package superDemo;

public class Son extends Father{
	public Son() {
		System.out.println("son的无参构造方法");
	}
	
	public Son(String name) {
		System.out.println("son的有参构造方法");
	}
	
	
	public static void main(String[] args) {
		Son s = new Son("son's name");
	}
}

```

输出为：
```
father的无参数构造方法  
son的有参构造方法  
```