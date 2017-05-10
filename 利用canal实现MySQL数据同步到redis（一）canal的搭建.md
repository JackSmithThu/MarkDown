---
title: 利用canal实现MySQL数据同步到redis（一）canal的搭建
date: 2017-05-10 17:20:22
tags: [Redis,MySQL,Canal,Windows]
categories: 技术
---

## 1.什么是cannal

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705100001.jpg)

cannal 的原理相对比较简单：

1. canal 模拟 mysql slave 的交互协议，伪装自己为mysql slave，向 mysql master发送 dump 协议
2. mysql master 收到 dump 请求，开始推送 binary log 给 slave (也就是 canal)
3. canal 解析 binary log 对象(原始为 byte 流)

## 2.服务器端的配置

### 2.1.配置MySQL

#### 2.1.1.修改配置文件

打开 MySQL 根目录，修改 my-default.ini 文件。

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705100002.png)

添加如下内容至文件中

```
log-bin=mysql-bin #添加这一行就ok
binlog-format=ROW #选择row模式
server_id=1 #配置mysql replaction需要定义，不能和canal的slaveId重复
```

添加结果如图所示：

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705100003.png)


#### 2.1.2.新建一个名为 canal 的用户，赋予其相应的权限

SQL 语句如下：

```
CREATE USER canal IDENTIFIED BY 'canal';  
GRANT SELECT, REPLICATION SLAVE, REPLICATION CLIENT ON *.* TO 'canal'@'localhost';
-- GRANT ALL PRIVILEGES ON *.* TO 'canal'@'localhost' ;
FLUSH PRIVILEGES;
```

语句中的 localhost 在实际应用中需要改成 canal 服务器的 ip 地址。如果你和我一样使用本机进行配置，使用 localhost 即可。

### 2.2.下载 canal

下载地址：https://github.com/alibaba/canal/releases 

我们下载下图中标红的两个文件。

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705100004.png)


在你喜欢的位置（我的是D:\WOrkSapce\）建立一个名为 canal 的文件夹，将这两个压缩包放到里面，然后分别解压到各自的文件夹即可。

### 2.3.修改 canal 配置文件

进入 D:\WorkSpace\canal\canal.deployer-1.0.24\conf\example 目录下

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705100005.png)


找到 instance.properties 文件，将数据库参数修改成你自己的信息。

```
#################################################
## mysql serverId
canal.instance.mysql.slaveId = 1234

# position info
canal.instance.master.address = 127.0.0.1:3306
canal.instance.master.journal.name = 
canal.instance.master.position = 
canal.instance.master.timestamp = 

#canal.instance.standby.address = 
#canal.instance.standby.journal.name =
#canal.instance.standby.position = 
#canal.instance.standby.timestamp = 

# username/password
canal.instance.dbUsername = canal
canal.instance.dbPassword = canal
canal.instance.defaultDatabaseName = test
canal.instance.connectionCharset = UTF-8

# table regex
canal.instance.filter.regex = .*\\..*
# table black regex
canal.instance.filter.black.regex =  

#################################################
```

### 2.4.启动 canal 服务端

进入 D:\WorkSpace\canal\canal.deployer-1.0.24\bin 目录下，输入：

```
startup.bat
```

启动 canal 服务端，得到结果如下图所示：

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705100006.png)


## 3.客户端的配置

### 3.1.导入 maven 项目

打开 eclipse，【File】->【Import】

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705100007.png)


选择 【Maven】 -> 【Existing Maven Projects】

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705100008.png)


找到你刚才下载解压的 canal-canal-1.0.24 ，确定

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705100009.png)


你会发现这些项目已经导入进来了

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705100010.png)


### 3.2.打开并运行 example

打开 canal.example ，找到 ClusterCanalClientTest ，运行

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705100011.png)


### 3.3.更新数据库并测试

打开 MySQL 终端，输入：

```
use test
create table table0(id int);
```

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705100012.png)


这时查看 ClusterCanalClientTest 的终端，发现 binlog 信息已经被解析出来了

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705100013.png)


## 4.参考链接

alibaba/canal 项目介绍：
https://github.com/alibaba/canal/wiki

## 5.操作系统：Windows10