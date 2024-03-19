---
title: 一个计数谜题的意外答案
date: 2021-01-15
categories:
- Fun
tags:
- Math
- 3Blue1Brown
---

**问题：**

想象光滑的地面有两个静止的小滑块，左面有一堵质量无穷大的墙。左边的小滑块质量为 $1$。此时给右边的小滑块一个向左的速度，假设所有的碰撞都为完全弹性碰撞，请问总共的碰撞次数？（滑块之间的碰撞+左滑块和墙的碰撞）

![Collision](https://i.imgur.com/43fJNPK.png)

当右滑块的质量为 $100^k$ 时，总共发生碰撞的次数为 $\lfloor 10^k \pi \rfloor$

| 右滑块质量 | 碰撞次数 |
| :--------: | :------: |
|     1      |    3     |
|    100     |    31    |
|   10000    |   314    |
|  1000000   |   3141   |


# 解法 1

考虑能量守恒和动量守恒方程
$$
\begin{gather*}
\frac{1}{2} m_1 v_1^2 + \frac{1}{2} m_2 v_2^2 = \text{const} \\
m_1 v_1 + m_2 v_2 = \text{const}
\end{gather*}
$$
令 $x = \sqrt{m_1} v_1, y = \sqrt{m_2} v_2$, 则方程可以被改写为
$$
\begin{gather*}
x^2 + y^2 = \text{const} \\
\sqrt{m_1} x + \sqrt{m_2} y = \text{const}
\end{gather*}
$$
分别对应平面上的圆和直线。在此相空间上考虑碰撞问题。

初始状态对应圆的左端点位置，当第一次碰撞发生时，状态转移至直线和圆的交点：

![First Collision](https://i.imgur.com/CfTvRae.png)

当第二次碰撞发生时，即左滑块与墙碰撞时，速度大小不变，方向取反，在相空间中表现为与 $x$ 轴对称：

![Second Collision](https://i.imgur.com/40TRGu1.png)

重复此过程，直到 $v_1 > 0$ 且 $v_1 \le v_2$ 时碰撞停止，对应与相空间中黄色三角形内：

![Whole Collisions](https://i.imgur.com/fSuobmK.png)

可以看出，碰撞次数等于在相空间内的跳跃次数。而任意两次连续的跳跃间（与墙的碰撞和滑块间的碰撞）在相空间中所形成的圆周角均相同，设为 $\theta$，则对应的弧长为 $2\theta$。则使得 $N\cdot 2\theta < 2\pi$ 的最大整数 $N$ 即为碰撞次数。

![Phase Space](https://i.imgur.com/l8KPfHr.png)

由动量方程可知
$$
\theta = \arctan\left(-\sqrt{\frac{m_1}{m_2}}\right) + \frac{\pi}{2} = \arctan\left(\sqrt{\frac{m_2}{m_1}}\right)
$$
当 $x \to 0$ 时，$\arctan(x) \sim x$，因此当 $m_1 \gg m_2$ 时
$$
N = \left\lfloor \frac{\pi}{\theta} \right\rfloor = \left\lfloor \pi\sqrt{\frac{ m_1}{m_2}}\right\rfloor
$$
可以看出当 $m_1/m_2 = 100^k$ 时，总共发生碰撞的次数为 $10^k \pi$

# 解法 2

使用另外一种相空间类比镜面反射来解决这一问题。首先令 $x = d_1$ 表示右滑块左端点到墙壁的距离，$y = d_2$ 表示左滑块右端点到墙壁的距离，则两滑块相撞时有 $x=y$。考虑相空间中的整个过程，首先点沿着 $-x$ 轴前进（右滑块自由向左移动）；与 $x=y$ 直线相交后改变前进方向（滑块碰撞）；与直线 $y=w$ 相交后再次改变行进方向（与墙壁碰撞）。

![Phase Space](https://i.imgur.com/SeCuJrc.gif)

这个过程类似于光线在两面镜子之间进行反射，但入射角和反射角并不相同，因此我们进行坐标变换：
$$
x = \sqrt{m_1} d_1, \qquad y = \sqrt{m_2}d_2
$$
在该坐标下，能量守恒表现为光线的速度保持恒定：$(dx/dt)^2 + (dy/dt)^2 = const$；动量守恒表现为入射角等于反射角。为了得到后一个结论，将动量方程改写为
$$
\begin{bmatrix}
\sqrt{m_1} \\ \sqrt{m_2}
\end{bmatrix} \cdot
\begin{bmatrix}
dx/dt \\ dy/dt
\end{bmatrix} = \text{const}
$$
第一个向量即为 $d_1 = d_2$ 表示的镜子，由于第二个向量大小保持不变，因此在反射时这两个向量的角度保持不变，所以入射角等于反射角。在与墙壁碰撞时，$dx/dt$ 不变，$dy/dt$ 取反，因此也满足反射定律。

![Mirror Reflection](https://i.imgur.com/oSb6TT2.png)

至此，我们将原问题转化为了：给定两面夹角为 $\theta$ 的镜子，当一束与其中一面镜子平行的光射入时，会发生多少次反射？

当反射时，等价地，可以假设光线依旧按照直线传播，而将其它物体关于镜面对称过去

![Equivalent Reflection](https://i.imgur.com/FuJqGbC.png)

此时碰撞次数即等于光线穿过的镜子数量，即 $\lfloor \pi/\theta \rfloor$，而 $\theta$ 的值和等价计算与解法 1 相同，证明完毕。

# 参考

1. [Video of Proof 1](https://www.bilibili.com/video/av41712219)
2. [Video of Proof 2](https://www.bilibili.com/video/BV1Mb41187jL)
