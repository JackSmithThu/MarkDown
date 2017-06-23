---
title: Eclipse搭建SpringBoot（三）用Mybatis连接MySQL数据库
date: 2017-06-23 22:55:05
tags: [Eclipse,SpringBoot,Mybatis,MySQL]
categories: 技术

---

## 1.搭建 MySQL 数据库

安装 MySQL 的步骤就不在此赘述了，如果没接触过 MySQL 安装的话，可以参考：

[单台windows搭建mysql主从机备份](https://jacksmiththu.github.io/2017/04/08/%E5%8D%95%E5%8F%B0windows%E6%90%AD%E5%BB%BAmysql%E4%B8%BB%E4%BB%8E%E6%9C%BA%E5%A4%87%E4%BB%BD/)

现在我们已经有了一个可用的数据库，以及 root 的密码（我使用的密码是：root）。

### 1.1.登录账户

**Win** + **R** 输入 **cmd** 敲回车，打开一个终端，在里面输入：

```
> mysql -uroot -proot 
```

![](E:/images/201706230003.png)

### 1.2.建立数据库

在终端输入：

```
> create database yun;
```

![](E:/images/201706230004.png)

因为我之前已经建立过了，所以会显示 **ERROR**。正常情况下应该显示：

```
Query OK, 1 row affected (0.01 sec) 
```

查询一下，输入：

```
> show databases;
```

![](E:/images/201706230005.png)

可以看到我们刚才建立的数据库 **yun**。

### 1.3.建立对应表

有了数据库，我们还需要建立响应的表并插入数据，这样才能够查询。为此我们先简历一张表：

```
> use yun;
> CREATE TABLE IF NOT EXISTS user (id INT(11),name VARCHAR(255));
```

![](E:/images/201706230006.png)

### 1.4.向表中插入数据

在终端输入：

```
> INSERT INTO user (id,name) values(1,"Jackie");
> INSERT INTO user (id,name) values(2,"Nacu");
```

![](E:/images/201706230007.png)

查询一下表中是否有插入的数据：

```
> SELECT * FROM user;
```

![](E:/images/201706230008.png)

到了这一步，数据库就搭建完成了。接下来我们来搭建网站。



## 2.向 SpringBoot 集成 Mybatis

### 2.1.修改 src/main/java 目录和添加文件

按照下图的修改项目的包名，并添加新的包和类。

![](E:/images/201706230009.png)

#### 2.1.1.YunAppalication.java 

将 **YunAppalication.java** 所在的包名修改成 **com.yun** ，并修改其内容如下所示：

```
package com.yun;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;

import com.yun.service.UserService;

/*
 * 控制器使用示例
 */

@SpringBootApplication
@Controller
public class YunApplication {

	public static void main(String[] args) {
		SpringApplication.run(YunApplication.class, args);
	}
	
	/*
	 * 返回字符串
	 * 调用示例 : localhost:8080
	 */
	@ResponseBody
	@RequestMapping("/")
	public String greeting() {	
		return "Hello World!";
	}
	
	/*
	 * 不带参数的模版渲染
	 * 调用示例 : localhost:8080/index
	 */
	@RequestMapping("/index")
	public String index() {	
		return "index";
	}
	
	/*
	 * 带参数的模版渲染（/风格）
	 * 调用示例 : localhost:8080/hello/1/jackie
	 */
	@RequestMapping("/hello/{id}/{name}")
    public String hello(@PathVariable("id") String id, @PathVariable("name") String name,Model model) {
        model.addAttribute("id", id);
        model.addAttribute("name", name);
        return "hello";
    }
	
	/*
	 * 带参数的模版渲染（?风格）
	 * 调用示例 : http://localhost:8080/hello?id=1&name=jackie
	 */
    @RequestMapping(value = "/hello")
    public String hello2(@RequestParam String id, @RequestParam String name, Model model) {
    	model.addAttribute("id", id);
    	model.addAttribute("name", name);
        return "hello";
    }
    
    @Autowired
    private UserService userService;
    @ResponseBody
    @RequestMapping("/database/{id}")
    public String hello(@PathVariable("id") Integer id) {

        return userService.getById(id).getName();
    }
}
```

#### 2.1.2.User.java

**User.java** 是一个实体类，一般来说，实体类会被放在 **mode**l 包下。如我们的 **User.java** 就被放到了 **com.yun.model** 包下，其内容如下所示：

```
package com.yun.model;

public class User {
    private Integer id;
    private String name;
    public Integer getId() {
        return id;
    }
    public void setId(Integer id) {
        this.id = id;
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
}
```

#### 2.1.3.UserMapper.java

**UserMapper.java** 是一个 **Mapper**（用于映射 **model** 中的实体类与数据库的查询结果）。其一半位于 **mapper** 包或者 **dao** 包下，我们就把它放在 **com.yun.mapper** 包下。其内容如下所示：

```
package com.yun.mapper;

import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Param;
import org.apache.ibatis.annotations.Result;
import org.apache.ibatis.annotations.Results;
import org.apache.ibatis.annotations.Select;

import com.yun.model.User;

@Mapper 
public interface UserMapper {
    @Select("SELECT * FROM user WHERE user.id = #{id}")
    @Results({
        @Result(column = "id", property = "id"),
        @Result(column = "name", property = "name")
    })
    User getById(@Param("id") Integer id);
}
```

 #### 2.1.4.UserService.java

**UserService.java** 是一个 **Service**（主要用于实现业务的逻辑）。一般位于 **service** 包下，我们将其放在 **com.yun.service** 包下。其内容如下所示：

```
package com.yun.service;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import com.yun.mapper.UserMapper;
import com.yun.model.User;

@Service
public class UserService {
    @Autowired
    private UserMapper userMapper;
    
	public User getById(Integer id) {
        return userMapper.getById(id);
    }
}
```

### 2.2.修改 application.properties 文件

修改 **application.properties** 文件的扩展名为 **yml** （**注意：这很重要！！！！！！** ），并添加如下内容：

```
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/yun?useUnicode=true&characterEncoding=utf-8&autoReconnect=true&zeroDateTimeBehavior=convertToNull
    username: root
    password: root
    driver-class-name: com.mysql.jdbc.Driver
```

### 2.3.修改 pom.xml 文件

向 **pom.xml** 文件添加依赖：

```
		<!-- 使用数据源 -->
		<dependency>
			<groupId>com.alibaba</groupId>
			<artifactId>druid</artifactId>
			<version>1.0.14</version>
		</dependency>
		<!-- mysql -->
		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<scope>runtime</scope>
		</dependency>
		<!-- mybatis -->
		<dependency>
			<groupId>org.mybatis.spring.boot</groupId>
			<artifactId>mybatis-spring-boot-starter</artifactId>
			<version>1.1.1</version>
		</dependency>
```

完整的 **pom.xml** 文件的内容如下所示：

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>com.example</groupId>
	<artifactId>yun</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<packaging>jar</packaging>

	<name>yun</name>
	<description>yun demo web</description>

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>1.5.4.RELEASE</version>
		<relativePath /> <!-- lookup parent from repository -->
	</parent>

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
		<java.version>1.8</java.version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-aop</artifactId>
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

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-thymeleaf</artifactId>
		</dependency>

		<!-- 使用数据源 -->
		<dependency>
			<groupId>com.alibaba</groupId>
			<artifactId>druid</artifactId>
			<version>1.0.14</version>
		</dependency>

		<!-- mysql -->
		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<scope>runtime</scope>
		</dependency>

		<!-- mybatis -->
		<dependency>
			<groupId>org.mybatis.spring.boot</groupId>
			<artifactId>mybatis-spring-boot-starter</artifactId>
			<version>1.1.1</version>
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


</project>
```

添加完成之后，**Ctrl** + **S** 保存。保存之后 maven 会自动下载相关的依赖，这可能需要一定的时间。

## 3.运行和测试

右击 **YunApplication.java**，选择 **Run as**，然后选择 **Java Application**。

![](E:/images/201706230010.png)

在浏览器输入：

```
localhost:8080/database/1
```

即可查询数据库中 1 号用户的名字。

![](E:/images/201706230011.png)

当然，你也可以查询 2 号用户。

![](E:/images/201706230012.png)

到了这一步，向 SpringBoot 集成 Mybatis 基本就完成了。

## 4.参考链接

[SpringBoot学习3之数据库集成mybatis](https://darylliu.github.io/2016/11/03/SpringBoot%E5%AD%A6%E4%B9%A03%E4%B9%8B%E6%95%B0%E6%8D%AE%E5%BA%93%E9%9B%86%E6%88%90mybatis/)

[mybatis 官网](http://www.mybatis.org/mybatis-3/)









