title: 设计模式 part4  结构模式 （上）
author: gsal
tags:
  - Java
  - 设计模式
categories:
  - Java
date: 2018-05-23 12:06:00
---
## 分类
适配器 桥接 组合 装饰者

## 适配器（Adapter）
把一个类的接口变换成客户端所期待的另一种接口，使因接口不匹配的类能一起工作。  
变压器模式——把一种电压变换成另一种电压。或者类似我们出国用的插口转换器。  
又如HDMI转VGA。又叫包装模式，wrapper模式。一般是将被适配者包在里面。  
### 分类
类适配器：需要利用多重继承
对象适配器：Adapter把Adaptee包进来，聚合进来。用得比较广泛。比类适配器用得更多。因为多重继承是不太好的。
<!--more-->

### 使用条件
希望使用一个现有已存在的类，它的接口又与希望的接口不匹配。要产生一个中间类。
### 例子  


## 桥模式（Bridge）
将抽象化与实现化脱耦。用组合聚合来代替继承关系，从而使两者可以相对独立地变化。实际上就是对变化的封装。  
桥模式和适配器模式有什么区别？ 感觉都有聚合/组合，即包含的关系。 两种变化维度就要考虑桥模式。就会设计到排列组合。比如加奶咖啡和不加奶咖啡，大杯咖啡，小贝咖啡。  
一个好的设计没有两个以上的变化因素。

## 组合模式
抽象节点 树叶节点 树枝节点  
树枝节点可能含有其他的树叶节点或者树枝节点。
树叶节点就像文件，树枝节点可能包含树叶和其他树枝节点。树枝节点就像文件夹，可以包含其他文件和文件夹。组合模式有一点递归的感觉。 组合模式最常见的也是最好理解的例子就是文件夹了。
https://www.cnblogs.com/lfxiao/p/6816026.html 

### 装饰者模式
是继承关系的一个替代方案。在不创造更多子类的情况下，将对象的功能加以扩展。给对象而不是类添加功能。扩展功能更为灵活。比如给Text对象加边框或者加滚动条。

在运行时改变对象的行为。装饰者has a 并且是 is a。  
从奶茶的例子讲起，如果你有很多款奶茶，你怎么样实现给奶茶添加很多的料，比如奶盖，波霸，珍珠，加不加冰之类的？如果你使用继承，那么你会有很多子类，并且会有子类的排列组合，比如奶盖奶茶，波霸奶茶，奶盖波霸奶茶，奶盖珍珠奶茶。。会造成类爆炸的现象。  
所以这个时候就是装饰者模式出手了。 装饰者模式就是一层套一层的，每一层都是上一层的装饰。看这张uml图：


![uml图](/images/decorator.png)
在装饰者模式中，所有的类都是Tea（也就是Component），而装饰者也是Tea，并且装饰者里面有变量Tea，这样的好处是一层包一层，装饰者里面包的Tea也可以是装饰者。而不同的具体装饰者就可以有不同的处理方法，比如说定不同的价格。但是实际上装饰者模式更适用于每一个装饰者与装饰者之间差别比较大，不仅仅是值的差别的时候。如果像我们给出的这个例子只是价格不同的话，其实在创建Tea的时候传入一个addons的列表就可以了，然后在Tea中计算价格的时候就遍历一下这个列表就可以了。  
这里我们还是回到我们的这个例子，我们要一个加冰的MilkTea的时候，就将MilkTea传入IceDecorator，如果还要加珍珠，就将上一个IceDecorator传入到PearlDecorator里面就可以了。更详细的内容请看https://www.youtube.com/watch?v=GCraGHx6gso。

