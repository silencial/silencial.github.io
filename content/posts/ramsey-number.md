---
title: Ramsey Number
date: 2018-12-13
lastmod: 2025-06-11
categories:
- Fun
tags:
- Math
- Discrete Math
---

**Problem:** What is the smallest integer $n$ such that in any group of $n$ people, where each pair consists of either mutual friends or mutual strangers, there must be a subgroup of three mutual friends or three mutual strangers?

<!--more-->

---

# Ramsey Number

The solution to this problem is $R(3,3)=6$. The proof involves rephrasing the problem in the language of graph theory: we must show that any 2-coloring (e.g., red and green) of the edges of a complete graph on six vertices, $K_6$, must contain a monochromatic triangle ($K_3$).

The proof is a straightforward application of the pigeonhole principle. Consider any vertex $v$ in $K_6$. It is connected to the other five vertices. By the pigeonhole principle, at least three of these five edges must be the same color. Assume, without loss of generality, that at least three edges incident to $v$ are red. Let the vertices connected to $v$ by these red edges be $a, b, c$. Now, consider the edges forming the triangle between vertices $a, b, c$.

* If any of the edges $\{a,b\}, \{b,c\}, \{c,a\}$ is red, a red triangle is formed with vertex $v$. For instance, if $\{a,b\}$ is red, the triangle is $\{v,a,b\}$.
* If none of these edges are red, they must all be green, forming a green triangle $\{a,b,c\}$.

In either case, a monochromatic triangle is guaranteed. This concept is generalized by the formal definition of Ramsey numbers:

- Let $i$ and $j$ be integers such that $i\ge 2$ and $j\ge 2$. A positive integer $m$ has the **$(i,j)$-Ramsey property** if, for any red-green coloring of the edges of a complete graph $K_m$, it must contain either a red $K_i$ or a green $K_j$ as a subgraph.
- The **Ramsey number** $R(i,j)$ is the smallest positive integer that has the $(i,j)$-Ramsey property.

Ramsey numbers have several fundamental properties:

1. $R(2,k)=k$
2. $R(i,j)=R(j,i)$
3. If $m$ has the $(i,j)$-Ramsey property, then so does every integer $n>m$.
4. If $m$ does not have the $(i,j)$-Ramsey property, then neither does any integer $n<m$.
5. If $i_1>i_2$, then $R(i_1,j)\ge R(i_2,j)$.

An important upper bound for $R(i,j)$ exists. If $i\ge 3$ and $j\ge 3$, then:

$$
R(i,j)\le R(i,j-1)+R(i-1,j)
$$

**Proof:** Let $m=R(i,j-1)+R(i-1,j)$. Suppose the edges of $K_m$ have been colored red or green, and let $v$ be an arbitrary vertex of $K_m$. Partition the remaining $m-1$ vertices into two sets:

- $A$: the set of vertices adjacent to $v$ via a red edge.
- $B$: the set of vertices adjacent to $v$ via a green edge.

Since $|A|+|B| = m-1 = R(i,j-1)+R(i-1,j)-1$, the pigeonhole principle implies that either $|A|\ge R(i-1,j)$ or $|B|\ge R(i,j-1)$.

Assume the first case holds: $|A|\ge R(i-1,j)$. Consider the subgraph induced by the vertices in $A$. By the definition of a Ramsey number, this subgraph must contain either a red $K_{i-1}$ or a green $K_j$.

- If it contains a red $K_{i-1}$, then this clique, together with vertex $v$ and the red edges connecting $v$ to it, forms a red $K_i$.
- If it contains a green $K_j$, then we have found our monochromatic clique.

A symmetric argument applies if $|B|\ge R(i,j-1)$. Thus, $K_m$ must contain either a red $K_i$ or a green $K_j$.

Furthermore, this bound can be tightened if both $R(i,j-1)$ and $R(i-1,j)$ are even: $R(i,j)\le R(i,j-1)+R(i-1,j)-1$.

Another upper bound for the Ramsey number is $R(i,j)\le \binom{i+j-2}{i-1}$.

The list of known exact values for Ramsey numbers is very short.

| i/j  |  2   |  3   |   4    |    5    |    6    |
| :--: | :--: | :--: | :----: | :-----: | :-----: |
|  2   |  2   |      |        |         |         |
|  3   |  3   |  6   |        |         |         |
|  4   |  4   |  9   |   18   |         |         |
|  5   |  5   |  14  |   25   |  43–48  |         |
|  6   |  6   |  18  | 35–41  |  58–87  | 102–165 |
|  7   |  7   |  23  | 49–61  | 80–143  | 115–298 |
|  8   |  8   |  28  | 59–84  | 101–216 | 134–495 |
|  9   |  9   |  36  | 73–115 | 133–316 | 183–780 |

# Generalization

The numbers discussed so far represent the most common family of Ramsey numbers. This section briefly explores other generalizations.

Suppose $i_1,i_2,\dots,i_n$ are integers, where each $i_j\ge 2$. A positive integer $m$ has the **$(i_1,\dots,i_n;2)$-Ramsey property** if, for any coloring of the edges of $K_m$ using $n$ colors, there exists some color $j$ for which a monochromatic complete subgraph $K_{i_j}$ is formed. The smallest positive integer with this property is the **Ramsey number** $R(i_1,\dots,i_n;2)$. The exact values of these multicolor Ramsey numbers are also largely unknown.

Ramsey's theorem can be stated more generally without reference to graphs, using sets and their subsets. This leads to the concept of hypergraph Ramsey numbers.

