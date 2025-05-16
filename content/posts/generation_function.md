---
title: 生成函数与计数
date: 2025-01-25
categories:
- Fun
tags:
- Math
- 3Blue1Brown
---

**问题**：

对于集合 $\{1,2,\dots, 2000\}$，有多少个子集满足子集中所有数字的和可以被 $5$ 整除？

<!--more-->

---

# 递推法

首先对集合进行分组 $A_n = \{5n-4, \dots, 5n\}$。对于任意 $A_n$，其子集中所有数字的和对 $5$ 取余的结果与 $A_1$ 相同。可以得到结果为 $0,1,2,3,4$ 的个数分别为 $8,6,6,6,6$。注意空集也满足条件（认为空集的所有数字和为 $0$）。

记 $A_1\cup A_2\cup\cdots \cup A_n$ 为 $B_n$，记 $B_n$ 满足条件的子集个数为 $a_n$，则 $B_{n+1}$ 满足条件的子集为：

1. 对于数字和能被 $5$ 整除的 $B_n$ 子集，同样取数字和能被 $5$ 整除的 $A_{n+1}$ 子集配对，个数为 $8a_n$
2. 对于数字和对 $5$ 取余为 $1$ 的 $B_n$ 子集，取数字和对 $5$ 取余为 $4$ 的 $A_{n+1}$ 子集配对。对于其它余数情况同理，可以得到这样的子集个数为 $6(2^{5n} - a_n)$

综上有

$$
a_{n+1} = 8a_n + 6(2^{5n} - a_n)
$$

求通项公式 $a_n$ 并代入 $n=400$ 即可得到原问题答案：

$$
\frac 1 5 (2^{2000} + 4\times 2^{400})
$$

# 生成函数法

生成函数经常用于组合计数中。考虑多项式：

$$
G(x) = (1+x)(1+x^2)\cdots(1+x^{2000})
$$

将其展开的过程可以与构造子集的过程一一对应：每个元素都只有选与不选两种选择，展开过程选择 $x^k$ 则意味着集合选取了元素 $k$。同时由于乘法的特性 $x^m \cdot x^n = x^{m+n}$，展开式中各项的次数即等于对应子集的元素和，因此系数就对应着有多少这样的子集。

由于元素和是否能被 $5$ 整除只和模 $5$ 的余数有关，生成函数可以简化为

$$
\begin{equation}
\label{gen_fun}
G(x) = \left[(1+x)(1+x^2)\cdots(1+x^{5})\right]^{400}
\end{equation}
$$

所以原问题等价与求该展开式中所有 $x^{5k}, k\in \mathbb{N}$ 项的系数和。可以利用 $n$ 次单位根性质。

## 单位根

$z^n-1$ 在复平面上的单位根为：

$$
\omega^k = \exp\left(\frac{2\pi i k}{n}\right), \quad k=0,1,\dots,n-1
$$

令 $\omega = \exp(\frac{2\pi i}{n})$，则 $z^n - 1$ 的根可以写作 $1, \omega, \omega^2, \dots, \omega^{n-1}$。又由如下的关系式：

$$
\begin{gather}
z^n - 1 = (z-1)(z-\omega)(z-\omega^2)\dots(z-\omega^{n-1}) \label{factor} \\
z^n - 1 = (z-1)(1+z+z^2+\cdots+z^{n-1})
\end{gather}
$$

可以得到：

$$
1+z+z^2+\cdots+z^{n-1} = (z-\omega)(z-\omega^2)\dots(z-\omega^{n-1})
$$

上式代入任意 $\omega^k$ 可以得到：

$$
\begin{equation}
\label{circle}
1+\omega^k+\omega^{2 k}+\cdots+\omega^{(n-1) k}=\begin{cases}
0 & n \nmid k \\
n & n \mid k
\end{cases}
\end{equation}
$$

## 求解

根据 $\eqref{circle}$，可以令 $\omega^k$ 为 $z^5 -1$ 的单位根，则 $\eqref{gen_fun}$ 中所有 $x^{5k}, k\in \mathbb{N}$ 项的系数和可以由下式计算：

$$
\frac 1 5 [G(1) + G(\omega) + \cdots + G(\omega^4)]
$$

其中 $G(1) = 2^{2000}$。$G(\omega)$ 可以通过 $\eqref{factor}$ 中代入 $z=-1$ 进行计算，得到 $G(\omega) = 2^{400}$，同理可得其它项。最后答案为：

$$
\frac 1 5 (2^{2000} + 4\times 2^{400})
$$

# 参考

1. [3Blue1Brown Video](https://youtu.be/bOXCLR3Wric)
