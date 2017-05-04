---
title: kafka学习
date: 2017-04-24 18:45:40
tags: [Kafka]
categories: 技术
---

## kafka简介
### 首先，我们先来看一下 **kafka** 是用来干什么的
根据[**kafka**官网](http://kafka.apache.org)的介绍，**kafka**主要用来做这么两件事儿：

*  搭建一个实时的流数据管道以支持系统间的实时数据通信。
*  搭建一个对流数据进行转换或响应的实时流应用。

### 为了实现这个目标，**kafka** 实现了以下三大关键特性

* 支持记录流（**stream of records**）的发布和订阅（有点儿像**消息队列**）。
* 允许你以一种高容错的手段存储记录流。
* 允许你实时地处理产生的记录。

### 初次之外，你可能还需要了解一些关于 **kafka** 其他的知识

* 首先，**kafka** 是运行在由一个或多个服务器组成的集群（**cluster**）上

* **kafka** 集群将流（**stream**）按照不同的分类存储，每个分类叫做一个主题（ **topic**）。

* 每一条记录（**record**）都包含一个键（**key**），一个值（**value**）和一个时间戳（**timestamp**）。

  ***

## kafka原理

###  **kafka** 的四大 API

* **Producer API** ：允许一个应用发布一个数据流到一个或多个 **topic** 上。
* **Consumer API** ：允许一个应用订阅一个或多个 **topic** ，并对发送给它的数据进行处理。 
* **Streams API** ： 允许一个应用作为流处理器（**stream processor**）工作，即从某个或某些 **topic** 订阅，处理之后再发布到其他 **topic** 上。
* **Connector API** ： 用于构建可重用的 **producer** 或 **constmer** 来把**kafka** 的 **topic** 连接到已有的系统上。比如，一个关系型数据库上的 **connector** 可以捕捉该数据库上某张表的内一个变化。
