title: Mybatis快速入门 part1
author: gsal
tags:
  - Java
  - Web开发
categories:
  - Java
date: 2018-03-17 16:13:00
---
#### 一、Mybatis架构
![mybatis架构](/images/pasted-0.png)  

Mybatis中有一堆配置文件，SqlMapConfig.xml以及对应数据库中各个表的xml比如User.xml、Order.xml等等。其中SqlMapConfig.xml配置了mybatis的运行环境等信息，其他的配置文件映射到各个数据库表中主要配置操作数据库的sql语句。  
SqlSessionFactory用于创建会话SqlSession，通过SqlSession操作数据库，我们现在只需要知道通过SqlSession我们就可以进行增删查改操作了。  
<!--more-->
#### 二、配置环境
Mybatis主要的配置工作就在我们前面说的几个xml文件中，除此之外就是常规的下载、导包。而由于mybatis是连接数据库的框架，从数据库中读出来的数据可以直接由mybatis帮我们导入到POJO类中（POJO类通常与数据库表的一个条目对应），因此还需要创建POJO类。
##### 下载并加入jar包
1.下载地址
[https://github.com/mybatis/mybatis-3/releases](https://github.com/mybatis/mybatis-3/releases)  
2.导入mybatis核心包mybatis-3.2.7.jar、依赖包（lib文件夹中所有内容）、数据库驱动包mysql-connetctor-java-5.1.7.bin.jar(不在mybatis压缩包中)
