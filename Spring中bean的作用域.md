---
title: Spring中bean的作用域
date: 2017-06-25 01:41:59
tags: [Spring,Bean]
categories: 技术
---

## 1.为什么要用到 Spring 的作用域

默认情况下，**Spring** 中所有的 **bean** 都是以单例（**singleton**）模式创建的。但是在有些情况下，这种方式创建的 **bean** 可能无法满足我们的需要。

比如，我们要创建一个购物车的 **bean** 。如果购物车是以单例模式创建的话，那么所有顾客的商品都会被添加到同一个购物车里。显然，这不是我们想要的结果。

## 2.Spring 的作用域

**Spring** 定义了几种作用域，下面是常用的四种：

1. 单例（**Singleton**）：整个应用中只创建一个 **bean** 的实例。
2. 原型（**Prototype**）：每次注入或者通过 **Spring** 上下文获取的时候，创建一个新的 **bean** 实例。
3. 会话（**Session**）：在 **Web** 应用中，为每个会话创建一个 **bean** 实例。
4. 请求（**Request**）：在 **Web** 应用中，为每个请求创建一个 **bean** 实例。


关于 **Spring** 上下文的相关内容，可以参考下面这篇文章: 

[spring中的web上下文，spring上下文，springmvc上下文区别(超详细)](http://blog.csdn.net/crazylzxlzx/article/details/53648625)

关于原型 **Bean** 作用域的相关内容，可以参考这篇文章：

[Spring核心技术（五）——Spring中Bean的作用域](http://blog.csdn.net/ethanwhite/article/details/51476238)

## 3.Spring 作用域的使用

Spring 使用 @Scope 注解来使用作用域，使用示例如下：

```
// 使用 @Scope 的一种方式
@Component
@Scope(ConfigurableBeanFactory.SCOPR_PROTOTYPE)
public class Notepad {...}

// 使用 @Scope 的另一种方式
@Component
@Scope("prototype")
public class Notepad {...}
```

## 4.参考链接

[spring中的web上下文，spring上下文，springmvc上下文区别(超详细)](http://blog.csdn.net/crazylzxlzx/article/details/53648625)