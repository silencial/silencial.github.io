---
title: Ramsey Numbers
date: 2018-12-13
categories:
- Fun
tags:
- Math
- Discrete Math
---

Find the smallest integer $n$ that there must be either three mutual friends or three mutual strangers among $n$ people, suppose each pair of people are either friends or strangers?

<!--more-->

---

# Ramsey Numbers

The answer to the above problem is $6$. To prove it, we first rephrase this problem as an edge-coloring problem: Show that if every edge of $K_6$ is colored red or green, then there must be either a red triangle or a green triangle.

The proof is a simple application of the pigeonhole principle. Pick a vertex $v$ in $K_6$, there are at least three red edges or three green edges incident on $v$. Assuming that $v$ has at least three red edges, say $\{v,a\},\{v,b\}$ and $\{v,c\}$, we examine the edges joining $a,b,c$. If any of these three edges is red, we obtain a red triangle. If non of the three edges is red, then we have a green triangle joining $a,b,c$.

We extend this idea to be a more general definition:

- Let $i$ and $j$ be integers such that $i\ge 2$ and $j\ge 2$. A positive integer $m$ has the $(i,j)$-Ramsey property if $K_m$ contains either a red $K_i$ ore a green $K_j$ as a subgraph, no matter how the edges of $K_m$ are colored red or green.
- The Ramsey number $R(i,j)$ is the smallest positive integer that has the $(i,j)$-Ramsey property.

We can see some properties of the Ramsey Numbers:

1. $R(2,k)=k$.
2. $R(i,j)=R(j,i)$.
3. If $m$ has the $(i,j)$-Ramsey property, then so does every integer $n>m$.
4. If $m$ does not have the $(i,j)$-Ramsey property, then neither does integer $n<m$.
5. If $i_1>i_2$, then $R(i_1,j)\ge R(i_2,j)$.

We also give an upper bound for $R(i,j)$. If $i\ge 3$ and $j\ge 3$, then
$$
R(i,j)\le R(i,j-1)+R(i-1,j)
$$
**Proof:** Let $m=R(i,j-1)+R(i-1,j)$. Suppose the edges of $K_m$ have been colored red or green and $v$ is a vertex of $K_m$. Partition the vertex set $V$ into two subsets:

- $A=$ all vertices adjacent to $v$ along a red edge
- $B=$ all vertices adjacent to $v$ along a green edge

Since
$$
|A|+|B|=|A\cup B|=m-1=R(i,j-1)+R(i-1,j)-1
$$
either $|A|\ge R(i-1,j)$ or $|B|\ge R(i,j-1)$. Consider the case where $|A|\ge R(i-1,j)$. We call the complete subgraph on the vertices in $A$ as $K_{|A|}$. Since $|A|\ge R(i-1,j)$, $K_{|A|}$ has either a red $K_{i-1}$ or a green $K_j$. If we have a red $K_{i-1}$, we add the red edges joining $v$ to the vertices of $K_{i-1}$ to obtain a red $K_i$. Thus, $K_{|A|}$, and hence $K_m$, has either a red $K_i$ or a green $K_j$.

Furthermore, if $R(i,j-1)$ and $R(i-1,j)$ are even, then $R(i,j)\le R(i,j-1)+R(i-1,j)-1$.

Another upper bound for Ramsey number is $R(i,j)\le C(i+j-2,i-1)$.

The list of Ramsey numbers whose exact values are known is very short

|  i/j  |  2   |  3   |   4    |    5    |    6    |
| :---: | :--: | :--: | :----: | :-----: | :-----: |
| **2** |  2   |      |        |         |         |
| **3** |  3   |  6   |        |         |         |
| **4** |  4   |  9   |   18   |         |         |
| **5** |  5   |  14  |   25   |  43~48  |         |
| **6** |  6   |  18  | 35~41  |  58~87  | 102~165 |
| **7** |  7   |  23  | 49~61  | 80~143  | 115~298 |
| **8** |  8   |  28  | 59~84  | 101~216 | 134~495 |
| **9** |  9   |  36  | 73~115 | 133~316 | 183~780 |

# Generalizations

The Ramsey numbers discussed in the last section represent only one family of Ramsey numbers. In this section we will briefly study some other families of these numbers.

Suppose $i_1,i_2,\dots,i_n$ are positive integers, where each $i_j\ge 2$. A positive integer $m$ has the $(i_1,\dots,i_n;2)$-Ramsey property if, given $n$ colors $1,2,\dots,n$, $K_m$ has a subgraph $K_{i_j}$ of color $j$, for some $j$, no matter how the edges of $K_m$ are colored with the $n$ colors.

The smallest positive with the $(i_1,\dots,i_n;2)$-Ramsey property is called the Ramsey number $R(i_1,\dots,i_n;2)$.

Very little is also known about the number $R(i_1,\dots,i_n;2)$ if $n\ge 3$. However, if $i_j=2$ for all $j$, then
$$
R(2,\dots,2;2)=2
$$

We can also represent Ramsey numbers without graph but only in terms of a set and properties of a certain collection of its subsets.

Suppose that $i_1,i_2,\dots,i_n,r$ are positive integers where $n\ge 2$ and each $i_j\ge r$. A positive integer $m$ has the $(i_1,\dots,i_n;r)$-Ramsey property if the following statement is true:

If $S$ is a set of size $m$ and the $r$-element subsets of $S$ are partitioned into $n$ collections $C_1,\dots,C_n$, then for some $j$ there is a subset of $S$ of size $i_j$ such that each of its $r$-element subsets belong to $C_j$.

