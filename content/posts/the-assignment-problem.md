---
title: The Assignment Problem
date: 2018-12-16
categories:
- Fun
tags:
- Math
- Discrete Math
---

Given $n$ workers and $n$ jobs, and the cost $c_{ij}$ to train the $i$-th worker for the $j$-th job, find an assignment of one worker to each job which minimized the total training cost.

<!--more-->

---

# Solving Assignment Problems

Recall that a permutation of a set $N=\{1,2,\dots,n\}$ is a function $\sigma:N\to N$ which is one-to-one and onto. Given any $n\times n$ matrix $C=[c_{ij}]$, the assignment problem specified by $C$ is the problem of finding a permutation $\sigma$ of $\{1,2,\dots,n\}$ that minimizes
$$
z=\sum_{i=1}^n c_{i\sigma(i)}
$$
One method for solving assignment problems is to generate all $n!$ permutations of $\{1,2,\dots,n\}$, compute every $z$ for each permutation $\sigma$, and then find a permutation on which the minimum of $z$ is attained.

We want to develop a more efficient method. Let $C=[c_{ij}]$ be any $n\times n$ matrix in which $c_{ij}$ is the cost of assigning worker $i$ to job $j$. Let $X=[x_{ij}]$ be the $n\times n$ matrix where
$$
x_{ij}=\begin{cases}
1 &\text{if row } i \text{ assigned to column } j \\
0 &\text{otherwise}
\end{cases}
$$
The assignment problem can then be expressed in terms of a function $z$ as:
$$
\begin{align*}
&\min & &z(X) = \sum_{i=1}^n\sum_{j=1}^n c_{ij}x_{ij} \\
&\text{ s.t.} & &\sum_{j=1}^n x_{ij}=1, \quad \text{for } i=1,2,\dots,n \\
& & &\sum_{i=1}^n x_{ij}=1, \quad \text{for } j=1,2,\dots,n
\end{align*}
$$
Any solution $X$ on which $z(X)$ is minimum is called an **optimal solution**.

This model allows for the derivation of an efficient algorithm known as the **Hungarian method**. The idea behind the Hungarian method is to try to transform a given assignment problem specified by $C$ into another one specified by a matrix $\hat{C}=[\hat{c}_{ij}]$, such that $\hat{c}_{ij}\ge 0$, for all pairs $i,j$, where both problems have the same set of optimal solutions.

**Theorem 1**  A solution $X$ is an optimal solution for
$$
z(X)=\sum_{i=1}^n\sum_{j=1}^n c_{ij}x_{ij}
$$
if and only if it is an optimal solution for
$$
\hat{z}(X)=\sum_{i=1}^n\sum_{j=1}^n \hat{c}_{ij}x_{ij}
$$
where $\hat{c}_{ij}=c_{ij}-u_i-v_j$ for any choice of $(u_1,\dots,u_n)$ and $(v_1,\dots,v_n)$ where $u_i$ and $v_j$ are real numbers for all $i$ and $j$.

**Proof:**
$$
\begin{split}
\hat{z}(x)&=\sum_{i=1}^n\sum_{j=1}^n \hat{c}_{ij}x_{ij} \\
&=\sum_{i=1}^n\sum_{j=1}^n (c_{ij}-u_i-v_j)x_{ij} \\
&=z(x)-\sum_{i=1}^n u_i\sum_{j=1}^n x_{ij}-\sum_{j=1}^n v_j\sum_{i=1}^n x_{ij} \\
&=z(x)-\sum_{i=1}^n u_i-\sum_{j=1}^n v_j
\end{split}
$$

**Theorem 2**  If $C=[c_{ij}]$ satisfies $c_{ij}\ge 0$ for all $i$ and $j$, and $\{c_{1j_1},c_{2j_2},\dots,c_{nj_n}\}$ is an independent set of $n$ zeros in $C$, then $X^*=[x_{ij}^*]$ where $x_{1j_1}^*=1,x_{2j_2}^*=1,\dots,x_{nj_n}^*=1$, and $x_{ij}^*=0$ for any other $i$ and $j$, is an optimal solution to the assignment problem specified by $C$.

# The Hungarian Method

