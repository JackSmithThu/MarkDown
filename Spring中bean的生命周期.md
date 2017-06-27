---
title: Spring中bean的生命周期
date: 2017-06-25 21:44:28
tags: [Spring,Bean]
categories: 技术
---

## 1.传统 Java 应用 bean 的生命周期

1. 使用 **Java** 关键字 **new** 进行 **bean** 实例化。
2. 使用这个 **bean**。
3. **Java** 自动进行垃圾回收。

## 2.Spring 中 bean 的生命周期

1. 实例化。
2. 填充属性。
3. 调用 **BeanNameAware** 的 **setBeanName()** 方法。
4. 调用 **BeanFactoryAware** 的 **setBeanFactory()** 方法。
5. 调用 **ApplicationContextAware** 的 **setApplicationContext()** 方法。
6. 调用 **BeanPostProcessor** 的预初始化方法。
7. 调用 **InitalizingBean** 的 **afterPropertiesSet()** 方法。
8. 调用自定义的初始化方法。
9. 调用 **BeanPostProcessor** 的初始化后方法。
10. **使用 bean。**
11. 调用 **DisposableBean** 的 **destory()** 方法。
12. 调用自定义的销毁方法。

## 3.Spring 中 bean 生命周期的解释

### 3.1.bean 生命周期的本质

就一个对象而言，其声明周期可以概括为：

1. 创建（实例化 + 初始化）
2. 使用
3. 销毁

就 **Spring** 中的 **bean** 而言，**Spring** 只是提供了一些接口，允许开发者在：**实例化**、**初始化**和**销毁**

的前后进行一些操作。

### 3.2.bean 相关方法分类

1. **Bean自身方法**：**init-method/destroy-method**，通过为配置文件**bean**定义中添加相应属性指定相应执行方法。
2. **Bean级生命周期接口**：**BeanNameAware**、**BeanFactoryAware**、和**InitializingBean** 。这些接口的方法，每个 **bean** 选择实现，可选择各自的个性化操作。
3. **容器级生命周期接口方法**：**BeanPostProcessor** 等 ，一般称它们的实现类为“后处理器”，这些接口是每个 **bean** 实例化或初始化时候都会调用。

### 3.3.简单描述

**bean** 的生命周期，主要就是在**实例化**和**填充属性**之后，让 bean 知道自己的**名称**， **工厂**和**上下文** ，并在自定义的初始化方法前后调用**预初始化（前）、填充属性后（前）**和**初始化后（后）**方法。

## 4.参考链接

[Spring Bean生命周期详解](http://blog.csdn.net/a327369238/article/details/52193822)

