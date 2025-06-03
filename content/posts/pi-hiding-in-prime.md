---
title: 隐藏在素数规律中的 $\pi$
date: 2018-08-08
lastmod: 2025-05-22
categories:
- Fun
tags:
- Math
- 3Blue1Brown
---

**问题：**

证明 $\dfrac{\pi}{4} = 1-\dfrac{1}{3}+\dfrac{1}{5}-\dfrac{1}{7}+\dfrac{1}{9}-\cdots$

<!--more-->

---

可以通过微积分证明：

$$
\begin{split}
1-\dfrac{1}{3}+\dfrac{1}{5}-\dfrac{1}{7}+\cdots &= \int_0^1 (1-x^2+x^4-\cdots)dx \\
&= \int_0^1 \frac{1}{1+x^2}dx \\
&= \arctan(1) \\
&= \frac{\pi}{4}
\end{split}
$$

下面从圆的几何定义出发，给出另一种推导。

# 计算圆内格点数目

将平面划分为单位网格，并画出半径为 $r$ 的圆，圆内（含圆周）包含的格点数约等于面积 $\pi r^2$。当 $r\to +\infty$ 时，两者无限接近，这提供了一种计算 $\pi$ 的方法。

考虑那些恰好经过格点的圆。由于格点坐标均为整数，因此我们只需关注半径 $R$ 满足 $R^2$ 为整数的圆。

![Lattices inside Circle](https://i.imgur.com/LawE19V.png)

考虑半径为 $R$ 的圆。如果存在整数 $a,b$ 使得 $R^2 = a^2+b^2$，则该圆经过格点 $(a,b)$。为了方便分析，我们将此方程拓展至复平面：

$$
\begin{equation}
\label{gaussian}
R^2 = a^2+b^2 = (a+bi)(a-bi)
\end{equation}
$$

形如 $a+bi$ ($a,b\in \mathbb{Z}$) 的数被称为高斯整数，记为 $\mathbb{Z}[i]$。因此，寻找圆经过的格点等价于寻找所有满足 $\eqref{gaussian}$ 的高斯整数。

# 高斯整数分解

下一步，我们需要了解整数在高斯整数环上的分解方式。

我们知道每个正整数都具有唯一的质因数分解。因此，我们只需考虑质数在高斯整数环上的分解情况。

例如，$5$ 可以分解为 $(2+i)(2-i)$。而 $(2+i)$ 无法在高斯整数上继续分解，这样的数被称为高斯质数。在高斯整数环内，质数的分解是唯一的，仅相差乘以一个单位因子（即乘以 $\pm 1, \pm i$）。

因此，将 $R^2$ 进行高斯质数分解后，通过计算其因子能够以多少种不同方式组成共轭高斯整数对，即可确定该圆周上的格点数目。

费马平方和定理指出，除 $2$ 外，仅当质数满足 $p\equiv 1 \pmod 4$ 时才可在 $\mathbb Z[i]$ 中分解；若 $p\equiv3\pmod4$，则在该环中仍为质数。

设 $R^2 = \prod p_i^{k_i}$ 是 $R^2$ 在 $\mathbb{Z}$ 中的质因数分解，圆周上的格点数 $N(R)$ 可由下列规则计算：

1. 若 $p_i\equiv1\pmod4$，则其贡献为 $k_i+1$；
2. 若 $p_i\equiv3\pmod4$，则当 $k_i$ 为偶数时贡献为 $1$，奇数时 $N(R)=0$；
3. 质因子 $2$ 的贡献恒为 $1$。
4. 最终 $N(R)$ 等于 4 乘以上述各贡献之积。

例如，若 $R^2=3^4 \cdot 5^3 \cdot 13^2$：

$$
N(R)=4\times1\times(3+1)\times(2+1)=48.
$$

# Dirichlet 特征 $\chi$

定义

$$
\chi(n) = \begin{cases}
1 & \text{if } n \equiv 1 \pmod{4} \\
-1 & \text{if } n \equiv 3 \pmod{4} \\
0 & \text{if } n \equiv 0 \pmod{2}
\end{cases}
$$

注意 $\chi(n)$ 是完全积性函数：$\chi(ab) = \chi(a)\cdot\chi(b)$。

利用此函数，$p_{i}$ 相关的贡献可以表示为 $\sum_{j=0}^{k_{i}} \chi(p_{i}^j)$。则 $N(R)$ 可以表示为：

$$
\begin{equation*}
\begin{split}
N(R)&=4\sum_{j=0}^{k_{1}} \chi(p_{1}^j) \sum_{j=0}^{k_{2}} \chi(p_{2}^j) \cdots \\
&= 4 \sum_{d\mid R^{2}} \chi(d)
\end{split}
\end{equation*}
$$

其中 $d$ 是 $R^2$ 的所有正因子。

# 最终证明

利用上述结论，我们可以计算半径为 $R$ 的圆内（含圆周）的格点数：

$$
\sum_{r=1}^{R} N(r)
$$

当 $R\to+\infty$ 时，在 $1$ 到 $R^2$ 的所有整数中，有因子 $k$ 的概率为 $1/k$。因此：

$$
\sum_{r=1}^{R} N(r) = 4R^2\left[\chi(1)+\dfrac{\chi(2)}{2}+\dfrac{\chi(3)}{3}+\cdots\right]
$$

同时圆内格点数为 $\pi R^2$，两式联立可得：

$$
\begin{equation*}
\begin{split}
\dfrac{\pi}{4} &= \chi(1)+\dfrac{\chi(2)}{2}+\dfrac{\chi(3)}{3}+\cdots \\
&= 1-\dfrac{1}{3}+\dfrac{1}{5}-\dfrac{1}{7}+\dfrac{1}{9}-\cdots
\end{split}
\end{equation*}
$$

得证。

# 参考

1. [3Blue1Brown Video](https://youtu.be/NaL_Cb42WyY)
