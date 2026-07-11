---
title: The Assignment Problem
date: 2018-12-16
updated: 2026-05-27
categories:
- Fun
tags:
- Math
- Discrete Math
---

**Problem statement**: Given $n$ workers and $n$ jobs, along with the cost $c_{ij}$ to train the $i$-th worker for the $j$-th job, find an assignment of exactly one worker to each job which **minimizes** the total training cost.

<!--more-->

---

# Mathematical Formulation

Recall that a permutation of a set $N=\{1,2,\dots,n\}$ is a one-to-one and onto function $\sigma:N\to N$. Given any $n\times n$ cost matrix $C=[c_{ij}]$, the assignment problem specifies finding a permutation $\sigma$ of $\{1,2,\dots,n\}$ that minimizes the total cost:

$$
z=\sum_{i=1}^n c_{i\sigma(i)}
$$

**Brute-Force Approach**: Generate all $n!$ permutations of $\{1,2,\dots,n\}$, compute $z$ for each permutation $\sigma$, and find the minimum.

**Linear Programming Formulation**: Let $X=[x_{ij}]$ be an $n\times n$ binary decision matrix where:

$$
x_{ij}=\begin{cases}
1 &\text{if worker } i \text{ is assigned to job } j \\
0 &\text{otherwise}
\end{cases}
$$

The problem can be expressed as:

$$
\begin{align*}
&\min & &z(X) = \sum_{i=1}^n\sum_{j=1}^n c_{ij}x_{ij} \\
&\text{ s.t.} & &\sum_{j=1}^n x_{ij}=1, \quad \text{for } i=1,2,\dots,n \\
& & &\sum_{i=1}^n x_{ij}=1, \quad \text{for } j=1,2,\dots,n
\end{align*}
$$

Any solution $X$ that minimizes $z(X)$ is called an optimal solution.

This model allows for the derivation of an efficient algorithm known as the **Hungarian method**. The core idea is to transform a given cost matrix $C$ into a reduced matrix $\hat{C}=[\hat{c}_{ij}]$ (where $\hat{c}_{ij} \ge 0$) such that both matrices share the same set of optimal solutions.

**Theorem 1 (Equivalent Objectives)**: A solution $X$ is an optimal solution for $z(X) = \sum_{i, j} c_{ij}x_{ij}$, if and only if it is an optimal solution for $\hat{z}(X)=\sum_{i, j} \hat{c}_{ij}x_{ij}$, where $\hat{c}_{ij}=c_{ij}-u_i-v_j$ for any real numbers $u_i$ and $v_j$.

**Proof**:

$$
\begin{split}
\hat{z}(X)&=\sum_{i=1}^n\sum_{j=1}^n \hat{c}_{ij}x_{ij} \\
&=\sum_{i=1}^n\sum_{j=1}^n (c_{ij}-u_i-v_j)x_{ij} \\
&=z(X)-\sum_{i=1}^n u_i \left(\sum_{j=1}^n x_{ij}\right) - \sum_{j=1}^n v_j \left(\sum_{i=1}^n x_{ij}\right) \\
&=z(X)-\sum_{i=1}^n u_i-\sum_{j=1}^n v_j
\end{split}
$$

Since $\sum u_i$ and $\sum v_j$ are constants for a given valid assignment $X$, minimizing $\hat{z}(X)$ is strictly equivalent to minimizing $z(X)$.

**Theorem 2 (Zero-Cost Assignment)**: If $C=[c_{ij}]$ satisfies $c_{ij}\ge 0$ for all $i, j$, and there exists an independent set of $n$ zeros $\{c_{1j_1},c_{2j_2},\dots,c_{nj_n}\}$ in $C$, then the assignment $X^*$ (where $x_{ij}^*=1$ if $j=j_{i}$, and $0$ otherwise) is an optimal solution.

# The Hungarian Method

The objective is to utilize Theorem 1 to transform matrix $C$ into a reduced matrix $\hat{C}$ containing an independent set of $n$ zeros, and then apply Theorem 2 to locate the optimal assignment.

