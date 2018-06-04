title: 设计模式 part3 创建模式 下
author: gsal
tags:
  - Java
  - 设计模式
categories:
  - Java
date: 2018-05-15 10:34:00
---
## 建造者（builder）模式
### 理解
建造者模式其实是把一个复杂对象的各个部分的建造和这各个部分建造的顺序和整体的构建算法（如何体现？）。Director中封装的就是构建的过程，这些通常是不变的。

### 参与者
抽象建造者（Builder）：建造者的抽象接口。需要建造的对象里面有多少个零件就规定了多少个方法，除此之外再有一个RetrieveResult()方法，返回最终产品对象。  
指导者（Director）:负责与用户进行沟通，并实例化建造者。这样用户就不知道你要建造的对象是由几部分组成了。并且可以将构建的顺序放到director中。Director是知道你要先造什么，后造什么的。比如你要造一个机器人，这个机器人有四部分，builder只知道每部分怎么造，以及我怎么样将各部分组装到正确的位置，但是先造什么，后造什么，builder是不知道的。要靠Director来指挥。
具体建造者（ConcreteteBuilder）：实现了抽象建造者的build各个部分的方法。处理具体建造各部分的逻辑。
产品（Product）：需要建造的复杂对象
<!--more-->

### 示例代码
建造一个机器人，由四部分组成，机器人头，机器人身体，机器人脚，机器人手臂。
#### Director
``` java
public class Director {
    public void construct(RobotBuilder builder){
        builder.buildHead();
        builder.buildBody();
        builder.buildArms();
        builder.buildLegs();
    }
}
```
Director中负责构造的顺序（其实还有其他复杂的组建的逻辑，但是我不知道可以怎样体现出来），一定是先头->身体->手臂->脚的这个顺序。  
#### Builder
``` java
public abstract class RobotBuilder {
    public abstract void buildHead();
    public abstract void buildBody();
    public abstract void buildArms();
    public abstract void buildLegs();    
    public abstract Robot getRobot();
}
```

#### ConcreteBuilder
``` java
    public class LegoRobotBuilder extends RobotBuilder {
        Robot robot = new Robot();    
        public void buildHead(){
            System.out.println("组装头部");
            robot.add("头部");
        }
        public void buildBody(){
            System.out.println("组装身子");
            robot.add("身子");
        }
        public void buildArms(){
            System.out.println("组装手臂");
            robot.add("手臂");
        }
        public void buildLegs(){
            System.out.println("组装腿部");
            robot.add("腿部");
        }
        public Robot getRobot() {
             return robot;
        }
    
    }
```

## 原型模式
待续...

## 单例模式
### 特点
每一个类只能创造一个对象，将构造函数私有化，通过


## 多例模式