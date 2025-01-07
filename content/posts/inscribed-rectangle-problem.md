---
title: 用莫比乌斯带巧解内接矩形问题
date: 2018-08-07
lastmod: 2025-01-06
categories:
- Fun
tags:
- Math
- 3Blue1Brown
---

**问题：**

对于任意的闭合连续曲线，是否总能在其上找到四个点形成一个矩形？

<!-- more -->

---

首先我们不再关注单个而是成对的点，并利用矩形的性质：对于平面上任意两对不同的点 $a, c$ 和 $b, d$，只需确保它们有相同的中点，且 $a, c$ 间的距离等于 $b, d$ 点的距离，那么即可以保证这四个点可以组成矩形。这样寻找内接矩形问题就转化为了寻找两对点的问题。

我们定义一个函数 $f(A,B) = (x,y,z)$ 将环路上的点对映射到 3 维空间的一个点。

设闭合回路位于 3 维空间中的 $X$–$Y$ 平面上，对于给定的一对点，记它们中点为 $M$，距离为 $d$，将位于 $M$ 正上方 $d$ 个单位的点画出:

![Mapping 2 2D points to 1 3D point](https://i.imgur.com/pl01lUp.jpeg)

对环路上的所有点对进行同样的操作，则在平面上方画出了某种曲面：

![2D Point Pairs to 3D Surface](https://i.imgur.com/rm26hDD.jpeg)

注意一点重要的性质
$$
f(X,X) = X
$$
即该曲面一定以环路为底。同时可以看出曲面必定连续。原问题等价于证明这一曲面存在自相交，即有两对不同的点对被映射到同一点。

下一步，我们希望找到另一种曲面，与环路上的点对存在一一对应关系，则点对到曲面的映射就可以转换为两个曲面的映射，从而证明自相交。

点对可分为两种：**有序对** $(x,y)\ne (y,x)$ 和**无序对** $(x,y) = (y,x)$，下面分别找到这两种分别对应的曲面。

有序对：

1. 将环路在某一点切开并拉直为 $[0,1]$ 区间的 $X$ 轴，再用一个区间构成 $Y$ 轴，这样在 $[0,1]\times [0,1]$ 上的单位正方形中的点都对应于环路上的一对点。

2. 注意在正方形的边界上存在重复的点对，因此将正方形的左右边界进行粘贴，再对上下边界进行粘贴，可以得到一个环面。

   ![Transform Curves to Squares](https://i.imgur.com/ZG3T2C3.jpeg)

3. 该环面上的每一点都与环路上的有序对一一对应，且这种映射是连续的。

![Equivalence of Torus to Ordered Point Pairs on the Loop](https://i.imgur.com/MSkibjE.jpeg)

无序对：

1. 同样先将环路中的点对映射至正方形，$(x,y) = (y, x)$ 意味着正方形上的点关于 $y=x$ 对称，先将其沿对角线对折成为三角形。

   ![Transform to Unordered Point-Pairs 1](https://i.imgur.com/MSzChy0.jpeg)

2. 对三角形的边界进行粘贴，此时注意粘贴的方向性。首先沿黄线切开，将其中一个小三角形进行翻转并重新拼接成为一个正方形

    ![Transform to Unordered Point-Pairs 2](https://i.imgur.com/ELR7Mep.jpeg)

3. 对该正方形的黄色边界进行粘贴，为了保持方向一致，需要将其中一个边界扭转并连接到另一个边界上，这使我们得到了一个莫比乌斯带，该表面上的每一个点都与环路上的无序对一一对应

   ![Transform to Unordered Point-Pairs 3](https://i.imgur.com/F8iDRMu.jpeg)

4. 注意莫比乌斯带的红色边界对应的是 $(x,x)$ 这样的点对

因此，环路上的无序点对与莫比乌斯带上的点一一对应且连续，所以也存在从莫比乌斯带到原曲面的一个连续映射。由于该映射必须保证莫比乌斯带的边界映射到平面上的环路，且剩余部分必须在平面上方，可以看出这样的映射必将导致自相交。



**Update**: 在 2020 年，证明了对于光滑曲线，总能找到四个点形成长宽具有任意比值的矩形。该定理的证明需要在 $(x,y,d)$ 的基础上加入点对连线与坐标轴的夹角 $\theta$，即需要将莫比乌斯带映射到 4 维空间。

# 参考

1. [3Blue1Brown Video](https://youtu.be/IQqtsm-bBRU)
2. [Inscribed Square Problem — Wiki](https://en.wikipedia.org/wiki/Inscribed_square_problem)