## 组合模式示例代码
``` java
1.AbstractFile.java
package hm4;

public abstract class AbstractFile {
	public void add(AbstractFile abstractFile) {
		throw new UnsupportedOperationException("不支持该操作");
	};
	public void remove(AbstractFile abstractFile) {
		throw new UnsupportedOperationException("不支持该操作");
	}
	public AbstractFile getChild(int i) {
		throw new UnsupportedOperationException("不支持该操作");
	}
	//对文件执行操作
	public abstract void operation();
}


2.File.java
package hm4;

public class File extends AbstractFile{
	private String name;
	public File(String name) {
		this.name = name;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	@Override
	public void operation() {
		System.out.println("对文件"+ name +"执行相应操作");
	}

}


3.Folder.java
package hm4;

import java.util.ArrayList;
import java.util.List;

public class Folder extends AbstractFile {
	
	List<AbstractFile> files = new ArrayList<>();
	private String name;
	
	public Folder(String name) {
		this.name = name;
	}
	public List<AbstractFile> getFiles() {
		return files;
	}

	public void setFiles(List<AbstractFile> files) {
		this.files = files;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	@Override
	public void add(AbstractFile file) {
		files.add(file);
	}
	
	@Override
	public void remove(AbstractFile file) {
		files.remove(file);
	}
	
	public AbstractFile get(int i) {
		return files.get(i);
	}
	
	
	@Override
	public void operation() {
		System.out.println("对文件夹" + name + "进行操作！");
		for (AbstractFile file : files) {
			file.operation();
		}
	}
}


4.Test.java
package hm4;
public class Test {
	public static void main(String[] args) {
		AbstractFile totalFolder = new Folder("总文件夹");
		
		AbstractFile folder1 = new Folder("文本文件");
		AbstractFile folder2 = new Folder("图片文件");
		
		AbstractFile img1 = new File("图片1");
		AbstractFile img2 = new File("图片2");
		
		AbstractFile txt1 = new File("文本1");
		AbstractFile txt2 = new File("文本2");
		
		
		folder1.add(txt1);
		folder1.add(txt2);
		
		folder2.add(img1);
		folder2.add(img2);
		
		totalFolder.add(folder1);
		totalFolder.add(folder2);
		
		totalFolder.operation();
	}
}

```

## 装饰者模式示例代码
``` java

1.AbstractDbOperator.java
package hm4_2;

public abstract class AbstractDbOperator {
	public abstract void query();
	public abstract void insert();
	public abstract void delete();
	public abstract void update();
}

2.DbOperator.java
package hm4_2;

public class DbOperator extends AbstractDbOperator{
	@Override
	public void query() {
		System.out.println("对数据库进行查询...");
	}
	@Override
	public void insert() {
		System.out.println("对数据库进行插入...");
	}
	@Override
	public void update() {
		System.out.println("对数据库进行更新...");
	}
	@Override
	public void delete() {
		System.out.println("对数据库进行删除...");
	}
}

3.AbstractDecorator.java
package hm4_2;

public abstract class AbstractDecorator extends AbstractDbOperator {
	private AbstractDbOperator operator;
	
	
	public AbstractDecorator(AbstractDbOperator operator) {
		this.operator = operator;
	}
	
	
	@Override
	public void query() {
		operator.query();
	}
	@Override
	public void insert() {
		operator.insert();
	}
	@Override
	public void update() {
		operator.update();
	}
	@Override
	public void delete() {
		operator.delete();
	}

}

4.CacheDecorator.java
package hm4_2;

public class CacheDecorator extends AbstractDecorator{

	public CacheDecorator(AbstractDbOperator operator) {
		super(operator);
	}
	
	@Override
	public void query() {
		System.out.println("进行缓存查询...");
		//如果缓存中没有，再进行这一步
		super.query();
	}

}

5.SafeDecorator.java
package hm4_2;

public class SafeDecorator extends AbstractDecorator{

	public SafeDecorator(AbstractDbOperator operator) {
		super(operator);
	}

	@Override
	public void update() {
		System.out.println("进行安全检查...");
		super.update();
	}
}

6.Test.java
package hm4_2;

public class Test {
	public static void main(String[] args) {
		AbstractDbOperator operator = new DbOperator();
		AbstractDecorator cached = new CacheDecorator(operator);
		cached.query();
		cached.update();
		
		AbstractDecorator safeCheck = new SafeDecorator(cached);
		safeCheck.query();
		safeCheck.update();
	}
}



```