Suppose that $i_1,i_2,\dots,i_n,r$ are positive integers where $n\ge 2$ and each $i_j\ge r$. A positive integer $m$ has the **$(i_1,\dots,i_n;r)$-Ramsey property** if the following is true:

If $S$ is a set of size $m$ and its $r$-element subsets are partitioned into $n$ collections $C_1,\dots,C_n$, then for some $j$, there exists a subset of $S$ of size $i_j$, all of whose $r$-element subsets belong to the collection $C_j$.

The **Ramsey number** $R(i_1,\dots,i_n;r)$ is the smallest positive integer with this property. In the case where $r=1$, the number is determined by the pigeonhole principle:

$$
R(i_1,\dots,i_n;1)=i_1+\dots+i_n-(n-1)
$$

**Proof:** Let $m=i_1+\dots+i_n-n+1$. Consider a set $S$ of size $m$ whose elements (1-element subsets) are partitioned into $n$ classes $C_1, \dots, C_n$. By the pigeonhole principle, there must be at least one class $C_j$ with $|C_j|\ge i_j$. This shows that $R(i_1,\dots,i_n;1)\le m$. To show this bound is tight, consider a set of size $m-1$. It can be partitioned into $n$ classes $C_j$ of size $i_j-1$. For such a partition, no class $C_j$ contains a subset of size $i_j$, establishing the lower bound.

# Schur's Theorem

The **Schur number**, $S(k)$, is the largest integer $n$ for which the set $\{1, 2, \dots, n\}$ can be partitioned into $k$ subsets, none of which contains elements $x,y,z$ (not necessarily distinct) such that $x+y=z$.

Schur's Theorem states that $S(k)$ always exists and is bounded by the classical Ramsey numbers:

$$
S(k) \le R(\underbrace{3,\dots,3}_{k};2)-1
$$

**Proof:** Let $k$ be the number of partitions. Let $n = R(\underbrace{3,\dots,3}_{k};2)$. Consider any partition of the set $\{1, 2, \dots, n-1\}$ into $k$ color classes $C_1, \dots, C_k$. We can use this partition to color the edges of the complete graph $K_n$ with vertices labeled $\{1, 2, \dots, n\}$. For any edge $(i,j)$ where $i>j$, we assign it the color of $i-j$.

By the definition of the Ramsey number $R(3,\dots,3;2)$, this colored graph must contain a monochromatic triangle. Let the vertices of this triangle be $a,b,c$ with $a<b<c$. Let $x=b-a$, $y=c-b$, $z=c-a$. Then $x$, $y$, $z$ all belong to the same color class and $x+y=z$.

This proves that for any $k$-coloring of $\{1, 2, \dots, n-1\}$, one color class must contain such a solution. Therefore, $S(k) \le n-1$.

# Happy Ending Problem

- convex set: a set of points is convex if it contains every line segment between two points in the set.
- convex polygon: a polygon that is the boundary of a convex set.
- happy ending problem: any set of five points in the plane in general position (no three are collinear) has a subset of four points that form the vertices of a convex quadrilateral.

Generalization of the happy ending problem: for any positive integer $m>3$, there is a minimum number of points, $n$, such that any set of $n$ points in the plane in general position contains a subset of $m$ points that form a convex $m$-gon.

This number is related to a Ramsey number: $n < R(m,5;4)$

**Proof:** Let $S$ be a set of $n\ge R(m,5;4)$ points in the plane, with no three points collinear. We partition the 4-element subsets of $S$ into two collections:

- $C_1$: 4-element subsets whose points form a convex quadrilateral.
- $C_2$: 4-element subsets whose points form a non-convex quadrilateral (one point is inside the triangle formed by the other three).

By the definition of $R(m,5;4)$, there must be either:

1. A subset $A\subseteq S$ of size $m$ where all its 4-element subsets are in $C_1$ (are convex).
2. A subset $B\subseteq S$ of size 5 where all its 4-element subsets are in $C_2$ (are non-convex).

Case 2 is impossible from the original happy ending problem. Therefore, case 1 must hold.

We must now show that this set $A$ of $m$ points forms a convex $m$-gon. Assume, for the sake of contradiction, that it does not. In that case, the convex hull of $A$ would be a $k$-gon for some $k<m$. This implies at least one point of $A$, let's call it $p_{i}$, lies inside the convex hull formed by other points of $A$. By triangulating the convex hull, we can find three vertices from the hull, say $p_1, p_2, p_3$, that form a triangle containing $p_{in}$. The set $\{p_{i}, p_1, p_2, p_3\}$ is a 4-element subset of $A$ that forms a non-convex quadrilateral. This contradicts our finding that all 4-element subsets of $A$ are convex. Therefore, the initial assumption must be false, and the $m$ points of $A$ must form a convex $m$-gon.

# Graph Ramsey Number

The concept of Ramsey numbers can be extended from finding monochromatic complete subgraphs ($K_i$) to finding monochromatic subgraphs of any specified type.

Let $G_1,\dots,G_n$ be arbitrary graphs. The **graph Ramsey number** $R(G_1,\dots,G_n)$ is the smallest integer $m$ such that any edge-coloring of the complete graph $K_m$ with $n$ colors contains a monochromatic subgraph isomorphic to $G_j$ in color $j$, for some $j \in \{1,\dots,n\}$.

It is a fundamental result that for any choice of graphs $G_1, \dots, G_n$, the graph Ramsey number $R(G_1, \dots, G_n)$ exists. If graph $G_j$ has $i_j$ vertices, this number is bounded by the classical Ramsey number:

$$
R(G_1,\dots,G_n)\le R(i_1,\dots,i_n;2)
$$
