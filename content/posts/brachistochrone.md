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

<!-- more -->

---

这一问题被称为最速降线问题（Brachistochrone），由约翰·伯努利在 1696 年提出来挑战欧洲的数学家。

# 费马原理与斯涅耳定律

约翰·伯努利的证明实际上非常巧妙，利用了**费马原理**：

一束光从 A 点传播到 B 点总是沿着尽可能快的路径。

从费马原理实际上可以导出**斯涅尔定律（Snell's Law）**：

考虑光线跟一条垂直于两介质边界所成的角度，该角的正弦值除以光速在从一种介质移到另一种介质时保持不变。
$$
\frac{\sin(\theta_1)}{v_{\text{air}}} = \frac{\sin(\theta_2)}{v_{\text{water}}}
$$
![Snell's Law](https://i.imgur.com/grhevoi.jpg)

# 光的传播

因此原问题可以想象为一束光在不同折射率的介质中传播，既以不同的速度连续地沿着滑道向下走：

![Equivalence to Light Refraction](https://i.imgur.com/HuYwICL.jpg)

当层数不断增加，我们就得到的想要的路径。

有能量守恒原理，重力势能转化为动能，因此：
$$
v = \sqrt{2gy}
$$
又根据斯涅尔定律可得：
$$
\frac{\sin(\theta)}{\sqrt{y}} = \text{constant}
$$
这就是我们要求的曲线方程。

# 摆线

这一曲线方程实际上就是**旋轮线**，即滚动的轮子边缘上的一点所描出的形状。

圆上定点为 P，圆与水平线的切点为 C，圆滚动时，点 C 充当点 P 的瞬时旋转中心：

![Instant Rotation Center](https://i.imgur.com/yMiZO8r.jpg)

所以 CP 垂直于摆线过点 P 的切线，又因直角圆周角对应直径，所以该切线一定过圆的最低点，交点与 C 的连线即为圆的直径：

![Tangent Crosses the Lowest Point on Circle](https://i.imgur.com/ZX63jAn.jpg)

设直线与切线的夹角为 $\theta$，根据相似三角形，我们可以算出点 P 到水平线的距离：

![Distance from P to C](https://i.imgur.com/5y8lXzp.jpg)

即
$$
\frac{\sin(\theta)}{\sqrt{y}} = \frac{1}{\sqrt{D}} = \text{constant}
$$
由此证明最速降线实际上就是摆线。

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
这样时间 $t$ 就被写成了关于 $y$ 的泛函，而求时间最短问题变成了在满足边界条件
$$
y(A) = 0, y(B) = y_B
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

1. [Video](https://www.bilibili.com/video/av6385842)
2. [Brachistochrone Curve](https://en.wikipedia.org/wiki/Brachistochrone_curve)
3. [变分法](http://blog.sciencenet.cn/blog-1319915-1013924.html)
