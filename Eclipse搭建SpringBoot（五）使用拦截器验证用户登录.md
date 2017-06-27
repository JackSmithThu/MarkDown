---
title: Eclipse搭建SpringBoot（五）使用拦截器验证用户登录
date: 2017-06-26 16:16:16
tags: [SpringBoot,Ecilpse,拦截器]
categories: 技术
---

## 1.使用拦截器验证用户登录的手段

目前网上提供的拦截器验证用户登录主要分为两种手段：

#### 把拦截器声明成一个注解加到 Controller 上

如：[springboot(八)拦截器之验证登录](http://blog.csdn.net/u011493599/article/details/53942757)

#### 在 WebConfig.java 中添加拦截器相关规则，

如：[SpringBoot学习笔记（六）：配置拦截器，控制登录跳转](http://blog.csdn.net/jalon2015/article/details/71423974)

本文中采用第二种方案，因为它实现起来比较简单。

## 2.实现步骤

### 2.1.创建拦截器

在项目中添加 **com.yun.interceptor** 包，并在其中添加 **MyInterceptor** 类，其内容如下：

```java
package com.yun.interceptor;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

public class MyInterceptor implements HandlerInterceptor {
    Logger logger = LoggerFactory.getLogger(MyInterceptor.class);

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
            throws Exception {
        // TODO Auto-generated method stub
        //获取session
        HttpSession session = request.getSession(true);
        //判断用户ID是否存在，不存在就跳转到登录界面
        if(session.getAttribute("userId") == null){
            logger.info("------:跳转到login页面！");
  response.sendRedirect(request.getContextPath()+"/login");
            return false;
        }else{
            session.setAttribute("userId", session.getAttribute("userId"));
            return true;
        }
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,
            ModelAndView modelAndView) throws Exception {
        // TODO Auto-generated method stub

    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex)
            throws Exception {
        // TODO Auto-generated method stub

    }

}
```

### 2.2.添加 WebConfig.java 文件

在项目中创建 com.yun.config 包，创建 WebConfig.java 文件，其内容如下：

```java
package com.yun.config;

import org.springframework.beans.BeansException;
import org.springframework.context.ApplicationContext;
import org.springframework.context.ApplicationContextAware;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.util.ResourceUtils;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurerAdapter;

import com.yun.interceptor.MyInterceptor;

@Configuration
@EnableWebMvc
@ComponentScan
public class WebConfig extends WebMvcConfigurerAdapter implements ApplicationContextAware {

    private ApplicationContext applicationContext;

    public WebConfig(){
        super();
    }

    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/static/**").addResourceLocations(ResourceUtils.CLASSPATH_URL_PREFIX+"/static/");
        registry.addResourceHandler("/templates/**").addResourceLocations(ResourceUtils.CLASSPATH_URL_PREFIX+"/templates/");

        super.addResourceHandlers(registry);  
        }

    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        this.applicationContext = applicationContext;
    } 
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        //拦截规则：除了login，其他都拦截判断
        registry.addInterceptor(new MyInterceptor()).addPathPatterns("/**").excludePathPatterns("/login");
        super.addInterceptors(registry);
    }

}
```

### 2.3.修改 YunApplication.java 文件

在 YunApplication.java 文件中添加如下代码：

```java
package com.yun;

import javax.servlet.http.HttpSession;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;

import com.yun.service.LoggerService;
import com.yun.service.ScopeService;
import com.yun.service.UserService;
/*
 * 控制器使用示例
 */

@SpringBootApplication
@Controller
public class YunApplication {
  
    @ResponseBody
	@RequestMapping("/")
	public String greeting() {	
		return "Hello World!";
	}

// 下面是需要添加到 YunApplication.java 中的代码
// 本文件中的其他内容只是为了说明这段代码应该添加到什么位置
    
    @ResponseBody
    @RequestMapping("/login")
    public String login(HttpSession session) {
    	session.setAttribute("userId", "jackie");
        return "login";
    }
  
 // 需要添加的代码到此为止
}
```

## 3.登录测试

本文中主要强调利用拦截器验证登录与否，因此 **login** 页面就直接帮助用户以 **jackie** 的身份登录了。

首先，在浏览器中输入：

```
localhost:8080
```

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201706260001.png)

然后敲击回车访问，发现网站自动跳转到 login 页面：

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201706260002.png)

再次输入：

```
localhost:8080
```

然后回车访问，发现已经可以正常地访问了：

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201706260003.png)

## 4.参考链接

[springboot(八)拦截器之验证登录](http://blog.csdn.net/u011493599/article/details/53942757)

[SpringBoot学习笔记（六）：配置拦截器，控制登录跳转](http://blog.csdn.net/jalon2015/article/details/71423974)





 

