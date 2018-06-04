title: mybatis快速入门 part3
author: gsal
tags:
  - mybatis
  - java
categories:
  - java
date: 2018-05-07 15:38:00
---
### 关联查询
#### 一对多
查找所有的类别的商品。category类有：`id`，`name`，`List<Product>`,Product类有`name`，`id`。sql语句：
``` sql
select c.id 'cid', p.id 'pid', c.name 'cname', p.name 'pname', p.price price from category_  c left join product_  p on c.id = p.cid
```
注意关联查询on一定要写，不能不写条件，syntax层面就有问题。  
这里查询的返回结果使用resultMap来接收，因为对于这种关联查询的情况mybatis不能很好地对应到pojo中，需要你自己来写一下。  
Category.xml 
``` xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
    PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
 
    <mapper namespace="com.how2java.pojo">
    	<resultMap type="Category" id="categoryBean">
    		<id property="id" column="cid" />
			<result property="name" column="cname" />
			
			<collection property="products" ofType="Product">
				<id property="id" column="pid"/>
				<result property="name"  column="pname"/>
				<result property="price" column="price"/>
			</collection>
		</resultMap>	
		<select id="listCategory" resultMap="categoryBean">
			select c.id 'cid', p.id 'pid', c.name 'cname', p.name 'pname', p.price price from category_  c left join product_  p
			on c.id = p.cid
		</select>
    </mapper>
```

#### 多对一
Category对Product就是一对多，Product对Category自然就是多对一。基本上和上面的相差不大，注意的就是写xml的时候是用的`<association property="category" javaType="Category">`而不是collection，ofType也改为了javaType。


### 动态sql
比较简单，这里就给出一些代码段即可：    
#### 使用if标签  
改造UserMapper.xml，如下：  
``` xml
<!-- 根据条件查询用户 -->
<select id="queryUserByWhere" parameterType="user" resultType="user">
	SELECT id, username, birthday, sex, address FROM `user`
	WHERE 1=1
	<if test="sex != null and sex != ''">
		AND sex = #{sex}
	</if>
	<if test="username != null and username != ''">
		AND username LIKE
		'%${username}%'
	</if>
</select>
``` 
注意字符串类型的数据需要要做不等于空字符串校验。  


#### Where标签
上面的sql还有where 1=1 这样的语句，很麻烦
可以使用where标签进行改造

改造UserMapper.xml，如下  
``` xml
<!-- 根据条件查询用户 -->
<select id="queryUserByWhere" parameterType="user" resultType="user">
	SELECT id, username, birthday, sex, address FROM `user`
<!-- where标签可以自动添加where，同时处理sql语句中第一个and关键字 -->
	<where>
		<if test="sex != null">
			AND sex = #{sex}
		</if>
		<if test="username != null and username != ''">
			AND username LIKE
			'%${username}%'
		</if>
	</where>
</select>
```