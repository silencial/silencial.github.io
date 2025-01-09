---
title: 隐藏在素数规律中的 $\pi$
date: 2018-08-08
categories:
- Fun
tags:
- Math
- 3Blue1Brown
---

**问题：**

证明 $\dfrac{\pi}{4} = 1-\dfrac{1}{3}+\dfrac{1}{5}-\dfrac{1}{7}+\dfrac{1}{9}-\cdots$

<!-- more -->

---

使用简单的微积分可以得到：
$$
\begin{split}
1-\dfrac{1}{3}+\dfrac{1}{5}-\dfrac{1}{7}+\cdots &= \int_0^1 (1-x^2+x^4-\cdots)dx \\
&= \int_0^1 \frac{1}{1+x^2}dx \\
&= \tan^{-1}(1) \\
&= \frac{\pi}{4}
\end{split}
$$
我们选择从另外一种方式来得出这个等式，从圆的定义开始。

# 计算圆内格点数目

如果将二维平面划分为网格，并画出半径为 $r$ 的圆，圆内包含的格点数应该大约等于 $\pi r^2$，并且当 $r\to+\infty$ 时，两者应该无限接近，这即给出了一个计算 $\pi$ 的方法。

考虑经过格点的圆，由于格点的坐标都为整数，因此我们只需要考虑那些半径是整数平方根的圆即可。

![Lattices inside Circle](https://i.imgur.com/LawE19V.png)

考虑半径为 $R$ 的圆，如果能找到整数 $a,b$ 使得 $R^2 = a^2+b^2$，则该圆经过点 $(a,b)$，将其拓展至复平面：
$$
\begin{equation}
\label{gaussian}
R^2 = a^2+b^2 = (a+bi)(a-bi)
\end{equation}
$$
 形如 $a+bi \ \ (a,b\in \mathbb{N})$ 的数被称为**高斯整数**，因此寻找圆经过的格点等价于寻找所有满足 $\eqref{gaussian}$ 的高斯整数。

# 在高斯整数上分解质因数

下一步我们需要了解一个数如何在高斯整数上分解。

我们已经知道每个正整数都可以以唯一的方式进行素数分解，因此我们只需要考虑素数在高斯整数上的分解问题。

例如 $5$ 可以被分解为 $(2+i)(2-i)$，而 $(2+i)$ 无法在高斯整数上继续分解，这样的数被称为**高斯素数**。除了让因子乘以 $-1,i,-i$ 之外，素数在高斯整数上的分解是唯一的。

由**费马平方和定理**可知，除了 $2$ 之外，只有形如 $p=4n+1\ \ (n\in N)$ 的素数可以在高斯整数中分解。

因此将半径的平方进行高斯素数全分解后，计算有多少种分配方式能将其因子分为互为复共轭的两对，即是计算该圆经过了多少个格点。

计算半径为 $R$ 的圆上的格点数：

1. 将 $R^2$ 进行质因数分解：$R^2=3^4\cdot5^3\cdot13^2$
2. 将形如 $p=4n+1$ 的素数继续在高斯整数上分解：$5,13$
3. 将最后的因子分为两列，使其互为复共轭，由于 $p=4n+1$ 的素数可以分解为一对共轭的高斯素数，它们所提供的分配方式总是它们的指数加 $1$。而对于 $p=4n+3$ 的素数而言，只有当它们的指数为偶数时才能刚好进行分配。因此对于本例，分配方式共有 $1 \times (3+1) \times (2+1)$ 种。
4. 由于因子可以乘以 $-1,i.-i$，分配方式需要再乘以 $4$。
5. 特殊地 $2=(1+i)(1-i)$，但 $i(1+i)=(1-i)$ 已经被上一步包括，因此 $2$ 不影响总分配形式。

# 定义函数 $\chi$

$$
\chi(n) = \begin{cases}
1 &n=4k+1 \\
-1 &n=4k+3 \quad (k\in \mathbb{N}) \\
0 &n=2k
\end{cases}
$$

注意 $\chi(n)$ 有可积性：$\chi(ab) = \chi(a)\cdot\chi(b)$。

有了这一函数，我们可以将 $a^b$ 对应的分配方式写为 $\chi(1)+\chi(a)+\chi(a^2)+\dots+\chi(a^b)$。

因此将半径的平方进行素数分解后再用 $\chi$ 函数进行替代，并利用可积性合并，记半径为 $R$ 的圆经过的格点数记为 $\#\text{LP}_R$：
$$
\begin{align*}
R^2&=a^b\cdot c^d \\
\#\text{LP}_R&=4[\chi(1)+\chi(a)+\cdots+\chi(a^b)][\chi(1)+\chi(c)+\cdots+\chi(c^d)] \\
&= 4[\chi(1)+\chi(a_1)+\cdots+\chi(a_n)]
\end{align*}
$$
其中 $1, a_1, \cdots, a_n$ 是 $R^2$ 的所有因子。

# 证明

有了上述结论，我们就可以计算半径为 $R$ 的圆内的格点数：
$$
\sum_{r=1}^{R} \#\text{LP}_r
$$
当 $R\to+\infty$ 时，在 $1$ 到 $R^2$ 的所有整数中，有因子 $k$ 的概率为 $\dfrac{1}{k}$。因此：
$$
\begin{align*}
\sum_{r=1}^{R} \#\text{LP}_r &= 4R^2\left[\chi(1)+\dfrac{\chi(2)}{2}+\dfrac{\chi(3)}{3}+\cdots\right] \\
&= \pi R^2
\end{align*}
$$
所以：
$$
\begin{align*}
\dfrac{\pi}{4} &= \chi(1)+\dfrac{\chi(2)}{2}+\dfrac{\chi(3)}{3}+\cdots \\
&= 1-\dfrac{1}{3}+\dfrac{1}{5}-\dfrac{1}{7}+\dfrac{1}{9}-\cdots
\end{align*}
$$
得证。

# 参考

1. [3Blue1Brown Video](https://youtu.be/NaL_Cb42WyY)
2. [Fermat's Theorem on Sums of Two Squares — Wiki](https://en.wikipedia.org/wiki/Proofs_of_Fermat%27s_theorem_on_sums_of_two_squares)
3. [Gaussian Integer — Wiki](https://en.wikipedia.org/wiki/Gaussian_integer#Gaussian_primes)