The objective of the Hungarian method is to use Theorem 1 to transform a matrix $C$ into another matrix $\hat{C}$, having the same set of optimal solutions as $C$, such that $\hat{C}$ contains an independent set of $n$ zeros. Then, using Theorem 2, an optimal solution to both problems can be obtained.

Given any $n\times n$ matrix $C=[c_{ij}]$, let
$$
\begin{align*}
&u_i=\min(c_{i1},c_{i2},\dots,c_{in}), & &\text{for }i=1,2,\dots,n \\
&v_j=\min(c_{1j}-u_1,c_{2j}-u_2,\dots,c_{nj}-u_n), & &\text{for }j=1,2,\dots,n
\end{align*}
$$
The matrix $\hat{C}=[\hat{c}_{ij}]$ given by $\hat{c}_{ij}=c_{ij}-u_i-v_j$ for all pairs $i$ and $j$ is called the reduced matrix for $C$.

The reduced matrix have the same set of optimal solutions as the original matrix. All entries in the reduced matrix are nonnegative. However, the reduced matrix may not contain an independent set of $n$ zeros. To obtain a new matrix having the same optimal solutions, but containing more zeros, draw a set of lines through the rows and columns of $\hat{C}$ using as few lines as possible so that there is at least one line through every zero. We can easily see that the minimum number of lines needed to cover all zeros is equal to the maximum number of independent zeros.

We now give the Hungarian method:

1. Obtain the reduce matrix $\hat{C}$ for $C$.
2. Find a maximal independent set of zeros. If this set has $n$ elements, an optimal solution is available. Otherwise go to step 3.
3. Find a set of lines that cover all zeros using the smallest possible number of lines. Let $k$ be the smallest entry not covered. Subtract $k$ from each entry not covered by any line, and add $k$ to each entry covered twice. Repeat step 2.

The number of iteration is $O(n^2)$.

# Perfect Matching Problems

Suppose four workers must be assigned to four jobs. Is it possible to assign workers to jobs so that each worker is assigned one job and each job is assigned to one worker? This problem can be stated in terms of graphs.

Let $G =(V,E) $be any bipartite graph with $V = V_1 \cup V_2$.A subset of edges $M$ contained in $E$ is called a perfect matching if every vertex in $V$ is contained in exactly one edge of $M$.

The **Frobenius Marriage Theorem** states that a bipartite graph $G=(V,E)$ with $V=V_1\cup V_2$ has a perfect matching if and only if $|V_1|=|V_2|$ and for every subset $W$ contained in $V_1$, $|R(W)|\ge |W|$, where $R(W)$ denote the set of vertices in $V_2$ adjacent to a vertex in $W$.

**Proof:** First we can give the definition of **vertex cover**. A vertex cover $Q$ of the edges of a graph $G$ is a set of vertices such that each edge of $G$ contains at least one vertex in $Q$. We can easily see that in a bipartite graph $G=(V,E)$ the maximum number of edges in a matching is equal to the minimum number of vertices in a vertex cover. Let $V=V_1\cup V_2$. If $G$ has a perfect matching, then for any $W$ contains in $V_1$ with $k$ elements, there must be $k$ elements in $V_2$ that are contained in $R(W)$, so $|R(W)|\ge |W|$. Conversely, assume $|V_1=|V_2|$ and $|R(W)|\ge |W|$, for all $W$ contained in $V_1$. Since $V_1$ is a vertex cover, the proof is complete if we show that $V_1$ is a cover of minimal size. Suppose $Q$ is a vertex cover of the edges of minimum size. Let $U_1=Q\cap V_1$ and $U_2=V_2-U_1$. By assumption, $|R(U_2)|\ge |U_2|$. However, $R(U_2)$ is contained in $Q\cap V_2$ because edges not covered by vertices in $V_1$ must be covered by vertices in $V_2$. Thus, $|U_2|\le|R(U_2)|\le|Q\cap V_2|$. This implies that $|V_1|=|U_1|+|U_2|\le|U_1|+|Q\cap V_2|=|Q\cap V_1|+|Q\cap V_2|=|Q|$. So that $V_1$ must be a cover of minimum size.

# Reference

1. [Assignment Problem](https://en.wikipedia.org/wiki/Assignment_problem)
2. [Hungarian algorithm](https://en.wikipedia.org/wiki/Hungarian_algorithm)
3. [Matching](https://en.wikipedia.org/wiki/Matching_(graph_theory))
