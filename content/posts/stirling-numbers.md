---
title: Stirling Numbers
date: 2018-11-29
categories:
- Fun
tags:
- Math
- Discrete Math
---

Various ways of distributing balls into cells.

<!--more-->

---

# Occupancy Problems

A classical set of problems in combinatorics is the number of distribution of $n$ balls to $k$ cells. The solution depends on whether the $n$ balls and/or the $k$ cells are distinguishable.

Suppose first that both the balls and the cells are distinguishable. Then the collection of balls and cells can be interpreted as $n$-element set $N$ and $k$-element set $K$, separately. A distribution then corresponds to a function $f: N\to K$. The number of distribution is then $k^n$.

If the balls are identical, but the cells are distinguishable, the number of distributions is $C(k+n-1,n)$.

This problem can have additional restriction on the number of balls that can be placed in each cell. If both the balls and cells are distinguishable. Suppose that at most one ball can be placed in each cell, we would then be seeking the number of **one-to-one** functions $f: N\to K$, which is $P(k,n)$. If instead there must be at least one ball in each cell, we want the number of **onto** functions $f: N\to K$. Using inclusion-exclusion
$$
\begin{multline*}
N(P_1'P_2'\cdots P_n')=N-\sum_{1\le i\le n}N(P_i)+\sum_{1\le i\le j\le n}N(P_iP_j) \\
-\sum_{1\le i\le j\le k\le n}N(P_iP_jP_k)+\dots+(-1)^nN(P_1P_2\cdots P_n)
\end{multline*}
$$
the number is equal to
$$
\sum_{i=0}^{k-1}(-1)^iC(k,i)(k-i)^n
$$

# Partitions and Stirling Numbers of the Second Kind

The Stirling number of the second kind, $S(n,k)$, is the number of partitions of an $n$-element set into $k$ classes.

In occupancy problems, a partition corresponds to a distribution in which the balls are distinguishable, the cells are identical and there is at least one ball in each cell. The number of distribution of $n$ balls into $k$ cells is therefore $S(n,k)$. If we remove the restriction that there must be at least one ball in each cell, then a distribution corresponds to a partition of the set $N$ with at most $k$ classes
$$
S(n,1)+S(n,2)+\dots+S(n,k)
$$
We can compute the Stirling numbers recursively using the following theorem
$$
S(n+1,k)=S(n,k-1)+kS(n,k)
$$

The $k^n$ distributions of $n$ distinguishable balls into $k$ distinguishable cells can be computed by another way with Stirling numbers. First partition $n$ balls into $j$ sets and then find the one-to-one function from $j$ sets to $k$ cells
$$
k^n=\sum_{j=1}^kS(n,j)P(k,j)
$$

The number of functions from a set with n elements onto a set with k elements is S(n,k)k!.
$$
S(n,k)=\dfrac{1}{k!}\sum_{i=0}^k(-1)^iC(k,i)(k-i)^n
$$

# Stirling Numbers and Polynomials

Extend Stirling numbers from positive integers to real numbers
$$
x^n=\sum_{k=1}^nS(n,k)(x)_k
$$
where $(x)_n=1$ when $n=0$ and $(x)_n=x(x-1)\cdots(x-n+1)$ when $n\ge 1$. Hence this equation has at most $n$ degrees and at most $n$ roots. Since this equality holds for every positive integer $x$, it must holds for every real number $x$.

# Stirling Numbers of the First Kind

From the view of linear algebra, the set of polynomials with real coefficients forms a **vector space**. Two important bases are the **standard basis** $\{x^k|k=0,1,\cdots\}$ and the **falling factorial basis** $\{(x)_k|k=0,1,\cdots\}$. Stirling numbers of the second kind $S(n,k)$ is in fact the coefficients changing from the falling factorial basis $\{(x)_k\}$ to the standard basis $\{x^k\}$.

The Stirling numbers of the first kind $s(n,k)$ are the numbers satisfying
$$
(x)_n=\sum_{k=0}^ns(n,k)x^k
$$
This equation uniquely determines the coefficients $s(n,k)$ since every polynomial can be uniquely expressed as a linear combination of the polynomials in a basis.

Also we have
$$
s(n+1,k)=s(n,k-1)-ns(n,k)
$$

# Reference

1. [Striling Number](https://en.wikipedia.org/wiki/Stirling_number)
