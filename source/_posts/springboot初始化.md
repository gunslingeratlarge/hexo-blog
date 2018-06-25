title: springboot初始化
author: gsal
tags:
  - Spring
  - ''
  - Java
categories:
  - Java
date: 2018-06-20 12:30:00
---
## 配置
springboot几乎不需要配置，它其实就是使用了spring加springmvc加hibernate，最终功能还是它们在做，只是boot确实开始起来很方便。只需要到`start.spring.io`选择对应的版本就可以了。选择maven版本直接导入到eclipse中就可以直接运行了。
springboot内置了tomcat，并且可以直接打包成jar包运行，可以把它当做是一个java应用，因为它的入口就是main方法。

## 注意
有一些版本号启动之后内置的tomcat不会启动，现在能用的有1.5.9.RELEASE，2.0.0.RELEASE，具体是什么原因我也不太清楚。我也是自己摸索出来的。还有注意在启动之后企图访问thymeleaf模板的时候可能会报ClassNotFoundException，这是用反射加载某个类的时候找不到这个类报的错，仔细阅读它的错误日志会发现是有一个thymeleaf的依赖没有添加进去，所以在maven仓库中找到这个依赖然后在pom.xml中添加进去就可以了。本来按理说只要添加了thymeleaf的依赖就可以了，我也不知道怎么回事。反正这样子是可以解决这个问题了暂时。  
springboot只从main方法所在的类往下找component，所以注解的时候要注意。

## 注解
springboot里似乎没有spring里面的ApplicationContext.xml这个配置文件了，很多都是使用注解完成配置了。确实也比较方便也更加直观。`@Controller`可以配合`@RequestMapping({"/",""})`使用，而只需要一个public方法就可以完成视图与model的绑定。这比之前springmvc的`ModelAndView`要方便不少了。

``` java
@Controller
public class JokeController {
	@RequestMapping("/joke")
	public String showJoke(Model model) {
		model.addAttribute("message", "here you are");
		return "joke";
	}
}

```
返回的虽然只是一个“joke”字符串，但是springboot就会去找`joke.html`,然后将该填充的内容填充进去。

## 一个可以使用的pom.xml
``` xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>guru.springframework.joke</groupId>
	<artifactId>jokeapp</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<packaging>jar</packaging>

	<name>jokeapp</name>
	<description>Joke App for Spring Boot</description>

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.0.0.RELEASE</version>
		<relativePath /> <!-- lookup parent from repository -->
	</parent>

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
		<java.version>1.8</java.version>
	</properties>

	<dependencies>
		<!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-thymeleaf -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-thymeleaf</artifactId>
			<version>2.0.3.RELEASE</version>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
		<!-- https://mvnrepository.com/artifact/org.attoparser/attoparser -->
		<dependency>
			<groupId>org.attoparser</groupId>
			<artifactId>attoparser</artifactId>
			<version>2.0.5.RELEASE</version>
		</dependency>
		<!-- https://mvnrepository.com/artifact/org.unbescape/unbescape -->
		<dependency>
			<groupId>org.unbescape</groupId>
			<artifactId>unbescape</artifactId>
			<version>1.1.6.RELEASE</version>
		</dependency>

	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>

	<repositories>
		<repository>
			<id>spring-snapshots</id>
			<name>Spring Snapshots</name>
			<url>https://repo.spring.io/snapshot</url>
			<snapshots>
				<enabled>true</enabled>
			</snapshots>
		</repository>
		<repository>
			<id>spring-milestones</id>
			<name>Spring Milestones</name>
			<url>https://repo.spring.io/milestone</url>
			<snapshots>
				<enabled>false</enabled>
			</snapshots>
		</repository>
	</repositories>

	<pluginRepositories>
		<pluginRepository>
			<id>spring-snapshots</id>
			<name>Spring Snapshots</name>
			<url>https://repo.spring.io/snapshot</url>
			<snapshots>
				<enabled>true</enabled>
			</snapshots>
		</pluginRepository>
		<pluginRepository>
			<id>spring-milestones</id>
			<name>Spring Milestones</name>
			<url>https://repo.spring.io/milestone</url>
			<snapshots>
				<enabled>false</enabled>
			</snapshots>
		</pluginRepository>
	</pluginRepositories>


</project>


```