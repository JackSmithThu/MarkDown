---
title: Linux常用指令
date: 2017-06-28 15:38:37
tags: [Linux]
categories: 技术
---

## 1.目录相关的命令

首先，我们要建立一个叫做 mydir 的文件夹，然后建立一个叫做 myfile 的文件。之后再把这个文件夹及其中的文件删除掉。

按住 Ctrl + Alt + T，打开一个端口，然后输入：

```
$ ls -l
```

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201707280008.png)

通过 ls 命令，我们就可以查看当前目录下的文件和目录（要不然怎么知道建立和删除是否成功了呢，是吧）。然后我们开始建立文件夹。输入 ：

```
$ mkdir mydir 
```

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201707280009.png)

看起来并没有发生什么，现在我们来 ls 一下看看我们是否成功建立了自己的文件夹。

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201707280010.png)

好，既然我们已经有了自己的文件夹了，那就进去建立一个文件吧。进入文件夹的命令是：

```
$ cd mydir
```

进去之后，我们来创建一个文件，命令为：

```
$ vi myfile.txt
```

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201707280011.png)

于是我们就进入了 vim 的界面。

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201707280012.png)

关于 vim 的操作以前已经介绍过了，这里不再赘述。按下 i 进入插入模式，输入 hello 。然后按 Esc 到命令模式，输入：

```
:wq
```

保存退出。然后我们再 ls 一下，就能看见我们刚刚创建的文件了。

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201707280013.png)

现在我们已经建立了新的文件，然后我们要删掉它。在 linux 中：

```
$ rm fliename // 删除文件
$ rmdir filename // 删除文件夹
```

但是我们如果碰到想要删除一个文件夹，而这个文件夹中又有很多文件的情况，处理起来就会很麻烦了。这时我们就需要一个新的命令：

```
$ rm -rf dirname //删除文件夹及其中所有文件
```

尝试一下，不过为了删除当前的目录，我们必须返回上一级目录中。输入：

```
$ cd ../
```

返回上级目录之后，删除该文件夹：

```
$ rm -rf mydir
```

这时整个文件夹已经被我们删除了。

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201707280014.png)



## 2.字符串匹配 grep

grep 可以过滤出你想要的东西，举个例子，我想要在当前目录下找到所有名字中带 Mu 的文件或文件夹，那么我们就可以：

```
$ ls | grep Mu
```

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201707280015.png)



## 3.进程相关指令 ps

ps 是显示进程的指令，可以根据不同的参数调节想要显示的进程种类，显示所有进程的指令是：

```
$ ps aux
```

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201707280016.png)

加入我只想看跟自己相关的进程呢？后面加个 grep 就可以了。

```
$ ps aux | grep jack
```

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201707280017.png)



## 4.查看文件

我们可以通过 cat 命令来查看文件内容：

```
$ cat fliename
```

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201707280018.png)

当然，我们也可以通过 tail 显示文件的最后几行。如果加上参数 -f ，就能不断地读取最新的内容，达到实时监视的效果：

```
$ tail -f filename
```



## 5.参考链接

[linux tail 命令详解](http://www.cnblogs.com/wangkangluo1/archive/2012/05/26/2518856.html)

 [初窥Linux 之 我最常用的20条命令](http://blog.csdn.net/ljianhui/article/details/11100625)





















