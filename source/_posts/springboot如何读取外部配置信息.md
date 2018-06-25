title: springboot如何读取外部配置信息
author: gsal
tags:
  - Spring
  - Java
categories:
  - Java
date: 2018-06-20 20:31:00
---
在resources文件夹下添加`datasource.properites`文件，对应properties中的属性写一个pojo。这个pojo将被注入，pojo上不用加@Component标注，因为我们等下用JavaConfig的方式统一进行。    
写一个Config类，比如叫PropertyConfig，加@Configuration，加@PropertySource("classpath:datasource.properties")，并配置一个PropertySourcesPlaceholderConfigurer，注意其@Bean的方法要是静态方法。为什么我也不知道，不是静态方法就会报错。  
思路是这样的，PropertySourcesPlaceholderConfigurer读取配置文件，并生成键值对，而Config类中的属性通过`@Value(${user})`的形式取得键值对的值，并注入到自己的变量中，而刚才写的那个pojo可以拿到这些值并注册到Spring容器中。

## 示例代码
``` java
@Configuration
@PropertySource("classpath:datasource.properties")
public class PropertyConfig {
	@Value("${user}")
	String user;
	@Value("${password}")
	String password;
	@Value("${url}")
	String url;
	
	//读取文件并生成键值对,PropertySourcesPlaceholderConfigurer
	@Bean
	public static PropertySourcesPlaceholderConfigurer configurer() {
		return new PropertySourcesPlaceholderConfigurer();
	}
	
	@Bean
	public DataSourceInfo dataSourceInfo() {
		DataSourceInfo info = new DataSourceInfo();
		info.setUser(user);
		info.setPassword(password);
		info.setUrl(url);
		return info;
	}
}


```
DataSourceInfo
``` java
package cn.kvmial.DIDemo.examplebean;

public class DataSourceInfo {
	private String user;
	private String password;
	private String url;
	public String getUser() {
		return user;
	}
	public void setUser(String user) {
		this.user = user;
	}
	public String getPassword() {
		return password;
	}
	public void setPassword(String password) {
		this.password = password;
	}
	public String getUrl() {
		return url;
	}
	public void setUrl(String url) {
		this.url = url;
	}
}

```