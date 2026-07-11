---
title: 发明新数学是怎样一种体验
date: 2018-08-05
updated: 2026-05-25
categories:
- Fun
tags:
- Math
- 3Blue1Brown
---

**问题：**

如何使 $1+2+2^2+\cdots=-1$ 成立？本笔记旨在探讨如何从已知的数学体系中拓展出全新的数学分支。

<!--more-->

---

# 无限求和与几何直观

已知无限项求和公式：

$$
\sum_{n=1}^{\infty} \frac{1}{2^n} = 1
$$

其几何直观在于「无限逼近」。若将长度为 $1$ 的线段分为长度为 $1-p$ 与 $p$ 的两段，并对长度为 $p$ 的线段持续递归执行该切分操作，可得等比数列求和公式：

$$
\begin{align*}
&(1-p) + p(1-p) + p^2(1-p) + \cdots = 1 \\
\implies & \sum_{n=0}^\infty p^n = \frac{1}{1-p}
\end{align*}
$$

# 距离定义的重构

上述公式仅在 $0<p<1$ 时收敛。若试图将其推广至 $p = 2$ 的情形，必须重新定义**距离**，并要求新距离函数满足**平移不变性**：

$$
\operatorname{dist}(A, B)=\operatorname{dist}(A+x, B+x) \quad \forall x
$$

构建新距离函数的逻辑如下：

1. 初始假设：定义 $0$ 与 $2^n$ 的距离为 $2^{-n}$。
2. 平移推演：利用平移不变性，推导出其余数值间的距离。

在此度量体系下，当 $n$ 趋于无穷大时，距离收敛于 $0$：

$$
\lim_{n\to \infty} 2^n = 0
$$

原问题得证：

$$
1+2+2^2+\cdots = \lim_{n\to \infty} 2^n -1 = -1
$$

# 推广至 p-adic Number

构建完备的度量空间 (Metric Space) 仍需满足非负性、对称性及三角不等式。上述推演旨在展示如何通过拓展基础定义，在兼容现有数学框架的前提下推导出新颖结论，进而开创全新数学分支。

这正是 $p$ 进数在 $p=2$ 时的特例。其形式化定义如下：

Let $p$ be a prime in $\mathbb{Z}$.

- **$p$-adic order**: Defined for $\mathbb{Z}$ as $v_p: \mathbb{Z}\to\mathbb{N} \cup \{ \infty \}$, where:

    $$
    v_p(n) = \begin{cases}
    \max\{v\in \mathbb{N}: p^v \mid n\} & \text{if } n\ne 0 \\
    \infty & \text{if } n=0
    \end{cases}
    $$

- **$p$-adic absolute value**: Defined as $|\cdot|_p$, where:

    $$
    |x|_p=\begin{cases}
    p^{-v_p(x)} &\text{if } x\ne 0 \\
    0 &\text{if } x=0
    \end{cases}
    $$

- **Metric space**: A corresponding metric space can then be defined by the distance function:

    $$
    d(x,y) = |x-y|_p
    $$

# 参考

1. [3Blue1Brown Video](https://youtu.be/XFDM1ip5HdU)
