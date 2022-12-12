---
title: "[协程] Java虚拟线程"
seo_title: "[协程] Java虚拟线程"
indent: true
comments: true
cover: false
mathjax: false
pin: false
author:   vinsonws
top_meta: true      
bottom_meta: true   
icons:
  - fas fa-fire red
  - fas fa-star green
plugins:
  - indent
date: 2022-12-12 14:10:29
updated: 2022-12-12 14:10:29
tag:
categories:
keywords:
description:
references:
  - title: Loom - inside.java
    url: https://inside.java/tag/loom
  - title: Loom Openjdk
    url: https://wiki.openjdk.org/display/loom/Main
  - title: 'JEP 418: Internet-Address Resolution SP'
    url: https://openjdk.org/jeps/418
  - title: 'JEP 425: Virtual Threads (Preview)'
    url: https://openjdk.org/jeps/425
  - title: 'JEP 425: Virtual Threads (Second Preview)'
    url: https://openjdk.org/jeps/436
  - title: 'JDK 19 Features'
    url: https://openjdk.org/projects/jdk/19/
---
从Project Loom诞生以来，社区都非常关注Java中协程的实现，终于在JDK 19中以预览API的形式开放使用。
 <!-- more -->

## 虚拟线程引入JDK

在JDK 19的Features中加入了Virtual Threads (Preview)，这意味着一个完全开发完成的Java版的协程已经处于可用阶段了，并且在不久的将来Java虚拟线程会去掉预览，成为Java语言的正式功能。

## JDK引入协程的动力

在讨论Java为什么引入协程之前，我们必须先讨论Java原有的线程模型，这是Java在服务端成功的重要原因。我们再来讨论Java原来的线程模型的局限性，以及其迫切升级的原因。

### 线程模型

线程（Thread）是操作系统中一个重要概念，是独立调度和分派的基本单位。线程的执行由线程的调度方决定，而调度可以在内核级或者用户级执行，多任务调度也可以是抢占式或协作式。我们先来简单介绍一些术语。

{% folding yellow::术语%}

{% note radiation green::**进程**: 进程是操作系统中执行中的程序实体，是系统进行资源分配的基本单位，一个进程由一个或者多个线程组成。进程由操作系统内核直接创建调度。进程中的线程是基本的调度和执行单位，根据方式不同，分为内核线程或用户线程。 %}

{% note radiation cyan::**内核线程**: 内核线程是内核的基本调度单元，这种线程由内核直接调度，每个进程中至少存在一个内核线程，在一个进程中存在多个线程的情况下，内核线程的调用根据操作系统的调度模式执行（一般是抢占式调度）。内核线程仅由堆栈、寄存器的副本和线程本地存储（Thread-local storage），其他资源都是进程中共享的，所以其创建和销毁产生的开销远低于进程。内核线程的切换开销相对较低，只需要保存恢复寄存器和堆栈指针。但是内核线程需要消耗内核资源，一个系统中的内核线程是有限的。 %}

{% note radiation blue::**用户线程**: 在用户空间中实现的的线程叫做用户线程。用户线程完全在在用户空间进行调度和管理，换句话说用户自己管理的线程叫做用户线程，所以内核并不知道用户线程，在用户线程切换时，完全不需要与内核交互，所以其上下文切换非常高效。完全不需要内核的参与也带来和坏处，用户必须完整的实现用户线程的调度，而且一旦发生阻塞将导致其他用户线程无限等待，要解决起来十分困难。 %}

{% endfolding %}

#### 线程模型-内核级线程

{% folding orange::内核线程:用户线程 = 1:1%}

{% endfolding %}

#### 线程模型-用户级线程

{% folding ::内核线程:用户线程 = 1:N%}

{% endfolding %}

#### 线程模型-混合线程

{% folding ::内核线程:用户线程 = M:N%}

{% endfolding %}

## 时间线

{% timeline %}

{% timenode 2022-10-23 [JEP 436: Virtual Threads (Second Preview)](https://openjdk.org/jeps/436) %}

Java虚拟线程的二阶段预览

{% endtimenode %}

{% timenode 2022-04-28 [JEP proposed to target JDK 19: 425: Virtual Threads (Preview)](https://mail.openjdk.org/pipermail/jdk-dev/2022-April/006530.html) %}

令人激动的消息，Java继绿色线程（Green Thread）退出Java平台后重新迎来的一个新的用户模式线程，现在虚拟线程带着Java平台的高并发期望回来。

{% endtimenode %}

{% timenode 2022-04-06 [JEP: 425: Virtual Threads (Preview)](https://openjdk.org/jeps/425) %}

将虚拟线程以预览API的形式引入Java平台。

{% endtimenode %}

{% timenode 2021-09-04 [JEP 418: Internet-Address Resolution SPI](https://openjdk.org/jeps/418) %}

为主机名和地址解析定义服务提供者接口 (SPI)，以便 java.net.InetAddress 可以使用平台内置解析器以外的解析器。平台内置解析器会阻塞至系统返回结果。Project Loom现在可以利用SPI直接实现 DNS 客户端协议，而不会阻塞。

{% endtimenode %}

{% timenode 2020-04-21 [State of Loom](https://cr.openjdk.java.net/~rpressler/loom/loom/sol1_part1.html) %}

给出的virtual thread的定义。

{% endtimenode %}

{% timenode 2021-05-10 [Networking I/O with Virtual Threads](https://inside.java/2021/05/10/networking-io-with-virtual-threads/) %}

讨论虚拟线程在网络I/O的应用

{% endtimenode %}

{% timenode 2019-10-22 [A lightweight thread is a Thread](https://mail.openjdk.org/pipermail/loom-dev/2019-October/000796.html) %}

在本篇文章中Project Loom的Owner——Alan Bateman讨论了轻量级线程（Lightweight Thread）、纤程（Fiber）、线程（Thread）的关系，他认为轻量级线程应该用线程来表示。

{% endtimenode %}

{% timenode 2019-07-29 [Early Access Loom Builds](https://mail.openjdk.org/pipermail/loom-dev/2019-July/000633.html) %}

Loom的第一个二进制版本。

{% endtimenode %}

{% timenode 2018-11-14 [Project Loom的诞生](https://inside.java/2018/11/14/loomdevoxx/) %}

Project Loom的目的：使编写、调试、分析和维护**并发应用程序**变得更加容易。

{% endtimenode %}

{% endtimeline %}
