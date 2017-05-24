---
title: 后台开发知识点总结（一）java基础
date: 2016-9-24 09:37:09
tags: [Java]
categories: 技术
---

### Java八种基本数据类型：
byte、short、int、long、float、double、boolean、char。




### Map在Java中的四种实现？
HashMap（最简单实现）、HashTable（线程安全）、LinkedHashMap（可顺序访问）、TreeMap（基于红黑树实现的map）。




### List在Java中的三种实现？
ArrayList（数组）、LinkedList（链表）、Vector（线程安全的ArrayList）。



### Set在Java中的四种实现？
HashSet（由HashMap支持，相当于没有 value 的 HashMap） 和 LinkedHashSet（顺序访问）。




### Java8的新特性
Lambda表达式：允许把函数作为参数传进方法中。
Stream：random.ints().limit(10).forEach(System.out::println);
Optional类：使用Optional类可以避免显式进行空值检测
Nashorn：Java中调用Js，JavaScript引擎。
方法引用：通过方法的名字指向一个方法（使用::）。
函数式接口：增加了java.util.function函数接口，用来支持函数式编程（支持闭包）
默认方法：接口可以通过defult实现默认方法。
新的日期时间API。
Base64：一种编解码器。




### 什么是Java NIO？
NIO就是非阻塞IO。




### NIO的实现原理是什么？
NIO使用双向的通道（channel）而非单向的流（stream）进行通信。在通道上我们可以注册总共四种事件：客户端连接、服务端接收客户端连接、读、写。客户端和服务端各自维护一个管理通道的对象（selector），接收一个或多个通道的事件。




### 为什么要用NIO？
对阻塞式IO而言，每一个客户端都要有一个对应的线程来进行处理。每个线程都要占用一些空间和CPU时间。另外，多个线程带来频繁的上下文切换，而这些切换很可能是无意义的。