The Ramsey number $R(i_1,\dots,i_n;r)$ is the smallest positive integer that has the $(i_1,\dots,i_n;r)$-Ramsey property. If $r=1$, this number is easy to find:
$$
R(i_1,\dots,i_n;1)=i_1+\dots+i_n-(n-1)
$$
**Proof:** Let $i_1+\dots+i_n-(n-1)=m$. Take a set $S$ of size $m$ and divide its $1$-element subsets into $n$ classes $C_1,\dots,C_n$. There must be a subscript $j_0$ such that $|C_{j_0}|\ge i_{j_0}$. If we take any $i_{j_0}$ elements of $C_{j_0}$, we have a subset of $S$ of size $i_{j_0}$ that has all its $i$-element subsets belonging to $C_{j_0}$. This shows that $R(i_1,\dots,i_n;1)\le i_1+\dots+i_n-(n-1)$.

Take a set $S$ of size $m-1$. Partition its $1$-element subsets into $n$ classes $C_1,\dots,C_n$ where $|C_j|=i_j-1$. With this partition there is no subset of $S$ of size $i_j$ that has all its $1$-element subsets belonging to $C_j$.

# Schur's Theorem

$S(c)$, called the Schur's number, is the smallest positive integers that for any partition of the set $\{1,2,\dots,S(c)\}$ into $c$ subparts, one of the parts contains three integers $x,y,z$ with $x+y=z$.

Schur's Theorem states that the numbers $S(k)$ always exist and are bounded by the Ramsey numbers
$$
S(k)\le R(3,\dots,3;2)
$$
where there are $k$ $3$s.

**Proof:** Let $n=R(3,\dots,3;2)$ and color the integers $1,2,\dots,n$ with $k$ colors. This gives a partition of $1,2,\dots,n$ into $k$ sets, $S_1,S_2,\dots,S_k$, where integers in the same set have the same color. Now take the graph $K_n$, label its vertices $1,2,\dots,n$ and label its edges $\{i,j\}$ with color $m$ if $|i-j|\in S_m$. This gives a coloring of $K_n$ with $k$ colors. There must be a monochromatic triangle in $K_n$. if its $3$ vertices are $i_1,i_2,i_3$, and we let $x=i_1-i_2,y=i_2-i_3$, and $z=i_1-i_3$, then we have $x+y=z$ where all $3$ values have the same color.

# Convex Sets

A **convex polygon** is a polygon $P$ such that if $x$ and $y$ are points in the interior of $P$, then the line segment joining $x$ and $y$ lies completely inside $P$.

Suppose $m$ is a positive integer and there are $n$ given points, no three of which are collinear. If $n\ge R(m,5;4)$, then a convex $m$-gon can be obtained from $m$ of the $n$ points.

**Proof:** Suppose $n\ge R(m,5;4)$ and $S$ is a set of $n$ points in the plane, with not three points collinear. With Ramsey property, no matter how we divide the $4$-element subsets of $S$ into two collections $C_1$ and $C_2$, either

1. There is a subset of $S$ of size $m$ with all its $4$-element subsets in $C_1$, or
2. There is a subset of $S$ of size $5$ with all its $4$-element subsets in $C_2$

We now take $C_1$ to be the collection of all subsets of $S$ of size four where the $4$-gon determined by the points is convex and take $C_2$ to be not convex. Note that (ii) cannot happen. That is, we can always find a convex $4$-gon among $5$ points in the plane. Therefore, we are guaranteed of having a subset $A\subseteq S$ where $|A|=m$ and all $4$-gons determined by $A$ are convex.

We will now show that the $m$ points of $A$ determine a convex $m$-gon. Let $k$ be the largest positive integer such that $k$ of the $m$ points form a convex $k$-gon $G$. If $k<m$, then at least one of the elements of $A$, say $a_1$, lies inside the convex $k$-gon $G$. Therefore there are three vertices of $G$, say $a_2,a_3,a_4$, such that $a_1$ lies inside the triangle determined by these vertices. Hence, the set $\{a_1,a_2,a_3,a_4\}$ determines a nonconvex $4$-gon, contradicting the property of $A$ that all its $4$-gons are convex. Therefore we must have $k=m$.

# Graph Ramsey Numbers

So far, we have examined colorings of $K_m$ and looked for monochromatic $K_i$ subgraphs. But suppose we don't look for complete subgraphs $K_i$, but rather try to find other subgraphs, such as cycle graphs $(C_i)$, wheels $(W_i)$, complete bipartite graphs $(K_{i,j})$ or trees?

Suppose $G_1,\dots,G_n$ are graphs, each with at least one edge. An integer $m$ has the $(G_1,\dots,G_n)$-Ramsey property if every coloring of the edges of $K_m$ with the $n$ colors $1,2,\dots,n$ yields a subgraph $G_j$ of color $j$, for some $j$.

The graph Ramsey number $R(G_1,\dots,G_n)$ is the smallest positive integer with the $(G_1,\dots,G_n)$-Ramsey property.

For $j=1,\dots,n$, suppose that $G_j$ is a graph with $i_j$ vertices (where $i_j\ge 2$). Then the graph Ramsey number $R(G_1,\dots,G_n)$ exists, and
$$
R(G_1,\dots,G_n)\le R(i_1,\dots,i_n;2)
$$

# Reference

1. [Ramsey's Theorem](https://en.wikipedia.org/wiki/Ramsey%27s_theorem#Ramsey_numbers)
2. [Schur's Theorem](https://en.wikipedia.org/wiki/Schur%27s_theorem)
3. [Complete Graph](https://en.wikipedia.org/wiki/Complete_graph)
4. [Wheel Graph](https://en.wikipedia.org/wiki/Wheel_graph)
5. [Bipartite Graph](https://en.wikipedia.org/wiki/Bipartite_graph)
