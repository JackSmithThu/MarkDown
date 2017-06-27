---
title: Spring中的AOP
date: 2017-06-26 18:09:22
tags: [Spring,AOP]
categories: 技术
---

## 1.什么是AOP

**AOP**（面向切面编程）给我们提供了很好的非入侵式（**在不修改源代码的情况下**）的手段为现有的类增加新的功能。本文通过演示 **Spring AOP** 是如何使用的来介绍什么是 **AOP**。

我们会首先演示如何使用基于注解的 **Spring AOP**，然后演示如何使用基于 **XML** 的 **Spring AOP**。最后对 **Spring AOP** 进行简要的总结。

首先我们需要定义一个目标对象（接口），这个对象在调用某些方法时（**之前或之后**）会被拦截，然后执行我们自己的代码。

```java
package concert; 
public interface Performance {
  public void perform();
}
```

假定上面的对象就是我们的目标类。我们希望在该类调用 **perform()** 方法之前和之后分别输出一些提示，但是处于某些原因，我们又不想修改它的源代码。为了实现这一目标，我们需要使用 **AOP** 来达到我们的目的。

## 2.基于注解的 Spring AOP

下面是一个 **Audience** 类，它在本文中作为一个切面来使用。也就是说，在 **Performance** 类调用 **perform()** 前后输出的提示，其实是由 **Audience** 提供的：

```java
package concert;

import org.aspectj.lang.annotation.AfterReturning;
import org.aspectj.lang.annotation.AfterThrowing;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.aspectj.lang.annotation.Pointcut;

@Aspect
public calss Audience {
  
  // 把 Performance 的 perform 方法定义为名为 performance 的切点
  @Pointcut("execution(** concert.Performance.perform(..))")
  public void performance() {}
  
  // 表演之前输出
  @Before("performance()")
  public void silencePhones() {
  	System.out.println("Siliencing phones.");
  }
  
  // 表演之后输出
  @AfterReturning("performance()")
  public void clap() {
  	System.out.println("Claping.");
  }
  
  // 表演失败（抛出异常）输出
  @AfterThrowing（“Performance()”)
  public void refund() {
  	System.out.println("Demanding a refund");
  }
}
```

为了使 Spring 能够识别 @Aspect 注解，我们需要在 JavaConfig 中启用 AspectJ 注解的自动代理。

```java
package concert;

import org.aspectj.lang.annotation.Bean；
import org.aspectj.lang.annotation.ComponentScan；
import org.aspectj.lang.annotation.Configuration；
import org.aspectj.lang.annotation.EnableAspectJAutoProxy；
  
@Configuration
@EnableAspectJAutoProxy
@ComponentScan
public class ConcertConfig {
  @Bean
  public Audience audience() {
    return new Audience();
  }
}
```

## 3.基于 XML 的 Spring AOP

使用注解的 AOP 能够在不侵入目标类的情况下，对目标类的方法进行监控。但是，如果切面 Audience 的代码中有许多注解。试想，如果我们连切面的代码都不想修改，或者无法修改切面的源代码，我们又该如何使用 AOP 呢？XML 为我们提供了一种解决方案：

```java
package concert;

public calss Audience {
  
  public void silencePhones() {
  	System.out.println("Siliencing phones.");
  } 
  
  public void clap() {
  	System.out.println("Claping.");
  }
  
  public void refund() {
  	System.out.println("Demanding a refund");
  }
}
```

我们去掉了 Audience 中所有的注解，当然这样的一个类是无法实现 AOP 的，因此我们需要在XML 中进行配置：

```xml
<aop:config>
  <aop:aspect ref="audience">
    <aop:pointcut
        id="performance"
        expression="execution(** concert.Performance.perform(..))" />
    <aop:before
       pointcut-ref="performance"
       method="soliencePhones"/>
    <aop:after-returning
       pointcut-ref="performance"
       method="clap"/>
    <aop:after-throwing
       pointcut-ref="performance"
       method="refund"/>
  </aop:aspect>
</aop:config>
```

这样一来，我们就可以在两个类都是完全的 **pojo** 类（纯 **java** 代码实现的普通类）的情况下实现 **aop** 了。

