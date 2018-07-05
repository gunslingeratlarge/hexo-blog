title: springboot整合mybatis
author: gsal
tags:
  - springboot
categories:
  - java
date: 2018-07-02 18:58:00
---
# Springboot整合mybatis
## 配置application.properties
``` 
spring.datasource.url=jdbc:mysql://127.0.0.1:3306/how2java?characterEncoding=UTF-8
spring.datasource.username=root
spring.datasource.password=admin
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
```
## 增加对mysql和mybatis的支持
```
<!-- mybatis -->
<dependency>
	<groupId>org.mybatis.spring.boot</groupId>
	<artifactId>mybatis-spring-boot-starter</artifactId>
	<version>1.1.1</version>
</dependency>
<!-- mysql -->
<dependency>
	<groupId>mysql</groupId>
	<artifactId>mysql-connector-java</artifactId>
	<version>5.1.21</version>
</dependency>
```
## 创建pojo包和实体类

## 创建mapper包和Mapper接口
使用@Mapper和@Select注解。比如CategoryMapper其实就是将category.xml中的内容从xml中移动到了注解上。
## 创建处理请求的控制器
聚合了一个Mapper接口的实例化对象。（由框架帮我们实例化），处理请求的时候使用listCategory接受Request方法，调用聚合的Mapper接口的实例化对象中的对应方法。