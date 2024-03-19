---
title: 希尔伯特曲线
date: 2018-08-06
categories:
- Fun
tags:
- Math
- 3Blue1Brown
---

**问题：**

如何将二维的图像转化为音乐？

<!-- more -->

---

在数学上，即是要寻找一个从像素空间映射到频率空间的一个函数。

首先定义 **PHC (Pseudo-Hilbert Curve)**：将正方形划分为 $2\times 2$ 的网格，画出如下图所示的曲线，称为 **Order 1 PHC** ，记为 $PHC_1$

![PHC 1](https://i.imgur.com/89p5k2d.png)

进一步将正方形划分为 $4\times 4$ 的网格，分别画出 $4$ 个 $PHC_1$ 并首尾相接，之后进行调整，得到 **Order 2 PHC** ，记为 $PHC_2$ 。

![PHC 2](https://i.imgur.com/pr3wzG5.png)

如此继续下去，希尔伯特曲线就是这一族曲线的极限
$$
HC(x)=\lim_{n\to\infty} PHC_n(x)
$$
回到原问题，将图像划分并使用希尔伯特曲线将所有像素串连，曲线上的每一点都对应了一个频率，那么我们就有了一个从像素空间到频率空间的映射。

**但为什么这样的曲线就比其它的曲线更加实用？**

因为随着图像的不断细分，图像上的一点所对应的频率会趋向一个特定的值。即是说明图像像素的提高并不会完全改变对应的音乐。

希尔伯特曲线的特点：

1. 良定义的：极限存在
2. 连续的
3. 空间填充的

# 参考

1. [Video](https://www.bilibili.com/video/av4201747)
2. [Hilbert Curve](https://en.wikipedia.org/wiki/Hilbert_curve)
