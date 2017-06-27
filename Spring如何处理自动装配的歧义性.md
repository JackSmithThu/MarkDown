---
title: Spring如何处理自动装配的歧义性
date: 2017-06-25 00:28:14
tags: [Spring,Spring实战]
categories: 技术
---

说明：本文为 **Spring in action** 的读书笔记，其内容部分参考该书。

## 1.Spring 自动装配的歧义性

我们已经知道，利用自动装配可以减少显式配置的数量，为我们带来很大的方便。但是，如果不仅有一个 **bean** 能够匹配的话，**Spring** 就无法进行自动装配了。

举个例子，我们使用 **@Autowired** 注解标注了 **setDessert()** 方法：

```java
@Autowired
public void setDessert(Dessert dessert) {
  this.dessert = dessert;
}
```

与此同时，我们有两个类实现了这个接口，一个叫做 **Cake**，另一个叫做 **IceCream**：

```java
@Component
public class Cake implements Dessert {...}

@Component
public class IceCream implements Dessert {...}
```

那么此时 **Spring** 究竟是应当注入 **Cake** 呢？还是注入 **IceCream** 呢？Spring 也无法判断，因此它会抛出一个 **NoUniqueBeanDedinitionException** 的异常。

## 2.如何解决自动装配的歧义性

Spring 提供了多种方法来解决这个问题，说明如下：

### 2.1.使用 @primary 注解

**@Primary** 注解的使用方式如下： 

```java
@Component
@Primary
public class IceCream implements Dessert {...}
```

**@Primary** 注解使得在有多个满足条件的 **bean** 同时存在是，提供一种优先的解决方案。

### 2.2.使用 @Qualifier 注解

**@Qualifier** 注解的使用方式如下：

```java
@Autowired
@Quatifier("iceCream")
public void serDessert(Dessert dessert) {
  this.dessert = dessert;
}
```

**@Qualifier**  注解中的 **iceCream** 为 **IceCream** 类名首字母变为小写的结果。

当然，你也可以像这样使用 **@Qualifier**  注解。

```java
@Component
@Qualifier("cold")
public class IceCream implements Dessert {...}

@Bean
@Qualifier("cold")
public void setDessert(Dessert dessert) {
  this.dessert = dessert;
}
```

上面的使用方式有利于解耦和代码重构。

### 2.3.自定义的注解

除此之外，我们还可以创建新的注解（如 **@Cold**）来代替 **@Qualifier("cold")**：

```java
@Target({ElementType.CONSTRUCTOR, ElementType.FIELD, ElementType.METHOD, ElementType.TYPE})
@Retention(RetentPolicy.Runtime)
@Qualifier
public @interface Cold { }
```

一般情况下，这个接口放在 **Cold.java** 里。这样我们就可以直接使用注解 **@Clod**：

```java
@Bean
@Cold
public void setDessert(Dessert dessert) {
  this.dessert = dessert;
}
```

当然，我们也可以同时使用多个注解来缩减范围：

```java
@Bean
@Cold
@Creamy
public void setDessert(Dessert dessert) {
  this.dessert = dessert;
}
```

## 3.参考链接

[Spring高级装配之处理自动装配的歧义性](http://www.jianshu.com/p/f2a92091f4ae)

