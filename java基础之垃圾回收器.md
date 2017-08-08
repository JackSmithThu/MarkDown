---
title: Java基础之垃圾回收器
date: 2017-07-25 18:38:00
tags: [Java,JVM,垃圾回收]
categories: 技术
---

## 1. Serial 收集器

**Serial** 收集器是单线程垃圾回收器，它仅能使用单个线程去进行垃圾回收的工作，而且在进行垃圾回收时需要 **"Stop the World"**。也就是说，在它工作的同时，其他线程必须暂停运行。

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201707260001.png)

## 2. Serial Old 收集器

**Serial** 收集器的老年代版本。一种用途是在 **JDK 1.5** 以及之前的版本中与**Parallel Scavenge** 收集器搭配使用，另一种用途就是作为**CMS** 收集器的后备预案，在并发收集发生**Concurrent Mode Failure** 时使用。 

## 3. ParNew 收集器

**ParNew** 收集器是 **Serial** 收集器的多线程版本。因为功能强大的老年代收集器 **CMS** 收集器无法与 **Parallel Scavenge** 收集器配合工作，因此 **CMS** 收集器常与 **ParNew** 收集器配合使用。

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201707260002.png)

## 4. Parallel Scavenge 收集器

**Parallel Scavenge** 收集器的特点是它的关注点与其他收集器不同，**CMS** 等收集器的关注点
是尽可能地缩短垃圾收集时用户线程的停顿时间，而**Parallel Scavenge** 收集器的目标则是达到
一个可控制的吞吐量。 

## 5. Parallel Old 收集器 

**Parallel Old** 是 **Parallel Scavenge** 收集器的老年代版本，使用多线程和“标记-整理”算法。
这个收集器是在 JDK 1.6 中才开始提供的，在此之前，新生代的 **Parallel Scavenge** 收集器一直
处于比较尴尬的状态。 原因是，如果新生代选择了**Parallel Scavenge** 收集器，老年代除了
**Serial Old** 收集器外别无选择。

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201707260003.png)

## 6. CMS 收集器

**CMS（Concurrent Mark Sweep）**收集器是一种以获取最短回收停顿时间为目标的收集器。   

其工作步骤分为以下四步：

* 初始标记（CMS initial mark）
* 并发标记（CMS concurrent mark）
* 重新标记（CMS remark）
* 并发清除（CMS concurrent sweep） 

其中，初始标记、 重新标记这两个步骤仍然需要“Stop The World”。 初始标记仅仅只是标记一下**GC Roots** 能直接关联到的对象，速度很快，并发标记阶段就是进行 **GC RootsTracing** 的过程，而重新标记阶段则是为了修正并发标记期间因用户程序继续运作而导致标记产生变动的那一部分对象的标记记录，这个阶段的停顿时间一般会比初始标记阶段稍长一些，但远比并发标记的时间短。

由于整个过程中耗时最长的并发标记和并发清除过程收集器线程都可以与用户线程一起工作，所以，从总体上来说，CMS收集器的内存回收过程是与用户线程一起并发执行的。

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201707260004.png)

## 7. G1收集器 

**G1** 是一款面向服务端应用的垃圾收集器。 **HotSpot** 开发团队赋予它的使命是（在比较长期的）未来可以替换掉 **JDK 1.5** 中发布的 **CMS** 收集器。 与 **CMS** 收集器相比，**G1**具备如下特点。 

* 空间整合：与 **CMS** 的“标记—清理”算法不同，**G1** 从整体来看是基于“标记—整理”算法实现的收集器，从局部（两个**Region**之间）上来看是基于“复制”算法实现的，但无论如何，这两种算法都意味着 **G1** 运作期间不会产生内存空间碎片，收集后能提供规整的可用内存。 这种特性有利于程序长时间运行，分配大对象时不会因为无法找到连续内存空间而提前触发下一次 **GC**。
* 可预测的停顿：这是 **G1** 相对于 **CMS** 的另一大优势，降低停顿时间是 **G1** 和 **CMS** 共同的关注点，但 **G1** 除了追求低停顿外，还能建立可预测的停顿时间模型，能让使用者明确指定在一个长度为M毫秒的时间片段内，消耗在垃圾收集上的时间不得超过N毫秒，这几乎已经是实时 **Java（RTSJ）**的垃圾收集器的特征了。

值得一提的是，在 **G1** 之前的其他收集器进行收集的范围都是整个新生代或者老年代，而 **G1** 不再是这样。 使用 **G1** 收集器时，**Java** 堆的内存布局就与其他收集器有很大差别，它将整个 **Java** 堆划分为多个大小相等的独立区域（**Region**），虽然还保留有新生代和老年代的概念，但新生代和老年代不再是物理隔离的了，它们都是一部分 **Region**（不需要连续）的集合。 

如果不计算维护Remembered Set的操作，G1收集器的运作大致可划分为以下几个步骤：

* 初始标记（Initial Marking）
* 并发标记（Concurrent Marking）
* 最终标记（Final Marking）
* 筛选回收（Live Data Counting and Evacuation） 

前三个阶段与 **CMS** 收集器相似，最后在筛选回收阶段首先对各个**Region** 的回收价值和成本进行排序，
根据用户所期望的 **GC** 停顿时间来制定回收计划。

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201707260005.png)