---
title: JVM内存机制和垃圾回收机制
top: true
cover: false
toc: true
mathjax: true
date: 2021-03-14 16:16:19
password:
summary:
tags:
  - Java
  - JVM
categories:
  - Java
---

#### JVM 的内存逻辑结构

JVM 内存区域：

`堆`：new 出来的对象

`方法区`：已被 JVM 加载的类信息，常量，静态变量

`Java栈`：每个方法在执行的同时都会创建一个栈帧，以栈帧的方式存储局部变量表、操作数栈、动态链接、方法出口等信息

`本地方法栈`：保存 native 方法的信息（native 方法用于调用第三方语言）

`程序计数器`：当前线程的字节码的行号指示器

本地内存：内含直接内存

Java1.8 中取消方法区，在本地内存中创建了一块空间，元空间，用于存储已被 JVM 加载的类信息，常量，静态变量

线程独享：Java 栈、本地方法栈、程序计数器

线程共享：堆、方法区

#### 垃圾回收机制

主要回收堆内存

![堆](https://i.loli.net/2021/03/14/zELTInNUQJ4msVH.png)

- 对象分配：

  优先在 eden 区分配，当 eden 区没有足够空间分配时，发起一次 MinorGC，将 eden 区和其中一块 survivor 区尚存活的对象放入另一块 survivor 区域，若 MinorGC 时，survivork 空间不够，则从前一个 survivor 区复制过来且还存活的对象进入年老代，两块 survivor 区永远由一个是空的，另一个非空且无碎片保存的

- 对象晋级：

  为每个对象定义了一个对象年龄计数器，经过第一次 MinorGC 后仍然存活，被移动到 survivor 空间中，并将年龄设为 1，若对象在 survivor 区中每熬过一次 MinorGC，年龄就加 1，增加到一定程度，就会进入年老代

- MinorGC：

  当新对象生成，且在 eden 区申请空间失败时，就会触发 MinorGC，消除 eden 区和其中一块 survivor 区的非存活对象，并将所有尚且存活的对象移入另一块 survivor 区，较为频繁，速度较快

- FullGC：

  对整个堆进行整理，包括年轻代和年老代，当年老代被写满，System.GC()被调用时，触发 FullGC，较慢

- 对象生死判定：可达性分析算法，当一个对象到 GCRoots 没有引用链相连时，即不可用

- 垃圾回收算法：标记清除法，复制算法，标记整理法
