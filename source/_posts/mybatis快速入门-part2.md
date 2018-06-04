title: mybatis快速入门 part2
author: gsal
tags:
  - mybatis
  - java
categories:
  - java
date: 2018-05-07 11:01:00
---
### DAO开发
刚才part1只是实现了对数据库的处理，还没有涉及到DAO层。我们如何引入DAO开发呢。首先，我们之前的每个方法都是现开一个SqlSessionFactoryBuilder,SqlSessionFactory,SqlSession,这里我们就把Factory提出来放到类的变量中去，类的方法新开一个SqlSession即可。（注意SqlSession不能共享受用，是线程不安全的。）
``` java 
SqlSession session = sqlSessionFactory.openSession();
try {
	 // do work
} finally {
	session.close();
}
```
<!--more-->
这里对dao开发不再赘述了，实际使用的也不多。只是这里给一些代码供不时之需。  
**Dao接口**

``` java
public interface UserDao {
	/**
	 * 根据id查询用户
	 * 
	 * @param id
	 * @return
	 */
	User queryUserById(int id);

	/**
	 * 根据用户名模糊查询用户
	 * 
	 * @param username
	 * @return
	 */
	List<User> queryUserByUsername(String username);

	/**
	 * 保存用户
	 * 
	 * @param user
	 */
	void saveUser(User user);
}

```
**DAO实现**
``` java
public class UserDaoImpl implements UserDao {
	private SqlSessionFactory sqlSessionFactory;

	public UserDaoImpl(SqlSessionFactory sqlSessionFactory) {
		super();
		this.sqlSessionFactory = sqlSessionFactory;
	}

	@Override
	public User queryUserById(int id) {
		// 创建SqlSession
		SqlSession sqlSession = this.sqlSessionFactory.openSession();
		// 执行查询逻辑
		User user = sqlSession.selectOne("queryUserById", id);
		// 释放资源
		sqlSession.close();

		return user;
	}

	@Override
	public List<User> queryUserByUsername(String username) {
		// 创建SqlSession
		SqlSession sqlSession = this.sqlSessionFactory.openSession();

		// 执行查询逻辑
		List<User> list = sqlSession.selectList("queryUserByUsername", username);
		// 释放资源
		sqlSession.close();
		return list;
	}

	@Override
	public void saveUser(User user) {
		// 创建SqlSession
		SqlSession sqlSession = this.sqlSessionFactory.openSession();

		// 执行保存逻辑
		sqlSession.insert("saveUser", user);
		// 提交事务
		sqlSession.commit();
		// 释放资源
		sqlSession.close();
	}
}
```

**DAO测试**  
测试一下DAO接口
``` java
public class UserDaoTest {
	private SqlSessionFactory sqlSessionFactory;

	@Before
	public void init() throws Exception {
		// 创建SqlSessionFactoryBuilder
		SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
		// 加载SqlMapConfig.xml配置文件
		InputStream inputStream = Resources.getResourceAsStream("SqlMapConfig.xml");
		// 创建SqlsessionFactory
		this.sqlSessionFactory = sqlSessionFactoryBuilder.build(inputStream);
	}

	@Test
	public void testQueryUserById() {
		// 创建DAO
		UserDao userDao = new UserDaoImpl(this.sqlSessionFactory);
		// 执行查询
		User user = userDao.queryUserById(1);
		System.out.println(user);
	}

	@Test
	public void testQueryUserByUsername() {
		// 创建DAO

		UserDao userDao = new UserDaoImpl(this.sqlSessionFactory);
		// 执行查询
		List<User> list = userDao.queryUserByUsername("张");
		for (User user : list) {
			System.out.println(user);
		}
	}

	@Test
	public void testSaveUser() {
		// 创建DAO
		UserDao userDao = new UserDaoImpl(this.sqlSessionFactory);

		// 创建保存对象
		User user = new User();
		user.setUsername("刘备");
		user.setBirthday(new Date());
		user.setSex("1");
		user.setAddress("蜀国");
		// 执行保存
		userDao.saveUser(user);

		System.out.println(user);
	}
}
```

### Mapper动态代理
动态代理是什么?（先不管了）

在src文件夹下创建mapper包，里面的结构是这样的，一个xml文件对应一个类文件，比如UserMapper.java和UserMapper.xml，是一一对应的，UserMapper是一个接口，里面定义了关于所有的对User的数据库操作的方法，比如FindUserById，FindUserByUsername等等。除此之外还需要遵守以下规范：
1、Mapper.xml文件中的namespace与mapper接口的类路径相同。  
2、Mapper接口方法名和Mapper.xml中定义的每个statement的id相同   
3、Mapper接口方法的输入参数类型和mapper.xml中定义的每个sql的parameterType的类型相同  
4、Mapper接口方法的输出参数类型和mapper.xml中定义的每个sql的resultType的类型相同  

