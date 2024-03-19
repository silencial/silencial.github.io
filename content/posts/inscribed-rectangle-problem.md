---
title: 用莫比乌斯带巧解内接矩形问题
date: 2018-08-07
categories:
- Fun
tags:
- Math
- 3Blue1Brown
---

**问题：**

对于任意的闭合环路，是否总能在其上找到四个点形成一个矩形？

<!-- more -->

---

该问题也被称为内接矩形问题，而内接正方形问题至今没有解答方案。

首先我们不再关注单个而是成对的点，并利用矩形的性质：对于平面上任意两对不同的点 a，c 和 b，d ，只需确保它们有相同的中点，且 a，c 间的距离等于 b，d 点的距离，那么即可以保证这四个点可以组成矩形。这样寻找闭合环路内接矩形问题就转化为了寻找两对点的问题。

我们定义一个函数 $f(A,B) = (x,y,z)$ 将环路上的点对映射到 3 维空间的一个点

设闭合回路位于 3 维空间中的 $X-Y$ 平面上，对于给定的一对点，记它们中点为 $M$ ，距离为 $d$ ，将位于 $M$ 正上方 $d$ 个单位的点画出:

![Mapping 2 2D points to 1 3D point](https://i.imgur.com/FyqvJMx.png)

对环路上的所有点对进行同样的操作，则在平面上方画出了某种曲面：

![2D Point Pairs to 3D Surface](https://i.imgur.com/PSJxn9v.png)

注意一点重要的性质
$$
f(X,X) = X
$$
即该曲面一定以环路为底，同时曲面必定连续。

我们的目标即是要证明这一曲面存在碰撞，即有两对不同的点对被映射到同一点。

下一步，我们需要找到一个二维曲面，与环路上的点对存在一一对应关系。

点对可分为两种：**有序对** $(a,b)\ne (b,a)$ 和**无序对** $(a,b) = (b,a)$

首先寻找有序对所对应的自然形状：

1. 将环路在某一点切开并拉直为 $[0,1]$ 区间的 $X$ 轴，再用一个区间构成 $Y$ 轴，这样在 $[0,1]\times [0,1]$ 上的单位正方形中的点都对应于环路上的一对点。

2. 由于在正方形的边界上存在重复对应的点对，因此将正方形的左右边界进行粘贴，再对上下边界进行粘贴，即得到了一个环面。

   ![Transform Curves to Squares](https://i.imgur.com/vfYCAqY.png)

3. 该表面上的每一个点都与环路上的有序对一 一对应。

![Equivalence of Torus to Ordered Point Pairs on the Loop](https://i.imgur.com/YvficIg.png)

无序对：

1. 由于正方形上的点关于 $y=x$ 对称，先将其沿对角线对折成为三角形。

   ![Transform to Unordered Point-Pairs 1](https://i.imgur.com/6jFWigs.png)

2. 需要将三角形的下边界粘贴到右边界，此时注意粘贴的方向性。首先沿对角线切开，将其中一个小三角形进行翻转并重新拼接成为一个小正方形

    ![Transform to Unordered Point-Pairs 2](https://i.imgur.com/0RE4FSp.png)

3. 需要将该正方形的黄色边界再次粘贴，得到莫比乌斯带，该表面上的每一个点都与环路上的无序对一 一对应

   ![Transform to Unordered Point-Pairs 3](https://i.imgur.com/4nDr1wj.png)

4. 注意莫比乌斯带的红色边界对应的是 $(x,x)$ 这样的点对

得到了平面上的无序点对所对应的自然形状是莫比乌斯带这一结论后，自然存在从莫比乌斯带到曲面的一个映射。而该映射又必须保证莫比乌斯带的边界正好映射到平面上的环路。由于莫比乌斯带的特殊形状，将它的边界粘到二维平面必定会使其自身相交，原命题得证。

# 参考

1. [Video](https://www.bilibili.com/video/av7053135)
2. [Inscribed Square Problem](https://en.wikipedia.org/wiki/Inscribed_square_problem)
