---
title: Redis常用命令
date: 2017-08-01 16:12:47
tags: [Redis]
categories: 技术
---

## 1.字符串

### 1.1. SET 命令

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201708080001.png)

### 1.2. GET 命令

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201708080002.png)

### 1.3. GETSET 命令

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201708080003.png)

### 1.4. INCR 自增命令

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201708080004.png)



## 2.有序集合（Sorted Set）

### 2.1. ZADD 命令

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201708080005.png)

### 2.2. ZCARD 数量统计命令

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201708080006.png)

### 2.3. ZCOUNT 查找指定区间成员数命令

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201708080007.png)

### 2.4. ZRANK 返回查找值排名命令

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201708080008.png)

### 2.5. ZREM 移除元素命令

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201708080009.png)

### 2.6. ZUNIONSTORE 并集命令

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201708080010.png)

### 2.7. ZINTERSTORE 交集命令

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201708080011.png)

### 2.8. ZRANGE 范围显示指令

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201708080011.png)



## 3. 哈希（HashSet）

### 3.1. HSET 插入指令

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201708080012.png)

###  3.2. HGET 读取指令

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201708080013.png)

### 3.3. HGETALL 读取全部

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201708080014.png)

### 3.4. HSETNX 如果不存在则赋值

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201708080015.png)

### 3.5. HDEL 删除

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201708080016.png)



## 4.列表（List）

### 4.1. PUSH（插入元素，分为左 LPUSH 和右 RPUSH）

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201708080017.png)

### 4.2. LRANGE 输出某一段元素

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201708080018.png)

### 4.3. POP（分为 LPOP 和 RPOP）

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201708080019.png)

### 4.4. LLEN 输出列表长度

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201708080020.png)

### 4.5. LINDEX 输出某位置元素

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201708080021.png)



## 5.集合

### 5.1. SADD 插入命令

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201708080022.png)

### 5.2. SCARD 统计个数命令

​              ![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201708080023.png)

### 5.3. SDIFFSTORE 差集并存储

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201708080024.png)

### 5.4. SUNION 并集（SUNIONSTORE 则存储）

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201708080025.png)

### 5.5. SINTER 交集

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201708080026.png)

### 5.6. SPOP 随机弹出

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201708080027.png)

### 5.7. SMEMBERS 显示所有元素 

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201708080028.png)