---
title: 计算共形几何笔记
date: 2024-03-01 08:00:00
tags: [数学,计算几何,笔记,图形学]
categories: 笔记
keywords: 数学, 计算几何,更新中
comments: true
top_img: '/image/CCG/title.png'
cover: '/image/CCG/title.png'
ai: true
description: '2023 计算共形几何'
background: '#fff'
swiper_index: 1
top_group_index: 1
mathjax: true
katex: true
---
# Foreword

不同的几何研究不同变换群下的不变量，在工程和医疗领域，常用的几何包括拓扑 （topology）、共形几何（conformal geometry）、Riemann 几何（Riemannian geometry） 和曲面微分几何 （surface differential geometry），其对应的变换群为拓扑变换群、共形变换群、等距变换群和曲面在欧氏空间中的刚体变换群，这些变换群构成了嵌套子群序列。

<div align=center>刚体变换群◁等距变换群◁共形变换群◁拓扑同胚群.</div>

不同变换群下的不变量也可视作不同的结构，这些结构彼此构成层次关系。以嵌入三维欧氏空间中的曲面为例，曲面具有拓扑结构、共形结构、 Riemann 度量结构和嵌入结构.后面的结构以前面的结构为基础，内涵逐步丰富。

- 拓扑结构

    一个曲面可以连续变形成另一个曲面，不发生撕破或粘连，即表示拓扑等价

- 共形结构
  
    一个曲面可以通过保持角度不变的变换变形成另一个曲面，即表示共形等价  

- Riemann 度量结构

    一个曲面可以通过保持长度不变的变换变形成另一个曲面，即表示等距等价


## Algebraic topology

### Fundamental Group and Covering Space

#### 定义

- 路径

    一个路径是一个连续映射 $f: [0,1] \to X$，其中 $X$ 是一个拓扑空间，$f(0)$ 是路径的起点，$f(1)$ 是路径的终点。
    

- 同伦

    两个路径 $f,g: [0,1] \to X$ 是同伦的，如果存在一个连续映射 $H: [0,1] \times [0,1] \to X$，使得 $H(0,t) = f(t)$ 和 $H(1,t) = g(t)$ 对所有 $t \in [0,1]$ 成立。这个连续映射 $H$ 被称为 $f$ 和 $g$ 之间的同伦。

- 同伦类

    一个路径 $f: [0,1] \to X$ 的同伦类是所有与 $f$ 同伦的路径的集合。

- 环路

    一个环路是一个路径 $f: [0,1] \to X$，使得 $f(0) = f(1)$。

- 环路乘积

    两个环路 $f: [0,1] \to X$ 和 $g: [0,1] \to X$ 的环路乘积 $f \cdot g$ 是一个路径，定义为

    $$
    (f \cdot g)(t) = \begin{cases}
    f(2t), & 0 \le t \le \frac{1}{2}, \\
    g(2t-1), & \frac{1}{2} \le t \le 1.
    \end{cases}
    $$

- 环路的逆

    一个环路 $f: [0,1] \to X$ 的逆环路 $f^{-1}$ 是一个路径，定义为 $f^{-1}(t) = f(1-t)$。

- 基本群

    一个拓扑空间 $X$ 的基本群 $\pi_1(X)$ 是所有 $X$ 中的环路的同伦类的集合，关于环路乘积的同伦类的群。

- 覆叠空间

    一个拓扑空间 $Y$ 是另一个拓扑空间 $X$ 的覆叠空间，如果存在一个连续映射 $p: Y \to X$，使得对于 $X$ 中的每一个点 $x$，存在一个开集 $U$ 包含 $x$，使得 $p^{-1}(U)$ 是 $Y$ 中的一些不相交的开集的并，每一个这样的开集 $p^{-1}(U)$ 被称为 $Y$ 中的一个片。

#### Fundamental Group

##### 词群表示

词群：用来表示拓扑空间同伦群的方式

给定一组“字母”，由字母组成词，字母是词的**生成元**，词是字母构成的序列，用$\{g_1, g_2, \cdots, g_n\}$表示生成元，用$g_1^{\pm 1}, g_2^{\pm 1}, \cdots, g_n^{\pm 1}$表示生成元的逆元，用$g_1^{m_1} g_2^{m_2} \cdots g_n^{m_n}$表示词，其中$m_1, m_2, \cdots, m_n$是整数。


##### 基本群的典范表示

基本群的生成元：$\{a_1,b_1,a_2,b_2,\cdots,a_g,b_g\}$

满足以下条件：
    $$
    \begin{cases}
    a_i b_j = \delta_i^j, \\
    a_i a_i = 0 ,\\
    b_i b_i = 0
    \end{cases}
    $$

