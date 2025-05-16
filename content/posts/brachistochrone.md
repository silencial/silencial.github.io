---
title: 最速降线问题
date: 2018-08-07
categories:
- Fun
tags:
- Math
- 3Blue1Brown
---

**问题：**

在只考虑重力作用的情况下，一质点从点 A 沿某条曲线到点 B，问怎样的曲线能使所需时间最短？

<!--more-->

---

这一问题被称为最速降线问题（Brachistochrone），由约翰·伯努利在 1696 年提出。

# 费马原理与斯涅耳定律

约翰·伯努利的证明非常巧妙，利用了**费马原理**：一束光从 A 点传播到 B 点总是沿着所需时间最短的路径。

从费马原理可以导出**斯涅尔定律 (Snell's Law)**。当光线从一种介质传播到另一种具有不同折射率的介质时，其与界面法线的夹角和在不同介质中的光速满足以下关系：

$$
\frac{\sin(\theta_1)}{v_{\text{air}}} = \frac{\sin(\theta_2)}{v_{\text{water}}}
$$

![Snell's Law](https://i.imgur.com/uPl2gsF.jpeg)

# 光的传播

因此可以用光代替原问题中的质点，用不同折射率的介质代替曲线，使得在任意一点，光在其中的速度等于质点的速度，则光的传播路径就是原问题所求的曲线。

![Equivalence to Light Refraction](https://i.imgur.com/t32yOJx.jpeg)

剩下的问题就是找到光在任意一点的速度。由能量守恒原理，重力势能转化为动能，因此小球在曲线上的速度只与竖直距离有关，记曲线到顶点的竖直距离为 $y$，则有：

$$
v = \sqrt{2gy}
$$

则根据斯涅尔定律可得：

$$
\frac{\sin(\theta)}{\sqrt{y}} = \text{constant}
$$

这就是我们要求的曲线方程。

# 摆线

这一曲线方程实际上就是**摆线**，即圆在一条直线上滚动时，圆边界上一定点所形成的轨迹。下面证明为什么摆线满足该方程。

设圆上定点为 P，圆与水平线的切点为 C，圆滚动时，点 C 充当点 P 的瞬时旋转中心：

![Instant Rotation Center](https://i.imgur.com/RwkCvqT.jpeg)

所以 CP 垂直于摆线过点 P 的切线，又因直角圆周角对应直径，所以该切线一定过圆的最低点，交点与 C 的连线即为圆的直径。设直线与切线的夹角为 $\theta$，根据相似三角形，我们可以算出点 P 到水平线的距离：

![Distance from P to C](https://i.imgur.com/omiKQOW.jpeg)

即

$$
\frac{\sin(\theta)}{\sqrt{y}} = \frac{1}{\sqrt{D}} = \text{constant}
$$

# 变分法

从数学的方法思考这一问题，设曲线方程为 $y=y(x)$，速度与纵坐标有 $v=\sqrt{2gy}$ 的关系，同时

$$
v = \frac{ds}{dt} = \sqrt{1+y'^2} \dfrac{dx}{dt}
$$

其中 $s$ 代表曲线的弧长，$t$ 表示时间，于是

$$
dt = \frac{\sqrt{1+y'^2}}{v}dx = \frac{\sqrt{1+y'^2}}{\sqrt{2gy}}dx
$$

所以从 A 到 B 的时间为

$$
t = J(y) = \int_A^B \frac{\sqrt{1+y'^2}}{\sqrt{2gy}}dx
$$

这样时间 $t$ 就被写成了关于 $y$ 的泛函，而求时间最短问题变成了在满足边界条件：

$$
y(A) = 0\quad y(B) = y_B
$$

下的对泛函 $J(y)$ 求极值问题，即变分问题。

考虑对泛函 $\displaystyle J(y) = \int_b^a F(x,y,y')dx$ 变分

$$
\begin{equation*}
\begin{split}
\delta J(y) &= J(y+\delta y) - J(y) \\
&= \int_a^b \left[\frac{\partial F}{\partial y}\delta y + \frac{\partial F}{\partial y'}\delta y'\right]dx \\
&= \int_a^b \left[\frac{\partial F}{\partial y} - \frac{d}{dx}\left(\frac{\partial F}{\partial y'}\right) \right] \delta y dx
\end{split}
\end{equation*}
$$

令 $\delta J(y) = 0$ 即得

$$
\frac{\partial F}{\partial y} - \frac{d}{dx}\left(\frac{\partial F}{\partial y'}\right) = 0
$$

将上述方程代入，即将变分问题转化为了微分方程问题，解此微分方程即得所求曲线。

# 参考

1. [3Blue1Brown Video](https://youtu.be/Cld0p3a43fU)
2. [Brachistochrone Curve — Wiki](https://en.wikipedia.org/wiki/Brachistochrone_curve)
