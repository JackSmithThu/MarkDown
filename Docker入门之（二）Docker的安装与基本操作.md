---
title: Docker入门之（二）Docker的安装与基本操作
date: 2017-06-07 14:33:21
tags: [Docker,虚拟机,Ubuntu]
categories: 技术

---

## 1.Docker的安装

### 1.1.安装 curl 包

按 Ctrl + Alt +T 打开终端，输入：

```
$ sudo su
```

然后输入密码。

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201706070001.png)

获取超级用户权限之后，输入：

```
$ apt-get update 
$ apt-get install curl
```

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201706070002.png)

### 1.2.用 curl 获取最新的 docker 安装包

在终端输入：

```
curl -sSL https://get.docker.com/ | sh 
```

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201706070003.png)

### 1.3.检查 docker 是否安装成功

在终端输入：

```
$ docker run hello-world
```

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201706070004.png)

由于本地没有 hello-world 镜像，docker 会自动从网上下载这个镜像，然后运行。



## 2.Docker的使用

为了演示 Docker 的使用方法，我们新建一个镜像并在里面安装一个 vim，然后上传到云端。

### 2.1.账号注册

首先你需要一个 Docker 的账号，登陆：https://www.docker.com/

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201706070005.png)

点击上图标红部分，进入账号申请页面：

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201706070006.png)

填写好信息之后点击 【Sign Up】。注意，到这一步注册还没有完全成功，因为它会给你的邮箱发一封验证邮件，在你去邮箱验证该邮件之前账号都是不能使用的。

#### 所以千万不要忘了验证邮件！！！

### 2.2.登录账号

在终端输入：

```
$ docker login
```

此时会提示输入账号密码，输入完成后会提示登录成功。

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201706070007.png)

### 2.3.启动一个容器

在终端输入：

```
$ docker run -t -i ubuntu:16.04
```

此时会下载 ubuntu 16.04 的镜像并启动。 -i 的意思是以交互模式运行，-t 的意思是打开一个该容器的伪终端。

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201706070008.png)

根据提示我们可以看出，我们已经进入了一个 docker 容器的伪终端。

### 2.4.安装 vim

在新打开的伪终端输入：

```
$ apt-get update
```

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201706070009.png)

然后输入：

```
$ apt-get install vim
```

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201706070010.png)

询问是否继续（**Y/n**）时，输入 y。

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201706070011.png)

### 2.5.测试vim

在伪终端输入：

```
$ vim a.txt
```

建立一个叫做 a.txt 的文件。

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201706070012.png)

打开 vim 程序如图所示：

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201706070013.png)

按下键盘上的 **i** ，进入插入模式（**i**nsert），随便输入一点字符。

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201706070014.png)

然后按下 Esc 进入命令模式，输入：

```
:wq
```

保存文件并退出。

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201706070015.png)

我们已经建立了一个 a.txt 的文档，接下来输入：

```
$ cat a.txt
```

查看文档是否存在，以及其内容是否为我们输入的内容。

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201706070016.png)





## 3.Docker镜像的保存

### 3.1.提交镜像

在伪终端输入：

```
$ exit
```

从伪终端退出。

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201706070017.png)

然后输入：

```
$ docker commit f0a jacksmith/demo
```

其中 f0a 是虚拟机容器序号的前三位，jacksmith 需要改成你自己的用户名，demo 是你给你自己镜像取的名字。

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201706070018.png)

### 3.2.上传镜像

在终端输入：

```
$ docker push jacksmith demo
```

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201706070019.png)

### 3.3.查看镜像是否上传成功

在终端输入：

```
$ docker search jacksmith
```

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201706070020.png)

可见已经上传已经成功了。



## 4.重用Docker镜像

Ctrl + Alt +T，打开一个新终端，输入：

```
$ sudo su
```

输入密码之后，输入：

```
$ docker run -i -t jacksmith/demo
```

打开伪终端，输入：

```
$ vim a.txt
```



![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201706070021.png)

可以看到这就是我们刚才发布的镜像：

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201706070022.png)



## 5.参考链接

[Docker 命令大全](http://www.runoob.com/docker/docker-command-manual.html)

[Docker手册](http://www.docker.org.cn/book/)