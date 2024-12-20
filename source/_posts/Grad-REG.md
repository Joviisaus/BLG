---
title: 毕业设计内容概要
date: 2024-06-07 09:38:25
tags: [技术,科研,记录]
description: '毕业设计内容概要'
categories: 记录
keywords: 毕业设计, 概要, Morse , 配准 , 控制抵消
top_img: '/image/GradReg/8Lap3.png'
comments: true
cover: '/image/GradReg/8Lap3.png'
mathjax: true
katex: true
ai: true
swiper_index: 1
top_group_index: 1
background: "#fff"
---

> 交文档的时候出了点意外，Latex 本地编译需要编译六次，一次编译文本图片一次链接文本图片一次编译参考文献一次链接参考文献一次索引参考文献一次把所有的文件都链接起来，外加上 VSCode 是每保存一次就编译一次，于是最后一次修改的时候刚点完保存就迫不及待的重命名 PDF 了第一次编译都没跑完😢。交了个只有文本和一半图片，没有参考文献目录，索引是问号的论文，顺利跳过一辩进入二辩😊。不过这里还是稍微分享一下毕业设计的相关工作。

# 现有工作

参考上一篇文章[📐Registration](/2023/08/20/📐Registration/)，在这个基础上，我对配准算法进行了一些改进，主要是在配准的过程中加入了控制抵消的方法，使得配准的结果更加稳定。

# 创新点

## 更优秀的 Feature Points 选择

在配准的过程中，我们需要选择一些特征点来进行配准，这些特征点的选择对配准的结果有很大的影响。在这里，我提出了一种新的特征点选择方法，使得配准的结果更加准确。

![feature](/image/GradReg/fp.png)

其原理就是同调性，可以证明最大正鞍点到最大值点间和最小负鞍点到最小值点之间的等值面都是具有同调属性的，这样我们就可以通过这些点来选择特征点。

## 控制抵消

配准问题的核心是是构建点对点的映射关系，但根据复现结果，当两组流形点迭代顺序不一样时，原论文的方法会产生大量噪声，效果很差。为了解决这个问题，本文利用提出一套控制抵消的策略。根据该噪声对特征值与特征向量的另灵敏度不一致的问题，利用这个差值测量出了两组流形点的迭代顺序不一致的程度，成功预测出了噪声并应用在去噪策略中,效果相当不错

![summary](/image/GradReg/summary.png)

### 一些问题

面对具有对称性的曲面时，配准方法会出现一些问题，由于局部突出反映到原始流形上，可以有多个映射方案，这个限制在工业上就是无解的，不考虑刚性特征只研究曲率的话，能做成我现在这个样子已经是极限了。

![sc](/image/GradReg/sc.png)

## 网格退化策略

网格退化的意思是，当我们的网格过于密集时，我们可以通过一些策略来减少网格的密度，这样可以减少计算量，更重要的是可以将点对点对齐策略转化成面对面对齐。在工业应用上很难保证两个流形表面离散化程度是一样的，这个策略可以很好的解决这个问题，只研究拉普拉斯低维特征值本身也是原始配准算法的特点，高维度特征本来就被算法本身省略，留着只会徒增输入限制与计算量

# 结论

本文提出了一种新的配准算法，通过控制抵消的方法，使得配准的结果更加稳定。同时，本文还提出了一种新的特征点选择方法，使得配准的结果更加准确。这些方法在实际应用中取得了很好的效果，具有很好的应用前景。


> 一些套话