时间关系，这里仅仅给出代码段  
**UserMapper.xml**  
``` xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!-- namespace：命名空间，用于隔离sql -->
<!-- 还有一个很重要的作用，使用动态代理开发DAO，1. namespace必须和Mapper接口类路径一致 -->
<mapper namespace="cn.itcast.mybatis.mapper.UserMapper">
	<!-- 根据用户id查询用户 -->
	<!-- 2. id必须和Mapper接口方法名一致 -->
	<!-- 3. parameterType必须和接口方法参数类型一致 -->
	<!-- 4. resultType必须和接口方法返回值类型一致 -->
	<select id="queryUserById" parameterType="int"
		resultType="cn.itcast.mybatis.pojo.User">
		select * from user where id = #{id}
	</select>

	<!-- 根据用户名查询用户 -->
	<select id="queryUserByUsername" parameterType="string"
		resultType="cn.itcast.mybatis.pojo.User">
		select * from user where username like '%${value}%'
	</select>

	<!-- 保存用户 -->
	<insert id="saveUser" parameterType="cn.itcast.mybatis.pojo.User">
		<selectKey keyProperty="id" keyColumn="id" order="AFTER"
			resultType="int">
			select last_insert_id()
		</selectKey>
		insert into user(username,birthday,sex,address) values
		(#{username},#{birthday},#{sex},#{address});
	</insert>

</mapper>
```
**UserMapper(接口文件）**
``` xml
public interface UserMapper {
	/**
	 * 根据id查询
	 * 
	 * @param id
	 * @return
	 */
	User queryUserById(int id);

	/**
	 * 根据用户名查询用户
	 * 
	 * @param username
	 * @return
	 */
	List<User> queryUserByUsername(String username);

	/**
	 * 保存用户
	 * 
	 * @param user
	 */
	void saveUser(User user);
}
``` 
在SqlMapConfig.xml主配置文件里面，加载UserMapper.xml文件：
``` xml
	<!-- 加载映射文件 -->
	<mappers>
		<mapper resource="sqlmap/User.xml" />
      <!--这句是新加的，上面那句其实就没用了-->
		<mapper resource="mapper/UserMapper.xml" />
	</mappers>
```
测试：
``` java
public class UserMapperTest {
	private SqlSessionFactory sqlSessionFactory;

	@Before
	public void init() throws Exception {
		// 创建SqlSessionFactoryBuilder
		SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
		// 加载SqlMapConfig.xml配置文件
		InputStream inputStream = Resources.getResourceAsStream("SqlMapConfig.xml");
		// 创建SqlsessionFactory
		this.sqlSessionFactory = sqlSessionFactoryBuilder.build(inputStream);
	}

	@Test
	public void testQueryUserById() {
		// 获取sqlSession，和spring整合后由spring管理
		SqlSession sqlSession = this.sqlSessionFactory.openSession();

		// 从sqlSession中获取Mapper接口的代理对象
		UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
		// 执行查询方法
		User user = userMapper.queryUserById(1);
		System.out.println(user);

		// 和spring整合后由spring管理
		sqlSession.close();
	}

	@Test
	public void testQueryUserByUsername() {
		// 获取sqlSession，和spring整合后由spring管理
		SqlSession sqlSession = this.sqlSessionFactory.openSession();

		// 从sqlSession中获取Mapper接口的代理对象
		UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
		// 执行查询方法
		List<User> list = userMapper.queryUserByUsername("张");
		for (User user : list) {
			System.out.println(user);
		}

		// 和spring整合后由spring管理
		sqlSession.close();
	}

	@Test
	public void testSaveUser() {
		// 获取sqlSession，和spring整合后由spring管理
		SqlSession sqlSession = this.sqlSessionFactory.openSession();

		// 从sqlSession中获取Mapper接口的代理对象
		UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
		// 创建保存对象
		User user = new User();
		user.setUsername("刘备");
		user.setBirthday(new Date());
		user.setSex("1");
		user.setAddress("蜀国");
		// 执行查询方法
		userMapper.saveUser(user);
		System.out.println(user);


		// 和spring整合后由spring管理
		sqlSession.commit();
		sqlSession.close();
	}
}
```
### mapper配置的几种方式

`<mapper resource=" " />  `
使用相对于类路径的资源（现在的使用方式）  
如：`<mapper resource="sqlmap/User.xml" />  `
 
<mapper class=" " />  
使用mapper接口类路径  
如：`<mapper class="cn.itcast.mybatis.mapper.UserMapper"/> ` 

注意：此种方法要求mapper接口名称和mapper映射文件名称相同，且放在同一个目录中。  

`<package name=""/>  `
注册指定包下的所有mapper接口  
如：`<package name="cn.itcast.mybatis.mapper"/>  `
注意：此种方法要求mapper接口名称和mapper映射文件名称相同，且放在同一个目录中。