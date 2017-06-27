---
title: Eclipse搭建SpringBoot（四）使用LogBack
date: 2017-06-25 16:10:01
tags: [Eclipse,SpringBoot,Logger]
categories: 技术
---

## 1.为什么使用 Logback

我们在使用调试 **java** 代码的时候，一般使用：

```java
System.out.println("Hello worold!");
```

来输出一些信息到控制台。

但是在实际开发中，一般使用日志记录工具来完成这件事。这么做的目的主要是利用日志的不同级别，来过滤掉不想要的信息。**Logback** 一般使用一下四个级别：

```java
logger.debug("This is a debug message");  
logger.info("This is an info message");  
logger.warn("This is a warn message");  
logger.error("This is an error message");  
```

 ## 2.如何使用Logback

**Logback** 的使用仅需要很简单的配置：

### 2.1.引入相关包

下面是一个使用 **Logback** 的简单示例：

```java
package com.yun.service;

import org.springframework.stereotype.Service;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

@Service
public class LoggerService {
	private final Logger logger = LoggerFactory.getLogger(this.getClass()); 
	public void run() {
		logger.debug("This is a debug message");  
        logger.info("This is an info message");  
        logger.warn("This is a warn message");  
        logger.error("This is an error message"); 
	}
}
```

输出结果为：

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201706250001.png)

为什么没有 debug 的信息呢？这是因为默认的日志级别只到 info。为了输出 debug 的信息，我们需要进行一些额外的配置。

### 2.2.控制输出级别

在 **application.yml** 中添加如下配置：

```yml
logging:
  file: yun.log  
  level:
    ROOT : debug
```

再运行一下，就能看到有 debug 信息的输出结果了：

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201706250002.png)