Given any $n\times n$ matrix $C=[c_{ij}]$

1. Row reduction: Find $u_i = \min(c_{i1}, \dots, c_{in})$ for each row $i$.
2. Column reduction: Find $v_j = \min(c_{1j}-u_1, \dots, c_{nj}-u_n)$ for each column $j$.
3. The matrix $\hat{C}=[\hat{c}_{ij}]$ given by $\hat{c}_{ij}=c_{ij}-u_i-v_j$ is the **reduced matrix**. All entries are nonnegative.

Algorithm steps:

1. Obtain the reduce matrix $\hat{C}$ for $C$.
2. Check for optimality: Find a maximal independent set of zeros.
    - If this set has $n$ elements, an optimal solution is available.
    - Otherwise, proceed to Step 3.
3. Line covering & Matrix update:
    - Draw the minimum number of lines through the rows and columns to cover all zeros.
    - Let $k$ be the smallest entry **not covered** by any line.
    - **Subtract** $k$ from each entry not covered by any line.
    - **Add** $k$ to each entry covered twice.
    - Repeat Step 2.

> Complexity: The number of iteration for matrix update is bounded by $O(n^2)$, leading to an overall standard time complexity of $O(n^3)$ for the Hungarian method.

# The Perfect Matching Problem

**Motivation**: Suppose 4 workers must be assigned to 4 jobs. Is it possible to assign them such that each worker gets exactly one job, and each job is assigned to exactly one worker? This translates directly to a graph theory problem.

- **Bipartite Graph**: A graph $G=(V,E)$ where the vertex set is partitioned into two disjoint subsets $V = V_1 \cup V_2$.
- **Perfect Matching**: A subset of edges $M \subseteq E$ where every vertex in $V$ is contained in exactly one edge of $M$.
- **Vertex Cover**: A set of vertices $Q$ such that every edge in $G$ contains at least one vertex in $Q$.

**Hall's Marriage Theorem**: A bipartite graph $G=(V,E)$ with $V=V_1\cup V_2$ has a perfect matching if and only if:

1. $|V_1| = |V_2|$
2. For every subset $W\subseteq V_{1}$, $|R(W)|\ge |W|$, where $R(W)$ denotes the set of vertices in $V_2$ adjacent to at least one vertex in $W$.

**Proof**: (By Kőnig's theorem, in any bipartite graph, the maximum number of edges in a matching is equal to the minimum number of vertices in a vertex cover.)

- Sufficiency:
    - Assume $G$ has a perfect matching.
    - For any $W \subseteq V_1$ with $k$ elements, the matching dictates there must be exactly $k$ elements in $V_{2}$ strictly adjacent to them.
    - These $k$ elements are contained in $R(W)$, so $|R(W)| \geq |W|$.
- Necessity:
    - Assume $|V_1| = |V_2|$ and $|R(W)|\ge |W|$ for all $W \subseteq V_1$.
    - Since $V_1$ inherently covers all edges (as every edge has exactly one endpoint in $V_1$), $V_1$ is a valid vertex cover. The proof is complete if we show $V_1$ is a cover of **minimal size**.
    - Suppose $Q$ is a vertex cover of minimum size. Let $U_1 = Q \cap V_1$ and $U_2 = V_1 \setminus U_1$.
    - Since $U_2 \cap Q = \emptyset$, all edges incident to $U_2$ must be covered by their other endpoints in $V_2$. Thus, the neighbors of $U_2$ must be entirely contained within $Q$, meaning $R(U_2) \subseteq Q \cap V_2$.
    - By the initial assumption, $|R(U_2)|\ge |U_2|$.
    - This gives us: $|U_2| \le |R(U_2)| \le |Q \cap V_2|$.
    - Calculating the total size of $Q$: $|Q| = |Q \cap V_1| + |Q \cap V_2| = |U_1| + |Q \cap V_2| \ge |U_1| + |U_2| = |V_1|$.
    - Since $|Q| \geq |V_{1}|$, $V_1$ must be a vertex cover of minimum size.
