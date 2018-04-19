title: Mybatis快速入门 part1
author: gsal
tags:
  - Java
  - Web开发
categories:
  - Java
date: 2018-03-17 16:13:00
---
### 一、Mybatis架构
![mybatis架构](/images/pasted-0.png)  

Mybatis中有一堆配置文件，SqlMapConfig.xml以及对应数据库中各个表的xml比如User.xml、Order.xml等等。其中SqlMapConfig.xml配置了mybatis的运行环境等信息，其他的配置文件映射到各个数据库表中主要配置操作数据库的sql语句。  
SqlSessionFactory用于创建会话SqlSession，通过SqlSession操作数据库，我们现在只需要知道通过SqlSession我们就可以进行增删查改操作了。  
<!--more-->
### 二、配置环境
Mybatis主要的配置工作就在我们前面说的几个xml文件中，除此之外就是常规的下载、导包。而由于mybatis是连接数据库的框架，从数据库中读出来的数据可以直接由mybatis帮我们导入到POJO类中（POJO类通常与数据库表的一个条目对应），因此还需要创建POJO类。
#### 下载并加入jar包
1.下载地址
[https://github.com/mybatis/mybatis-3/releases](https://github.com/mybatis/mybatis-3/releases)  
2.导入mybatis核心包mybatis-3.2.7.jar、依赖包（lib文件夹中所有内容）、数据库驱动包mysql-connetctor-java-5.1.7.bin.jar(不在mybatis压缩包中)

#### 创建pojo
一个pojo类通常与一个数据库表相对应
``` java
Public class User {
	private int id;
	private String username;// 用户姓名
	private String sex;// 性别
	private Date birthday;// 生日
	private String address;// 地址
    getter/setter..

```

#### 加入配置文件
我们先创建config文件夹，将这些配置文件放在下面
##### log4j  
mybatis默认使用log4j来输出日志。创建log4j.properties文件：
```
# Global logging configuration
log4j.rootLogger=DEBUG, stdout
# Console output...
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%5p [%t] - %m%n

```
##### SqlMapConfig.xml  
核心配置文件，配置的内容主要有数据源（结合Spring后就不必配置了）、事务处理。  
``` xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
	<!-- 和spring整合后 environments配置将废除 -->
	<environments default="development">
		<environment id="development">
			<!-- 使用jdbc事务管理 -->
			<transactionManager type="JDBC" />
			<!-- 数据库连接池 -->
			<dataSource type="POOLED">
				<property name="driver" value="com.mysql.jdbc.Driver" />
				<property name="url"
					value="jdbc:mysql://localhost:3306/mybatis?characterEncoding=utf-8" />
				<property name="username" value="root" />
				<property name="password" value="root" />
			</dataSource>
		</environment>
	</environments>
</configuration>

```

##### 添加User.xml
在config下创建sqlmap目录，其下添加sql映射文件User.xml。
User.xml中就是写我们对User表的sql语句，定义查询的一些规则。
``` xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!-- namespace：命名空间，用于隔离sql，还有一个很重要的作用，后面会讲 -->
<mapper namespace="test">
</mapper>

```
##### 加载映射文件
mybatis并不知道你的User.xml写到哪里去了，因此你需要在SqlMapConfig.xml中提一句。
``` xml
<mappers>
  <mapper resource = "sqlmap/User.xml"/>
</mappers>
```
### 三、实现业务
#### 根据用户id查询一个用户
要实现查询在mybatis中非常简单，因为mybatis可以直接帮我们映射到pojo中，因此我们只需要在User.xml中写sql语句就可以了。每一条sql语句有自己的id，有传入参数的类型（如这里的id是整型），有传出参数的类型。
```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!-- namespace：命名空间，用于隔离sql，还有一个很重要的作用，后面会讲 -->
<mapper namespace="test">
	<!-- id:statement的id 或者叫做sql的id-->
	<!-- parameterType:声明输入参数的类型 -->
	<!-- resultType:声明输出结果的类型，应该填写pojo的全路径 -->
	<!-- #{}：输入参数的占位符，相当于jdbc的？ -->
	<select id="queryUserById" parameterType="int"
		resultType="cn.itcast.mybatis.pojo.User">
		SELECT * FROM `user` WHERE id  = #{id}
	</select>
</mapper>

```
实际上到这里，根据用户id查询一个用户就搞完了，下面我们需要进行一下测试。正如我们一开始说的： *SqlSessionFactory用于创建会话SqlSession，通过SqlSession操作数据库，我们现在只需要知道通过SqlSession我们就可以进行增删查改操作了。*   
测试程序的步骤：
 1. 创建SqlSessionFactoryBuilder对象
 2. 加载SqlMapConfig.xml配置文件
 3. 创建SqlSessionFactory对象
 4. 创建SqlSession对象
 5. 执行SqlSession对象执行查询，获取结果User
 6. 打印结果
 7. 释放资源
``` java
public class MybatisTest {
    private SqlSessionFactory sqlSessionFactory = null;

    @Before
    public void init() throws Exception {
        // 1. 创建SqlSessionFactoryBuilder对象
        SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();

        // 2. 加载SqlMapConfig.xml配置文件(这里要把config文件夹设置为scource folder)
        InputStream inputStream = Resources.getResourceAsStream("SqlMapConfig.xml");

        // 3. 创建SqlSessionFactory对象
        this.sqlSessionFactory = sqlSessionFactoryBuilder.build(inputStream);
    }

    @Test
    public void testQueryUserById() throws Exception {
        // 4. 创建SqlSession对象
        SqlSession sqlSession = sqlSessionFactory.openSession();

        // 5. 执行SqlSession对象执行查询，获取结果User
        // 第一个参数是User.xml的statement的id，第二个参数是执行sql需要的参数；
        Object user = sqlSession.selectOne("queryUserById", 1);

        // 6. 打印结果
        System.out.println(user);

        // 7. 释放资源
        sqlSession.close();
    }
}
```
#### 根据用户名模糊查询一个用户
主要涉及到模糊查询的处理，在mybatis中#{}表示占位符，可以防止sql注入，${}表示拼接字符串，不能防止sql注入。现在我们要模糊查询，因此可以写
``` sql
select * from user where username like "%"#{username}"%"
```
注意，这里是一种防止了sql注入，又可以直接模糊查询的一种方式。这时候我们使用`selectList`而非`selectOne`来进行查询，返回的是`List<Object>` 。其他地方大同小异。一个小细节要注意的是如果传入的参数是基本类型（整型、字符串），那么可以在#{}中写任意字符，但是如果是传入pojo，那只能写value。

#### 增加一个用户
User.xml中的配置
``` xml
	<!-- 保存用户 -->
	<insert id="saveUser" parameterType="cn.itcast.mybatis.pojo.User">
		INSERT INTO `user`
		(username,birthday,sex,address) VALUES
		(#{username},#{birthday},#{sex},#{address})
	</insert>
```
测试程序
``` java
@Test
public void testSaveUser() {
	// 4. 创建SqlSession对象
	SqlSession sqlSession = sqlSessionFactory.openSession();

	// 5. 执行SqlSession对象执行保存
	// 创建需要保存的User
	User user = new User();
	user.setUsername("张飞");
	user.setSex("1");
	user.setBirthday(new Date());
	user.setAddress("蜀国");

	sqlSession.insert("saveUser", user);
	System.out.println(user);

	// 需要进行事务提交
	sqlSession.commit();

	// 7. 释放资源
	sqlSession.close();
}

```
其他的增删查改都大同小异，不再赘述。