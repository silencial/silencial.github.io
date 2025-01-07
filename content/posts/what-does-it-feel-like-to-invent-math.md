---
title: 发明新数学是怎样一种体验
date: 2018-08-05
lastmod: 2025-01-05
categories:
- Fun
tags:
- Math
- 3Blue1Brown
---

**问题：**

如何使 $1+2+2^2+\cdots=-1$ ？从已知的数学中拓展至新数学。
<!-- more -->

---

对于一个无限项求和：
$$
\sum_{n=1}^{\infty} \frac{1}{2^n} = 1
$$
它的意义是：我们可以无限靠近 1。在几何上，如果将 0 至 1 的线段分成 $1-p$ 和 $p$ 的左右两断，然后对 $p$ 的线段继续进行此操作，可以得到：
$$
\begin{align*}
&(1-p) + p(1-p) + p^2(1-p) \cdots = 1 \\
\implies & \sum_{n=0}^\infty p^n = \frac{1}{1-p}
\end{align*}
$$
该式只在 $0<p<1$ 时成立，但如果我们想推广其使得 $p = 2$ 成立，则需要重新思考**距离**的定义。

全新定义距离函数，同时保持这一函数的平移不变性：
$$
\operatorname{dist}(A, B)=\operatorname{dist}(A+x, B+x) \quad \forall x
$$
首先假设 $0$ 与 $2^n$ 距离为 $2^{-n}$，之后通过平移不变性确定其它数的距离。由此可知在新的距离函数下：

$$
\lim_{n\to \infty} 2^n = 0
$$
原问题得证：
$$
1+2+2^2+\cdots = \lim_{n\to \infty} 2^n -1 = -1
$$
距离函数其实还需要满足一些其它的性质，比如非负性、对称性、三角不等式。这里只是为了展示如何拓展定义，使其能够兼容已经存在的数学形式，并且能够得到一些新的有趣的结论，从而发展成为一门新数学。



实际上这是 **$p$-adic number** 在 p=2 下的结论。给出正式定义：

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

1. [3Blue1Brown Video](https://youtu.be/XFDM1ip5HdU)
3. [p-adic Number](https://en.wikipedia.org/wiki/P-adic_number)
