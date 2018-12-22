---
category: linux
published: true
layout: post
title: 『 Java 』Netty In Action
description:
---

公司项目升级到Netty3.10.0.Final后的性能表现，观察了几天，没有发现之前遇到的内存一直不释放的问题，附上jvisulvm截图：

![1](../images/jvm_status/1.png)

![2](../images/jvm_status/2.png)

![3](../images/jvm_status/3.png)

![4](../images/jvm_status/4.png)

![5](../images/jvm_status/5.png)

最后一张图是手工执行了垃圾回收后的曲线图，证明每天逐渐升高的内存确实能够被回收，悬着的心终于可以落下来了。

经过这段时间的调整，项目底层日趋稳定，也认识到自己在JAVA底层知识的薄弱，还需要好好学习。
