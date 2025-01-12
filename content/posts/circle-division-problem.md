---
title: 分圆问题：一个诡异的数列规律
date: 2018-08-14
categories:
- Fun
tags:
- Math
- 3Blue1Brown
---

**问题：**

在圆上任取 $n$ 个点，将每对点用直线连接，并规定三条线不能交于一点，这些直线会将圆分割成多少份？

<!-- more -->

---

首先我们列出一些简单情况来寻找规律：

- $2$ 个点将圆分为 $2$ 份
- $3$ 个点将圆分为 $4$ 份
- $4$ 个点将圆分为 $8$ 份
- $5$ 个点将圆分为 $16$ 份

![Simple Cases](https://i.imgur.com/QvMBlyA.png)

看上去这个数列的规律已经非常明显，然而，当点数增加到 $6$ 个的时候，分割的份数不是我们预料的 $32$，而是 $31$。

![Pattern Fail at 6](https://i.imgur.com/TqeOUAo.png)

为了找到这个数列的通项公式，我们使用**欧拉示性数公式**（Euler's Characteristic Formula）来进行推导：
$$
V-E+F=2
$$
这个公式的意思是，在任何连通平面图中，顶点数减去边数加上面数总是等于 $2$。

为了利用这个公式得到分割的份数即面数，我们需要先求出顶点和边的数量。

首先求顶点数：圆内的每个交点都对应圆上的四个点交叉相连，因此圆内的交点共有 $C(n, 4)$ 个，加上圆上的 $n$ 个点，因此 $V=n+C(n, 4)$。

再求边数：圆内的每个交点的度数为 $4$，度数是与点相连的边的个数，圆上的每个点都与除此之外的点相连，因此度数为 $n-1$，所以总度数为 $4C(n, 4)+n(n-1)$， 由于每条边对于总度数的贡献为 $2$，再加上连接圆上顶点的弦的数量，最后得到边的数量 $E=2C(n, 4)+C(n, 2)+n$。

![Find the Correct Pattern](https://i.imgur.com/F7Tp3gW.png)

将结果代入到欧拉公式，并考虑到圆外区域算为一个面，则分割的份数为：
$$
\begin{equation*}
\begin{split}
F-1 &= E-V+2-1 \\
&= (2C^n_4+C^n_2+n)-(n+C^n_4)+1 \\
&= C^n_4+C^n_2+1
\end{split}
\end{equation*}
$$
由排列组合公式，上式可以继续分解：
$$
C^n_4+C^n_2+1 = C^{n-1}_4+C^{n-1}_3+C^{n-1}_2+C^{n-1}_1+C^{n-1}_0
$$
这也就解释了当 $n<6$ 的时候结果总是成 $2$ 的幂次。

# 参考

1. [3Blue1Brown Video](https://youtu.be/YtkIWDE36qU)
2. [Dividing a Circle into Areas — Wiki](https://en.wikipedia.org/wiki/Dividing_a_circle_into_areas)
