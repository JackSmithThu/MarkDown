---
title: Spring中bean的作用域的实验
date: 2017-06-25 16:10:47
tags: [Spring,Bean]
categories: 技术
---

本文是 [Spring中bean的作用域](https://jacksmiththu.github.io/2017/06/25/Spring%E4%B8%ADbean%E7%9A%84%E4%BD%9C%E7%94%A8%E5%9F%9F/) 的相关实验，目的是演示 **bean** 的作用域是如何使用的。

## 1.实验内容

我们要进行一个实验，看看 bean 的作用域是否能整的响起描述的那样运作。为此，我们选了三种作用域：**单例**、**请求**和**会话**。我们为每一中作用域建立一个 **Service**，这样我们就能看到何时会创建新的 **bean** 来注入。

值得一提的是，因为我们的 **Service** 类并没有实现接口，因此我们就不能使用基于接口的 **JDK** 动态代理。所以我们就只好使用基于 **CgLib** 的动态代理。

在 Spring 中，两种动态代理的设置方式分别为：

```java
// 基于 CgLib 的动态代理
@Service
@Scope(
		value=WebApplicationContext.SCOPE_REQUEST,
		proxyMode=ScopedProxyMode.TARGET_CLASS)
public class RequestService { ... }

// 基于 JDK 的动态代理
@Service
@Scope(
		value=WebApplicationContext.SCOPE_REQUEST,
		proxyMode=ScopedProxyMode.INTERFACES)
public class RequestService { ... }
```

本实验中我们使用的是前者，

## 2.实验代码

在 **com.yun.service** 包下创建四个 **Service**，分别名为 **ScopeService**，**SingletonService**，**SingletonService** 和 **SessionService**。并修改 **YunApplication** 中的内容，其内容分别如下：

### 2.1.ScopeService.java

```java
package com.yun.service;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

@Service
public class ScopeService {
	
	private final Logger logger = LoggerFactory.getLogger(this.getClass());
	
	@Autowired
	private SingletonService singletonService;
	
	@Autowired 
	private SessionService sessionService;
	
	@Autowired
	private RequestService requestService;
	

	
	public String getScopeId() {
		logger.info("=========================================");
		logger.info("requestService id is:" + requestService.getId());
		logger.info("sessionService id is:" + sessionService.getId());
		logger.info("singletonService id is:" + singletonService.getId());
		return "request id is : " + requestService.getId() + " | " + 
			   "session id is : " + sessionService.getId() + " | " +
			   "singleton id is : " + singletonService.getId();
	}
	
	public void setScopeId(Integer id) {
		requestService.setId(id);
		sessionService.setId(id);
		singletonService.setId(id);
		logger.info("=========================================");
		logger.info("requestService id set:" + id);
		logger.info("sessionService id set:" + id);
		logger.info("singletonService id set:" + id);
	}

}
```

### 2.2.SingletonService.java

```java
package com.yun.service;

import org.springframework.beans.factory.config.ConfigurableBeanFactory;
import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Service;

@Service
@Scope(ConfigurableBeanFactory.SCOPE_SINGLETON)
public class SingletonService {
	private Integer id;

	public Integer getId() {
		return id;
	}

	public void setId(Integer id) {
		this.id = id;
	}
}
```

### 2.3.RequestService.java

```java
package com.yun.service;

import org.springframework.context.annotation.Scope;
import org.springframework.context.annotation.ScopedProxyMode;
import org.springframework.stereotype.Service;
import org.springframework.web.context.WebApplicationContext;

@Service
@Scope(
		value=WebApplicationContext.SCOPE_REQUEST,
		proxyMode=ScopedProxyMode.TARGET_CLASS)
public class RequestService {
	private Integer id;

	public Integer getId() {
		return id;
	}

	public void setId(Integer id) {
		this.id = id;
	}
}
```

### 2.4.SessionService.java

```java
package com.yun.service;

import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Service;
import org.springframework.context.annotation.ScopedProxyMode;
import org.springframework.web.context.WebApplicationContext;

@Service
@Scope(
	value=WebApplicationContext.SCOPE_SESSION,
	proxyMode=ScopedProxyMode.TARGET_CLASS)
public class SessionService {
	private Integer id;

	public Integer getId() {
		return id;
	}

	public void setId(Integer id) {
		this.id = id;
	}
}
```

### 2.5.YunApplication.java

```java
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

import com.yun.service.ScopeService;

@SpringBootApplication
@Controller
public class YunApplication {
    
    @Autowired
    private ScopeService scopeService;
    
    @ResponseBody
    @RequestMapping("/setscopeid/{id}")
    public String setScopeId(@PathVariable("id") Integer id) {
        scopeService.setScopeId(id);
        return "id is set by " + id;
    }
    
    @ResponseBody
    @RequestMapping("/getscopeid")
    public String getScopeId() {
        return scopeService.getScopeId();
    }
}
```

## 3.实验结果

### 3.1.本地测试

#### 3.1.1.设定 bean 的属性值

在浏览器输入：

```
http://localhost:8080/setscopeid/25
```

得到结果如图所示：

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201706250003.png)

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201706250004.png)

#### 3.1.2.读取 bean 的属性值

在浏览器输入：

```
http://localhost:8080/getscopeid
```

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201706250005.png)

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201706250006.png)

可见，作用域是 request 的属性值已经读取不到了，但是 session 和 singleton 的仍然能够读取到。

### 3.2.远程测试

#### 3.2.1.获取本机 ip

为了测试 session 作用域的效果，我们使用另一台设备对我们的应用进行访问。在那之前，我们需要知道本机的 ip 地址。在控制台输入：

```
ipconfig
```

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201706250007.png)

如图所示，我的本机 ip 为 10.29.138.44。

#### 3.2.2.远程访问

如果我要在另一台设备上访问我的主机，那么我就需要在浏览器输入：

```
10.29.138.44:8080/getscopeid
```

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201706250008.png)

可见 **session** 在另一台机器上已经不能用了，然而 **singleton** 依旧保持着原来的值。

#### 3.2.3.在本机上使用不同的 session

当然，如果你不想使用另一台计算机的话，在同一计算机上的另一个浏览器也是不同的 **session**。

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201706250009.png)

或者你把原来的浏览器重启一下：

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201706250010.png)



