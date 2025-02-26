---
title: CG Log
date: 2023-11-04 08:00:00
updated:
tags: [图形学,三维模型,总结,指南,规划]
categories: 随笔
keywords: 工业软件,图形学,入门经验
description: 记录到目前为止的图形学踩坑感想，可能可以当作一个入坑指南
top_img: 'image/CGI.jpeg'
comments: true
cover: 'image/CGI.jpeg'
toc:
toc_number:
toc_style_simple:
copyright:
copyright_author:
copyright_author_href:
copyright_url:
copyright_info:
mathjax:
katex:
ai: true
aplayer:
highlight_shrink:
aside: true
swiper_index: 3
top_group_index: 3
background: "#fff"
---

记录到目前为止的图形学踩坑感想，可能可以当作一个入坑指南
<!-- more -->
## 前言

随着笔者上完本校工业软件特色班相关课程，刷完 GAMES101 ，在实验室帮了一点点四边形网格生成的工作，手撸了一套曲面配准代码,前前后后做的工作加起来也有那么一些,自认为在计算机图形学方面是入门了。这篇文章主要介绍一下我的踩坑理由和感想，同时有意向进入工业软件特色班的同学也可以获得一点点参考。

## 入坑

### 首先是一段视频

话不多说，先上[b站](https://www.bilibili.com/video/BV1Hk4y1q7Rz/),或者[YouTube](https://www.youtube.com/watch?v=yTimLYi9XJ4)。

可以说这段视频是我明确要走这条路的最根本原因，虽然现在来看这段视频里讲的内容没有第一次看时那么魔幻高深，主要涉及的内容也是这个方向已经被探索得差不多的渲染方向，但其震撼效果对我而言依然不减。


### 其次是一些比较功利性的东西

可以上招聘网站上看一眼游戏引擎的岗位，或者是中望，风雷这些国产工业软件。因为既需要某些特定领域的数学门槛，比如拓扑理论，偏微分方程，同时也要求代码复现的基本功。在这个领域竞争相对没有那么大，但同时需要付出的代价就是要去理解一些很抽象的东西，这些内容稍后会在劝退部分详细介绍一下。

## 方向

主要以 IMIS-Lab 实验室里于本文发表时我知道的一些本科生可以参与的工作来介绍

### 三角网格生成

*   输入：点云
*   输出：以点云中的点为顶点拼凑出的三角形组成的面

#### 举例 - Delaunay 三角剖分算法

在二维平面上简单概述一下思路：先画一个大三角形包括了所有的输入点云，随后迭代点，将该点与该点所在的三角形三条边连接，最后删除最外层的边

在这个过程中可能出现钝角三角形可以通过凸包算法优化，即针对新生成的三角形，作其外接圆，其中不应该有初顶点外的已经被迭代的点。如果有，则需要对包含这个点且和该三角形有公共边的三角形实行边交换算法。将两个三角形公共边删除，并且将拼凑起来构成的四边形的另一条对角线作为新的边。

如果进入工业软件特色班的话，郑老师的 CAE 前处理课程的第一个上机实验就会是这个。这算是一个门槛，越过了代码能力方面基本就没什么问题了。

### 四边形网格生成

实验室里已经有一套相对成熟的算法,通过 Morse-Smale Complex 构建四边形的划分然后进行网格精细化。

### 体网格

这一块的知识相对复杂一点，包括四面体网格和六面体网格，印象中也都是研究生的师兄师姐在负责，我本人也可以说完全没有接触过，给不出太多的建议。

### 样条

贝塞尔曲线和B样条，然后还有样条曲面，不算是一个难点，也不是一个创新点，但使用前景是很丰富的。这也可能会是我接下来的工作，因为毕设内容主要是[曲面配准](/blog/registration/)，如果想要有创新点那就是摆脱点的约束，样条就是一个很好的思路。

### 其他

以上是我稍微接触过或者了解过的一些方向，实际上远不止这些。如果想干活的话也不需要很高深的前置知识，学点 QT 就能来帮忙设计可视化界面了，对这方面的科研工作感兴趣的话可以来看看[传帮带](https://www.kdocs.cn/l/cfSLy50pDUwP)（虽然有段时间没更新了）,实验室的师兄师姐还有老师都很热情。不管还缺不缺名额，看到感兴趣的方向直接联系就好了。

## 劝退帖

要让我来评价图形学方向，那就是：不卷但是很难。这个方向显然不是所有人都建议无脑入的。

1.  正如之前所说，相关的工作既需要编码能力，也需要数理基础。不仅如此，在一些有限元仿真工作，光线追踪算法里还包括了应用力学，辐射度量学等，想要学下去就一定要有持久的好奇心。
2.  相比起 CV ，NLP 这些受众广泛且热门的方向，图形学涉及的期刊会议会少很多，论文也远远没有热门行业好发，水刊更是少中之少（不知道这算好事还是坏事🤪）
3.  对 C/C++ 要很熟悉，很多框架要学会自己去找。相比起之前做的图像分割项目，我毕设感觉到最困难的一点就是 C++ 的库少而且没有一款主流的包管理工具（比如 python 的 pip，js 的 npm），每导入一个库都要clone下来，编译安装，然后还要修改自己的 cmake 文件，再把头文件移过来，再链接 lib 文件，刚开始的时候对 cmake 和 C++ 不是很熟悉的话搞个一整天就导一个库时很正常的。有的工作也因此不太好统一，比如这个项目计算二次线性规划的库使用的是 proxsuite ，而想重用另一个项目代码时有可能需要使用另一个二次线性规划库比如 osqp-eigen ，那很有可能得两头的文档都要看。

## 入门的资源

1.  [GAMES101](https://sites.cs.ucsb.edu/~lingqi/teaching/games101.html) 是国人讲的质量很高的公开课了，主要涉及的是渲染，样条也讲了一点。课程内容本身可能不能很好对接上实验室的工作，但最为图形学的入门课程可以说是必刷的。此外 GAMES 平台还有很多优质的课程，可以根据自己的兴趣来听。
2.  顾险峰老师的 [计算共形集合](https://space.bilibili.com/446605493) ，这个课程基本涵盖了参加实验室工作可能要用到的所有数学知识，课程使用的 [Meshlib](https://github.com/mathsyouth/meshlib) 框架可以很好的处理网格，可以说是 IMIS-Lab 的官方指定合作框架。
3.  多来听听实验室的讲座，学完前两条资源就算正式入门了，接下来跟着做几个项目读几篇论文，在这个领域该不该走，该怎么走应该就有思路了
