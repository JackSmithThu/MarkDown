---
title: Eclipse搭建SpringBoot
date: 2017-03-28 15:44:49
tags: [SpringBoot,Ecilpse]
categories: 技术
---

## 1.在Eclipse中安装STS插件

进入Eclipse，选择【help】→【Eclipse Marketplace 】

![1](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705040001.png)

搜索→【STS】,找到【SpringToolSuite】并下载。

![2](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705040002.png)

## 2.新建一个项目

【file】->【new】->【project】

![3](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705040003.png)

选择Spring Starter Project，然后【next】

![4](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705040004.png)

给你的项目取个名字，我的叫【bootDemo】，点击【next】

![5](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705040005.png)

添加你所需要的依赖，由于我们现在只是演示，就只添加【core】中的【AOP】和【web】中的【web】。选好之后，点击【next】->【Finish】。

![6](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705040006.png)

建好之后，你会得到这样一个工程：

![7](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705040007.png)

## 3.运行程序

点击运行按钮，运行程序。运行方式选 Spring Boot App。

![8](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705040008.png)
![9](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705040009.png)

得到结果如下：

![10](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705040010.png)

这样，一个Spring Boot 项目就搭建起来了。

## 4.可能会遇到的问题：

建立新项目后下载依赖的时候，可能会出现卡在那里不动的情况。这是因为国内连国外的镜像连不上造成的。可以修改maven中的镜像源，具体操作如下：

修改maven根目录下的conf文件夹中的setting.xml文件，内容如下：

``` 
<mirrors>
    <mirror>
      <id>alimaven</id>
      <name>aliyun maven</name>
      <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
      <mirrorOf>central</mirrorOf>        
    </mirror>
</mirrors>
```
改好之后，右击项目→【maven】→【update project】即可

![11](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705041001.png)

如果还是不行的话，就去看看 用户名/.m2/目录下是否也存在一个settings.xml文件，这个配置文件的优先级是高于maven安装目录下的配置文件的。所以要把这个也改成阿里云的镜像。

```
<mirrors>
    <mirror>
      <id>alimaven</id>
      <name>aliyun maven</name>
      <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
      <mirrorOf>central</mirrorOf>        
    </mirror>
</mirrors>
```

## 5.参考链接：

[Eclipse中创建新的Spring Boot项目](http://blog.csdn.net/clementad/article/details/51334064)
[maven阿里云中央仓库](http://blog.csdn.net/u010717403/article/details/52188496)
[Maven2的配置文件settings.xml](http://www.cnblogs.com/yakov/archive/2011/11/26/maven2_settings.html)

## 6.Eclipse版本：eclipse-jee-neon-2-win32-x86_64.zip







