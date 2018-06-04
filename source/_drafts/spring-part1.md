title: 'spring part1 '
author: gsal
date: 2018-05-08 10:54:26
tags:
---
## IOC/DI
### 概念
IOC：控制反转，由spring帮你管理对象，而不用你自己去new了  
DI:依赖注入，简单地理解就是spring拿给你的对象已经提前注入好值了  
<!--more-->
### 步骤
#### 导包
非常简单，不多说了
#### 配置文件
spring的配置文件名为`applicationContext.xml`,放在src文件夹下即可。配置时采用模板，直接复制下面这段即可，不用自己去写模板的约束。
``` xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:aop="http://www.springframework.org/schema/aop"
    xmlns:tx="http://www.springframework.org/schema/tx" xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="
   http://www.springframework.org/schema/beans
   http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
   http://www.springframework.org/schema/aop
   http://www.springframework.org/schema/aop/spring-aop-3.0.xsd
   http://www.springframework.org/schema/tx
   http://www.springframework.org/schema/tx/spring-tx-3.0.xsd
   http://www.springframework.org/schema/context     
   http://www.springframework.org/schema/context/spring-context-3.0.xsd">
  
  <bean name="c" class="cn.how2java.pojo.Category">
    <property name="name" value="sports and gym"></property>
  </bean>  
</beans>
```
要获得spring帮你管理的对象使用如下代码：
``` java
ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
Category c = (Category)context.getBean("c");

```
非常简单，不要纠结于细节。

## 注入对象
要把对象注入，比如把Category注入到Product中，就要先让spring管理Category对象，再在Product对象的bean中写`<property name="category" ref="c">`,即可。
``` xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:aop="http://www.springframework.org/schema/aop"
    xmlns:tx="http://www.springframework.org/schema/tx" xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="
   http://www.springframework.org/schema/beans
   http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
   http://www.springframework.org/schema/aop
   http://www.springframework.org/schema/aop/spring-aop-3.0.xsd
   http://www.springframework.org/schema/tx
   http://www.springframework.org/schema/tx/spring-tx-3.0.xsd
   http://www.springframework.org/schema/context     
   http://www.springframework.org/schema/context/spring-context-3.0.xsd">
 
    <bean name="c" class="com.how2java.pojo.Category">
        <property name="name" value="category 1" />
    </bean>
    <bean name="p" class="com.how2java.pojo.Product">
        <property name="name" value="product1" />
        <property name="category" ref="c" />
    </bean>
 
</beans>
```
## 注解方式注入
### 注入对象
现在想要将Product中的category使用注解注入，在配置中改为`<context:annotation-config/>`，并注释掉` <property name="category" ref="c" />`,即
``` xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:aop="http://www.springframework.org/schema/aop"
    xmlns:tx="http://www.springframework.org/schema/tx" xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="
   http://www.springframework.org/schema/beans 
   http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
   http://www.springframework.org/schema/aop 
   http://www.springframework.org/schema/aop/spring-aop-3.0.xsd
   http://www.springframework.org/schema/tx 
   http://www.springframework.org/schema/tx/spring-tx-3.0.xsd
   http://www.springframework.org/schema/context      
   http://www.springframework.org/schema/context/spring-context-3.0.xsd">
 
 	<context:annotation-config/>
    <bean name="c" class="com.how2java.pojo.Category">
        <property name="name" value="category 1" />
    </bean>
    <bean name="p" class="com.how2java.pojo.Product">
        <property name="name" value="product1" />
<!--         <property name="category" ref="c" /> -->
    </bean>
 
</beans>

```
然后在java代码中，在需要注入的位置加上`@Autowired`即可，或者使用`@Resource(name="c")`
``` java
package com.how2java.pojo;
 
import org.springframework.beans.factory.annotation.Autowired;
 
public class Product {
 
    private int id;
    private String name;
    @Autowired
    private Category category;
 
    public int getId() {
        return id;
    }
 
    public void setId(int id) {
        this.id = id;
    }
 
    public String getName() {
        return name;
    }
 
    public void setName(String name) {
        this.name = name;
    }
 
    public Category getCategory() {
        return category;
    }
 
    public void setCategory(Category category) {
        this.category = category;
    }
}
```
### 注入组件
上面只是把Product中的category对象注入了，如果我们想Product也自动注入应该怎么办呢？使用`@Component("p")`