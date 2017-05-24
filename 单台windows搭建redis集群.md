---
title: 单台windows搭建redis集群
date: 2017-03-18 14:02:38
tags: [Redis,Windows]
categories: 技术
---
## 1.下载和安装必要的工具

### 1.1.下载安装redis for windows

下载地址：https://github.com/MSOpenTech/redis/releases

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705050001.png)

msi文件和zip文件，选一个下就行。msi文件是一个安装程序，有点类似于exe文件，双击就能运行。安装好之后跟用zip解压的结果是一样的。

### 1.2.下载安装ruby

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705050002.png)

下载地址：https://rubyinstaller.org/downloads/

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705050003.png)

我下载安装的版本是Ruby2.3.3（x64）。安装的时候，记得把下面三个选项勾选上（如下图所示），这样就不用我们自己去配置环境变量了。

### 1.3.下载安装GEM：

下载地址：https://rubygems.org/pages/download

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705050004.png)

下载zip文件就行，解压到你喜欢的位置（我解压到了D:/software目录下）。

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705050005.png)

双击setup.rb，安装GEM。完成之后，把该文件夹的bin目录添加到系统变量中的path里面。
首先，复制该bin路径。

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705050006.png)

然后右键【此电脑】->【属性】

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705050007.png)

【高级系统设置】->【环境变量】->【系统变量】->【Path】->【编辑】

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705050008.png)

单击【新建】，把bin路径粘贴到方框中，然后点击【确定】即可。

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705050009.png)

### 1.4.生成redis-trib.rb文件

新建一个文本文件，把它的名字改成redis-trib.rb。注意在改名之前先把隐藏的扩展名显示出来，否则会生成redis-trib.rb.txt文件。建好之后，访问：

https://raw.githubusercontent.com/antirez/redis/unstable/src/redis-trib.rb

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705050010.png)

复制页面中的内容，粘贴到新建的redis-trib.rb文件中，保存。然后将此文件复制到redis的安装目录下（我的是D:\Software\Redis）。然后将这个目录也添加到系统变量的path中。

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705050011.png)

OK，这样准备工作就完成了。接下来我们要配置redis集群。

## 2.配置redis集群

### 2.1.创建相应目录：

在你喜欢的地方（我的是D:\WorkSpace）建立一个叫做redis的目录（注意，这个不是刚才安装redis的目录，而是一个你新建的目录，后文中如果没有特殊说明，redis目录都指这个新建的目录），里面创建6个子目录，分别叫7000,7001,7002,7003,7004,7005。

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705050011.png)

打开7000文件夹，建立一个redis.conf文件，将下面内容粘贴进去。

```
port 7000
cluster-enabled yes
cluster-config-file nodes-7000.conf
cluster-node-timeout 5000
appendonly yes

```
同理，在其他5个文件夹也各自创建一个redis.conf文件，将上面内容粘贴进去。不过要将prot和cluster-config-file中的7000改成对应的数字。如在7001文件夹中就改成7001。

### 2.2.Win+R输入cmd，回车，打开命令行。输入：gem install redis。

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705050012.png)

你显示的结果可能跟我稍微有些不一样，没关系，这是因为我刚才已经安装过了。

由于 GFW的问题， GEM 的源有时会不可用，如果你遇到了这个问题，可以使用淘宝的映像：
添加：gem sources -a https://ruby.taobao.org
查看已存在的源：gem sources -l
删除被墙的源：gem sources -r https://rubygems.org/

### 2.3.在D:\WorkSpace\redis目录下创建redis.bat文件
文件内容如下所示：
```
@echo off
cd D:\WorkSpace\Redis
start Redis-Server ./7000/redis.conf
start Redis-Server ./7001/redis.conf
start Redis-Server ./7002/redis.conf
start Redis-Server ./7003/redis.conf
start Redis-Server ./7004/redis.conf
start Redis-Server ./7005/redis.conf

```
双击redis.bat，弹出6个窗口，同时redis目录下会出现nodes-7000.conf 至 nodes-7005.conf 这几个文件。

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705050013.png)

### 2.4.在D:\WorkSpace\redis目录下新建cluster.bat文件

文件内容如下所示：
```
cd D:\WorkSpace\redis
redis-trib.rb create --replicas 1 127.0.0.1:7000 127.0.0.1:7001 127.0.0.1:7002 127.0.0.1:7003 127.0.0.1:7004 127.0.0.1:7005

```
双击cluster.bat，在询问是否要接受配置时输入“yes”。

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705240001.png)

得到结果如下所示：

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705240002.png)

### 2.5.测试一下

打开cmd，输入：redis-cli.exe -c -p 7000
再打开一个cmd，输入redis-cli.exe -c -p 7001
它们连接了不同的实例，我们现在要测试以下它们的数据是否是同步的。
我们在7000的cmd窗口中输入： set test helloworld
在7001的cmd窗口中输入：get test
可以看到redis输出：”helloworld”
![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705050015.png)

好，到这儿redis的集群就部署完成了。

## 3.参考链接：
Windows 部署 Redis 群集:
http://www.cnblogs.com/xling/p/5253063.html
Redis安装：
http://www.runoob.com/redis/redis-install.html

## 4.操作系统：Windows 10