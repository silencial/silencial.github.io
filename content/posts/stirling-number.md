---
title: Stirling Number
date: 2018-11-29
lastmod: 2025-06-23
categories:
- Fun
tags:
- Math
- Discrete Math
---

This note explores the combinatorial problem of distributing balls into boxes, which provides a natural introduction to Stirling numbers of the first and second kind.

<!--more-->

---

# The Occupancy Problem

A classical set of problems in combinatorics involves determining the number of ways to distribute $n$ balls into $k$ boxes. The solution depends on whether the balls and/or boxes are distinguishable.

1. Distinguishable balls, distinguishable boxes: For each ball, there are $k$ choices. So the number of distributions is $k^n$.
2. Indistinguishable balls, distinguishable boxes: This is a stars and bars problem. The number of distributions is $\binom{n+k-1}{k-1}$
3. Distinguishable balls, indistinguishable boxes: This case will be addressed by Stirling numbers of the second kind, $S(n,k)$.
4. Indistinguishable balls, indistinguishable boxes: This is the integer partition problem. Can be solved by dynamic programming: `dp(n, k) = dp(n-k, k) + dp(n, k-1)`.

The problem can also include the constraint of whether empty boxes are permitted. The table below summarizes the eight primary variations of this problem.

| Distinguishable Ball | Distinguishable Box | Empty Boxes Allowed? | Number of Distributions |
| :------------------: | :-----------------: | :------------------: | :---------------------: |
|          ✅           |          ✅          |          ✅           |          $k^n$          |
|          ✅           |          ✅          |          ❌           |       $k!S(n,k)$        |
|          ❌           |          ✅          |          ✅           |  $\binom{n+k-1}{k-1}$   |
|          ❌           |          ✅          |          ❌           |   $\binom{n-1}{k-1}$    |
|          ✅           |          ❌          |          ✅           | $\sum_{j=1}^{k}S(n,j)$  |
|          ✅           |          ❌          |          ❌           |        $S(n,k)$         |
|          ❌           |          ❌          |          ✅           |       `dp(n, m)`        |
|          ❌           |          ❌          |          ❌           |      `dp(n-m, m)`       |

# Stirling Numbers of the Second Kind

The **Stirling number of the second kind**, $S(n,k)$, is the number of ways to partition an $n$-element set into $k$ non-empty, indistinguishable subsets.

In the context of the occupancy problem, $S(n,k)$ counts the number of ways to distribute $n$ distinguishable balls into $k$ indistinguishable boxes such that no box is empty. If empty boxes are allowed, the total number of distributions is then the sum:

$$
\sum_{j=1}^{k} S(n,j)
$$

$S(n,k)$ can be computed recursively as follows:

$$
S(n+1,k)=S(n,k-1)+kS(n,k)
$$

The number of distributions of $n$ distinguishable balls into $k$ distinguishable boxes, $k^n$, can also be expressed using Stirling numbers. First, partition $n$ balls into $j$ non-empty subsets (in $S(n,j)$ ways). Then, map these $j$ subsets to $k$ distinct boxes (in $P(k, j)$ ways). Summing over all possible numbers of non-empty subsets $j$ gives the total:

$$
k^n=\sum_{j=1}^kS(n,j) P(k,j)
$$

From this, we can derive an explicit formula for $S(n,k)$. The number of surjective (onto) functions from an $n$-element set to a $k$-element set is exactly $k!S(n,k)$. By applying the principle of inclusion-exclusion to count these surjective functions, we have:

$$
S(n,k)=\dfrac{1}{k!}\sum_{i=0}^k(-1)^i\binom{k}{i}(k-i)^n
$$

# Stirling Numbers and Polynomials

The relationship between powers and falling factorials can be extended from integers to the domain of real or complex numbers. The identity

$$
x^n=\sum_{k=0}^nS(n,k)(x)_k
$$

where $(x)_k$ is the **falling factorial** defined as $(x)_k=x(x-1)\cdots(x-k+1)$ for $k \ge 1$ and $(x)_0=1$, equates two polynomials in the variable $x$. Since this equality holds for all positive integers $x$, and a non-zero polynomial of degree $n$ can have at most $n$ roots, the two polynomials must be identical. Therefore, the equality holds for all real or complex numbers $x$.

# Stirling Numbers of the First Kind

From a linear algebra perspective, the set of all polynomials with real coefficients forms a vector space. Two important bases for this space are the **standard basis** $\{x^k \mid k=0, 1, \dots\}$ and the **falling factorial basis** $\{(x)_k \mid k=0, 1, \dots\}$. The Stirling numbers of the second kind, $S(n,k)$, are the change-of-basis coefficients that express standard powers $x^n$ as a linear combination of falling factorials $(x)_k$.

The Stirling numbers of the first kind, $s(n,k)$, are defined as the coefficients for the reverse transformation, converting falling factorials into standard powers:

$$
(x)_n=\sum_{k=0}^ns(n,k)x^k
$$

This equation uniquely determines the coefficients $s(n,k)$, as any polynomial has a unique representation in a given basis. These numbers can be computed recursively:

$$
s(n+1,k)=s(n,k-1)-ns(n,k)
$$

The unsigned Stirling numbers of the first kind, $|s(n,k)|$, count the number of permutations of $n$ elements with exactly $k$ cycles.
