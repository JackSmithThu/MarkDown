---
title: Java基础之Synchronized与ReentrantLock的区别
date: 2017-06-27 18:38:37
tags: [Java,JVM,Synchronized,Lock]
categories: 技术
---

## 1.不同同步手段之间的区别

### 1.1.Synchronized

在资源竞争不是很激烈的情况下，偶尔会有同步的情形下，**synchronized** 是很合适的。

1. 原因在于，编译程序通常会尽可能的进行优化 **synchronized**，
2. **synchronized** 另外可读性非常好。

###　1.2.ReentrantLock

**ReentrantLock** 适用于资源竞争比较激烈的情况。

1. 在资源竞争不激烈的情形下，性能稍微比**synchronized**差点点。
2. 当同步非常激烈的时候，**synchronized** 的性能一下子能下降好几十倍。而 **ReentrantLock** 还能维持常态。
3. 可以实现多样化的同步，比如**有时间限制的**同步，可以被 **Interrupt** 的同步等。

### 1.3.Atomic

**Atomic** 同样适用于资源竞争比较激烈的情况。

1. 不激烈情况下，性能比**synchronized** 略逊。
2. 资源竞争激烈的时候，也能维持常态。**Atomic** 的性能会优于**ReentrantLock**一倍左右。
3. **Atomic** 有一个缺点，就是只能同步一个值，一段代码中只能出现一个**Atomic** 的变量，多于一个同步无效。因为他不能在多个**Atomic**之间同步。

## 2.RentranLock 的使用方式

1. **lock()** ，如果获取了锁立即返回，如果别的线程持有锁，当前线程则一直处于休眠状态，直到获取锁
2.  **tryLock()** ，如果获取了锁立即返回true，如果别的线程正持有锁，立即返回false。
3. **tryLock(long timeout,TimeUnit unit)**， 如果获取了锁定立即返回true，如果别的线程正持有锁，会等待参数给定的时间，在等待的过程中，如果获取了锁定，就返回true，如果等待超时，返回false。
4. **lockInterruptibly()** ，如果获取了锁定立即返回，如果没有获取锁定，当前线程处于休眠状态，直到或者锁定，或者当前线程被别的线程中断。

## 3.参考链接

[Lock与synchronized 的区别](http://www.cnblogs.com/nsw2018/p/5821738.html)

