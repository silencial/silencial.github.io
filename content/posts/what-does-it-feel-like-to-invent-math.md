---
title: 发明新数学是怎样一种体验
date: 2018-08-05
categories:
- Fun
tags:
- Math
- 3Blue1Brown
---

**问题：**

如何使 $1+2+4+\cdots+2^n+\cdots=-1$ ？
<!-- more -->

---

全新定义距离函数，同时保持这一函数的平移不变性：
$$
\operatorname{dist}(A, B)=\operatorname{dist}(A+x, B+x) \quad \forall x
$$
首先假设 $0$ 与 $2^n$ 距离为 $2^{-n}$，之后通过平移不变性确定其它数的距离。

由此可知
$$
\lim_{n\to \infty} 2^n = 0
$$
所以原问题
$$
1+2+4+\cdots+2^n+\cdots = \lim_{n\to \infty} 2^n -1 = -1
$$
实际上这是 **$p$-adic number** 在 p=2 下的结论。

给出正式定义：

let $p$ be a prime in $\mathbb{Z}$. The **$p$-adic order** for $\mathbb{Z}$ is defined as $v_p: \mathbb{Z}\to\mathbb{N}$
$$
v_p(n) = \begin{cases}
\max\{v\in \mathbb{N}: p^v | n\} & \text{if } n\ne 0 \\
\infty & \text{if } n=0
\end{cases}
$$
The **$p$-adic absolute value** is defined as $|\cdot|_p$
$$
|x|_p=\begin{cases}
p^{-v_p(x)} &\text{if } x\ne 0 \\
0 &\text{if } x=0
\end{cases}
$$
A metric space then can be defined by
$$
d(x,y) = |x-y|_p
$$

# 参考

1. [Video](https://www.bilibili.com/video/av3509556)
2. [p-adic Order](https://en.wikipedia.org/wiki/P-adic_order#p-adic_Norm)
3. [p-adic Number](https://en.wikipedia.org/wiki/P-adic_number)
