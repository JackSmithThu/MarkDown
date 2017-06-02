---
title: Docker入门（一）什么是Docer
date: 2017-06-02 16:57:55
tags: [Dcoker,虚拟机，容器]
categories: 技术

---

## 1.什么是Docker

> Docker 是一个开源的应用容器引擎，让开发者可以打包他们的应用以及依赖包到一个可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。容器是完全使用沙箱机制，相互之间不会有任何接口。

通俗地说，Docker 是一种虚拟机（广义上的，准确的说法应该是**容器**）。Docker 与其他虚拟机（如：VMWare，Vitrual Box）的区别在于，Docker是一种高度特化的虚拟机。**Docker的效率更高，但是它只能运行与其所运行在的操作系统相同的系统。**也就是说，如果你的服务器上安装的是Ubuntu，那么你所有的 Docker 虚拟机都必须是 Ubuntu 系统。



## 2.什么时候使用Docker

> 如果你只是想让尽可能多的服务器应用实例在尽可能少的硬件上运行，可能不大关心运行多个操作系统虚拟机。要是同一应用程序的多个副本正是你需要的，那么你会喜欢上容器。

虚拟机管理程序能做容器做不了的一件事就是，使用不同的操作系统或内核。由于所有的容器都需要使用同样的操作系统和内核，因此如果你想仅依靠 Docker 在一台电脑上同时部署 Windows 和 Linux 是一件不可能的事情。不过，这并不影响 Docker 的广泛使用，毕竟 Docker 的效率远高于普通虚拟机，而这意味着更低的成本。



## 3.Docker到底有多快

> docker相对于物理机其计算能力几乎没有损耗，而虚拟机对比物理机则有着非常明显的损耗。虚拟机的计算能力损耗在50%左右。

下图是在IBM x3650 M4 服务器运算 Linpack 程序得到的测试结果：

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201706020002.png)

我们可以看到，使用 Docker 和不使用任何虚拟技术得到的结果基本没什么差别，而使用 KVM（一种虚拟机）则要慢的多了。

那么，为什么 Docker 能够达到如此高的效率呢？我们来看两张图：

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201706020001.png) ![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201706020003.png)

左边是传统虚拟机的架构图，右边是 Docker 。总的来说，Docker 比传统虚拟机快主要在于以下两点原因：

1. docker有着比虚拟机更少的抽象层。
2. docker利用的是宿主机的内核，而不需要Guest OS。

Docker 和传统虚拟机的区别，就好比前者是在集装箱里面打了隔板，而后者则是在集装箱里有塞进去了小的集装箱。因此前者的效率比后者更高也就不足为奇了。



## 4.参考链接

[Docker到底是什么？为什么它这么火！](http://cloud.51cto.com/art/201410/453718.htm)

[如何通俗解释Docker是什么？](https://www.zhihu.com/question/28300645)

[ docker与虚拟机性能比较](http://blog.csdn.net/cbl709/article/details/43955687)