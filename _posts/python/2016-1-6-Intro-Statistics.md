---
category: python
published: true
layout: post
title: 『 Python 』统计学——基础概念
description: 
---

如何有效的利用大数据就需要懂得从一堆数据中挖掘出有价值的信息，而数据挖掘又是一门交叉领域，涉及到数据库技术、统计学、神经网络、模式识别、机器学习、信息提取、高性能计算等众多领域，并在工业、电子商务、科学、通讯、医疗等行业得到广泛的应用。所以，各行各业对数据挖掘都有各自的需求。本文主要介绍统计学一些基础概念，后续文章会陆陆续续介绍以上提到的其它知识。

这里是一组专门准备的统计数据，用来介绍接下来的一些基本概念。
统计数据：11、5、9、10、3、1、8、10、4、12
从小到大：1、3、4、5、8、9、10、10、11、12

**均值(Mean)**，是表示一组数据集中趋势的量数，是指在一组数据中所有数据之和再除以这组数据的个数。它是反映数据集中趋势的一项指标。
本组数据的均值：(1+3+4+5+8+9+10+10+11+12) ÷ 10 = 7.3

**中位数(Median)**，统计学中的专有名词，代表一个样本、种群或概率分布中的一个数值，其可将数值集合划分为相等的上下两部分。对于有限的数集，可以通过把所有观察值高低排序后找出正中间的一个作为中位数。如果观察值有偶数个，通常取最中间的两个数值的平均数作为中位数。
由于本组数据是偶数个，所以中位数计算值为：(8+9) ÷ 2 = 8.5

**众数(Mode)**，是一组数据中出现次数最多的数值，叫众数，有时众数在一组数据中有好几个，用M表示。 简单的说，就是一组数据中占比例最多的那个数。
本组数据的众数为：10

**极差(Range)**，是用来表示统计数据中的变异量数(measures of variation)，其最大值与最小值之间的差距；即最大值减最小值后所得之数据。这个数字越大，表示分得越开，最大数和最小数之间的差就越大；该数越小，数字键就越紧密，这就是极差的概念！
本组数据的极差：12－1 ＝ 11

**中程数(Mid-Range)**，中程数是考虑数据集中趋势的又一种方式，其使用数据的最大值和最小值的平均值。
本组数据的中程数：(12+1) ÷ 2 = 6.5

基本概念先介绍到这里，下一篇文章将介绍箱形图（又名盒须图）概念，并用Python编程演示从样本数据如何生成箱形图，敬请期待。

参考资料：[百度百科](http://baike.baidu.com)

如有错误，敬请指正！
