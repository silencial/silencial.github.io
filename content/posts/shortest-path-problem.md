---
title: Shortest Path Problem
date: 2018-12-18
lastmod: 2025-06-22
categories:
- Fun
tags:
- Math
- Discrete Math
---

Problems like finding the optimal route between two cities on a complex road network, or transmitting a message between two nodes across a sprawling computer network, are all instances of the shortest path problem.

<!--more-->

---

# Dijkstra's Algorithm

Dijkstra's algorithm finds the shortest path in a weighted graph between a starting vertex $a$ and all other vertices, including a target vertex $z$. The algorithm works by iteratively assigning distance labels to vertices. Initially, the starting vertex $a$ is labeled with $0$, and all other vertices are labeled with $\infty$. Labels are initially temporary. A label becomes permanent once it is determined to be the shortest possible distance from $a$. When the process terminates—once all reachable vertices have permanent labels—the labels represent the lengths of the shortest paths from $a$ to every other vertex in the graph.

As an example, let's apply this algorithm to find the shortest path between $a$ and $z$ in the graph below:

![Dijkstra's Algorithm Example](https://i.imgur.com/yauUBhg.png)

1. The paths from the starting vertex $a$ to its immediate neighbors are to $b$ (length $4$) and $d$ (length $2$). The shorter of these is the path to $d$. Therefore, $d$ is the closest vertex to $a$, with a path of length $2$.
2. The second closest vertex is found by considering paths from $a$ that pass through the now-visited set $\{a,d\}$. There are two such paths to consider: path $a,d,e$ with a total length of $5$ (since $2+3=5$), and path $a,b$ of length $4$. Hence, the second closest vertex to $a$ is $b$, with a path of length $4$.
3. We now examine paths starting from the visited set $\{a, d, b\}$. There are three potential new paths: $a,b,c$ (length $4+3=7$), $a,b,e$ (length $4+3=7$), and $a,d,e$ (length $2+3=5$). We choose the shortest of these, establishing the path to $e$ as $a,d,e$ with a length of $5$.
4. Finally, we examine paths from the set $\{a, d, b, e\}$ to the remaining vertices. The path to $c$ via $b$ ($a,b,c$) has a length of $7$, while the path to $z$ via $e$ ($a,d,e,z$) has a length of $6$. The shorter of these is the path to $z$.

The shortest path from $a$ to $z$ is $a,d,e,z$ with a total length of $6$.

However, repeating this process for every vertex is inefficient for finding all-pairs shortest paths.

# Hedetniemi's Algorithm

This algorithm is based on a special matrix operation for computing matrix powers, which is called the **Hedetniemi matrix sum**. We begin with a connected, weighted graph with vertices $v_1,\dots,v_n$. With this graph, we associate an $n\times n$ "adjacency" matrix $A =[a_{ij}]$ defined as follows:

$$
a_{ij}=
\begin{cases}
0 &\text{if }i=j \\
x &\text{if } i\ne j \text{ and there is an edge of weight } x \text{ between } i \text{ and } j\\
\infty &\text{otherwise}
\end{cases}
$$

For the example graph above, if we denote the vertices $a,b,c,d,e,z$ by $v_1,v_2,v_3,v_4,v_5,v_6$ respectively, the adjacency matrix $A$ is:

$$
A=\begin{pmatrix}
0 & 4 & \infty & 2 & \infty & \infty \\
4 & 0 & 3 & \infty & 3 & \infty \\
\infty & 3 & 0 & \infty & \infty & 2 \\
2 & \infty & \infty & 0 & 3 & \infty \\
\infty & 3 & \infty & 3 & 0 & 1 \\
\infty & \infty & 2 & \infty & 1 & 0
\end{pmatrix}
$$

We now introduce an operation on matrices called the Hedetniemi matrix sum, denoted by $\dashv\vdash$. Let $A$ be an $m\times n$ matrix and $B$ be an $n\times p$ matrix. The Hedetniemi matrix sum is the $m\times p$ matrix $C=A\dashv\vdash B$, whose $(i,j)$-th entry, $c_{ij}$, is defined as:

$$
c_{ij}=\min_{1 \le k \le n}\{a_{ik}+b_{kj}\}
$$

Using this operation to compute $A^2 = A \dashv\vdash A$, we get:

$$
A^2=\begin{pmatrix}
0 & 4 & 7 & 2 & 5 & \infty \\
4 & 0 & 3 & 6 & 4 & 5 \\
7 & 3 & 0 & \infty & 3 & 2 \\
2 & 6 & \infty & 0 & 3 & 4 \\
5 & 3 & 3 & 3 & 0 & 1 \\
\infty & 4 & 2 & 4 & 1 & 0
\end{pmatrix}
$$

For example, the entry $a_{13}^{(2)}$ in $A^2$ is computed as follows:

$$
\begin{split}
a_{13}^{(2)}&=\min\{a_{11}+a_{13}, a_{12}+a_{23}, a_{13}+a_{33}, a_{14}+a_{43}, a_{15}+a_{53}, a_{16}+a_{63}\} \\
&=\min\{0+\infty, 4+3, \infty+0, 2+\infty, \infty+\infty, \infty+2 \} \\
&=7
\end{split}
$$

The resulting value, $7$, corresponds to the path $v_1 \rightarrow v_2 \rightarrow v_3$. Thus, $a_{13}^{(2)}$ represents the length of the shortest path of two or fewer edges from $v_1$ to $v_3$. In general, $A^m$ represents the lengths of the shortest paths between any two vertices using $m$ or fewer edges. Since a simple path in a graph with $n$ vertices can have at most $n-1$ edges, this leads to the following theorem.

**Theorem 1:** In a connected, weighted graph with $n$ vertices, the $(i,j)$-th entry of the Hedetniemi matrix $A^{n-1}$ is the length of the shortest path between $v_i$ and $v_j$.

In our example, with $6$ vertices, we can compute powers of $A$ until the matrix stabilizes. We find that $A^3=A^4=A^5$:

$$
A^3=A^5=\begin{pmatrix}
0 & 4 & 7 & 2 & 5 & 6 \\
4 & 0 & 3 & 6 & 4 & 5 \\
7 & 3 & 0 & 6 & 3 & 2 \\
2 & 6 & 6 & 0 & 3 & 4 \\
5 & 3 & 3 & 3 & 0 & 1 \\
6 & 4 & 2 & 4 & 1 & 0
\end{pmatrix}
$$

Therefore, the shortest path from $v_1$ to $v_6$ has a length of $6$. The fact that the matrix stabilizes at $A^3$ gives rise to our next theorem.

**Theorem 2:** For a connected, weighted graph with $n$ vertices, let $k$ be the smallest positive integer such that the Hedetniemi matrix power $A^k = A^{k+1}$. Then $A^k$ is the matrix of shortest path lengths, and no shortest path in the graph consists of more than $k$ edges.

# Shortest Path Calculation

To reconstruct the actual shortest path between two vertices, one must have the final matrix (e.g., $A^k$), its predecessor ($A^{k-1}$), and the initial adjacency matrix $A$. We will find the shortest path from $v_1$ to $v_6$. From the matrix $A^3$, we know the length is $a_{16}^{(3)}=6$.

The value $a_{16}^{(3)}$ is derived from the calculation $a_{16}^{(3)} = \min_{k} \{a_{1k}^{(2)} + a_{k6}\}$. To find the path, we must identify which value of $k$ produced this minimum. We use the first row of $A^2$ and the sixth column of $A$:

- Row 1 of $A^2$: $(0,4,7,2,5,\infty)$
- Column 6 of $A$: $(\infty,\infty,2,\infty,1,0)^T$

The minimum value, $6$, is uniquely obtained from $a_{15}^{(2)} + a_{56} = 5 + 1$. This indicates that the final edge in the shortest path is $(v_5, v_6)$, and the preceding segment is the shortest path from $v_1$ to $v_5$ that uses two or fewer edges.

We repeat this process to find the edge preceding $v_5$. The path length is given by $a_{15}^{(2)}=5$. We trace this back by calculating $a_{15}^{(2)} = \min_k\{a_{1k} + a_{k5}\}$. We use the first row of $A$ and the fifth column of $A$:

- Row 1 of $A$: $(0, 4, \infty, 2, \infty, \infty)$
- Column 5 of $A$: $(\infty, 3, \infty, 3, 0, 1)^T$

The minimum value, $5$, arises from $a_{14} + a_{45} = 2+3$. This means the path to $v_5$ comes from $v_4$. The path from $v_1$ to $v_4$ is simply the edge $(v_1, v_4)$, which has a weight of $2$.

Assembling these segments, the shortest path from $v_1$ to $v_6$ is $v_1 \rightarrow v_4 \rightarrow v_5 \rightarrow v_6$, with a total length of $2+3+1=6$.

Thus, the Hedetniemi method not only finds shortest path lengths but also provides a clear framework for reconstructing the paths themselves, with each matrix power corresponding to paths of a certain maximum number of edges.
