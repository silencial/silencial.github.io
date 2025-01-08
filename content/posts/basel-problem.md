---
title: 巴塞尔问题：著名公式背后的几何学
date: 2018-08-19
categories:
- Fun
tags:
- Math
- 3Blue1Brown
image:
---

**问题：**

证明 $1+\dfrac{1}{4}+\dfrac{1}{9}+\dfrac{1}{16}+\cdots=\dfrac{\pi^2}{6}$

<!-- more -->

---

这一问题实际上有多种代数方法证明，但由于公式中出现了 $\pi$，则必定在几何学中可以找到圆来对应。

想象一位观察者站在数轴的原点，并在所有的正整数的位置放置一座灯塔。假设观察者接收到的第一座灯塔的亮度为 $1$。由于亮度和距离的平方成反比，则第 $n$ 座灯塔的亮度为 $\dfrac{1}{n^2}$。这样我们就将原问题转化为了物理的表达形式，我们只需要证明原点所接收到的总亮度为 $\dfrac{\pi^2}{6}$ 即可。

![Basel Problem in Different Form](https://i.imgur.com/qXkRWEt.png)

初看上去，我们只不过是将原问题换了一个描述方法，但是这一描述方法真正有用的地方在于它能够进一步提出问题：是否能够重新排列这些灯塔而不改变观察到的总亮度？即将问题转化成更易求解的等价形式。

想象二维平面的某一点有一座灯塔，连接该灯塔与原点，再经过灯塔作一条垂线，最后在这条线和坐标轴的两个交点上放置灯塔，如下图所示：

![Equivalent Transformation from 1 Lighthouse to 2](https://i.imgur.com/XLbruPK.png)

由**倒数勾股定理**可知，$\dfrac{1}{a^2}+\dfrac{1}{b^2}=\dfrac{1}{h^2}$。即原灯塔的亮度等于 $A, B$ 灯塔的亮度之和。

现在有了在不改变亮度的条件下更改灯塔位置的方法，可以将原问题转化为下列形式:

想象观察者在周长为 $2$ 的圆上的一点并正对着一座灯塔，则灯塔的亮度为 $\dfrac{1}{d^2}=\dfrac{\pi^2}{4}$。作一个与小圆在观察者处相切的两倍大的圆，并过小圆顶端作切线，将原先的灯塔替换为位于切线与大圆交点上的两座灯塔。

![Equivalence in Circles](https://i.imgur.com/AykC1B2.png)

因为直径所对应的圆周角为直角，由倒数勾股定理可知，这两座灯塔的亮度之和等价于原灯塔亮度。继续重复此过程，将两座灯塔变为四座：

![Repeating Process](https://i.imgur.com/UnMv08O.png)

同时由对称性可以证明这四座灯塔在圆上等距分布，相邻灯塔的距离为 $2$。将这一过程无限进行下去，即得到了一条水平线，在两个方向上均匀分布着无限座灯塔，亮度总和依旧为 $\dfrac{\pi^2}{4}$。这个结果等价于下式：
$$
\sum_{k=-\infty}^{+\infty} \left(\frac{1}{2k+1}\right)^2 = \frac{\pi^2}{4}
$$
由于
$$
\begin{gather*}
\frac{1}{4}\sum_{k=1}^{\infty} \left(\frac{1}{k}\right)^2 = \sum_{k=1}^{\infty} \left(\frac{1}{2k}\right)^2 \\
\sum_{k=1}^{\infty}\left(\frac{1}{2k}\right)^2 + \sum_{k=0}^{\infty}\left(\frac{1}{2k+1}\right)^2 = \sum_{k=1}^{\infty} \left(\frac{1}{k}\right)^2
\end{gather*}
$$
因此
$$
\begin{equation*}
\begin{split}
\sum_{k=1}^{\infty} \left(\frac{1}{k}\right)^2 &= \frac{1}{4}\sum_{k=1}^{\infty} \left(\frac{1}{k}\right)^2 + \frac{\pi^2}{8} \\
&= \frac{\pi^2}{8}\times\frac{4}{3} \\
&=\frac{\pi^2}{6}
\end{split}
\end{equation*}
$$

# 参考

1. [Video](https://www.bilibili.com/video/av20400157)
2. [Basel Problem](https://en.wikipedia.org/wiki/Basel_problem)
