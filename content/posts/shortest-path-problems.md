---
title: Shortest Path Problem
date: 2018-12-18
categories:
- Fun
tags:
- Math
- Discrete Math
---

Finding the best route between two cities, given a complicated road network; or transmitting a message between two computers along a network of hundreds of computers, are all classified as the shortest path problem.

<!--more-->

---

# Dijkstra's Algorithm

Dijkstra's algorithm was designed to find the shortest path, in a weighted graph, between two points $a$ and $z$. This is done by initially labeling each vertex other than $a$ with $\infty$, labeling $a$ with $0$, and then modifying the labels as shortest paths within the graph were constructed. These labels are temporary; they become permanent when it becomes apparent that a label could never become smaller.

If this process is continued until every vertex in a connected, weighted graph has a permanent label, then the lengths of the shortest paths from $a$ to each other vertex of the graph are determined.

As an example, we use this algorithm to find the shortest path between $a$ and $z$ in the graph below:

![Dijkstra's Algorithm Example](https://i.imgur.com/yauUBhg.png)

1. The only paths starting at $a$ that contain no vertex other than $a$ are formed by adding an edge that has $a$ as one endpoint. These paths have only one edge. They are $a,b$ of length $4$ and $a,d$ of length $2$. It follows that $d$ is the closest vertex to $a$ with length $2$.
2. The second closet vertex can be found by examining all paths that begin with the shortest path from $a$ to a vertex in the set $\{a,d\}$, followed by an edge that has one endpoint in $\{a,d\}$ and its other endpoint not in this set. There are two such paths to consider, $a,d,e$ of length $7$ and $a,b$ of length $4$. Hence, the second closet vertex to $a$ is $b$ with length $4$.
3. Examine $\{a,d,b\}$. There are three such paths, $a,b,c$ of length $7$, $a,b,e$ of length $7$, and $a,d,e$ of length $5$. We then choose $a,d,e$ of length $5$.
4. Examine $\{a,d,b,e\}$. There are two such paths, $a,b,c,$ of length $7$ and $a,d,e,z$ of length $6$. So the shortest path from $a$ to $z$ is $a,d,e,z$ with length $6$.

We can repeat this process with any other point besides $a$ as the initial point, and eventually all possible shortest paths between any two points of the graph can be determined. But it is not so efficient as a process to find all shortest paths.

# Hedetniemi's Algorithm

The algorithm constructed here is based on a new way to compute powers of matrices, which we will call the **Hedetniemi matrix sum**. Suppose we begin with a connected, weighted graph with vertices $v_1,\dots,v_n$. With this graph we associate the $n\times n$ "adjacency" matrix $A =[a_{ij}]$ defined as follows:
$$
a_{ij}=
\begin{cases}
0 &\text{if }i=j \\
x &\text{if } i\ne j \text{ and there is an edge of weight } x \text{ between } i \text{ and } j\\
\infty &\text{otherwise}
\end{cases}
$$
For example, the graph above have the adjacency matrix $A$, when $a,b,c,d,e,z$ are denoted by $v_1,v_2,v_3,v_4,v_5,v_6$ respectively.
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
We now introduce an operation on matrices called the Hedetniemi matrix sum, denoted by $\dashv\vdash$. Let $A$ be an $m\times n$ matrix and $B$ an $n\times p$ matrix. Then the Hedetniemi matrix sum is the $m\times p$ matrix $C=A\dashv\vdash B$, whose $(i,j)$th entry is
$$
c_{ij}=\min\{a_{i1}+b_{1j},a_{i2}+b_{2j},\dots,a_{1n}+b_{nj}\}
$$
Then
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
Look at a typical computation involved in finding $A^2=[a_{ij}^{(2)}]$:
$$
\begin{split}
a_{13}^{(2)}&=\min\{0+\infty, 4+3, \infty+0, 2+\infty, \infty+\infty, \infty+2 \} \\
&=7
\end{split}
$$
Notice that the value $7$ is the sum of $4$, the shortest path of length $1$ from $v_1$ to $v_2$, and $3$, the length of the edge between $v_2$ and $v_3$. Thus $a_{13}^{(2)}$ represents the length of the shortest path of two ore fewer edges from $v_1$ to $v_3$. So $A^2$ represents the lengths of all shortest paths with two or fewer edges between any two vertices. Similarly, $A^3$ represents the lengths of all shortest paths of three or fewer edges, and so on. Since, in a connected, weighted graph with $n$ vertices, there can be at most $n − 1$ edges in the shortest path between two vertices, the following theorem has been proved.

**Theorem 1** In a connected, weighted graph with $n$ vertices, the $(i,j)$th entry of the Hedetniemi matrix $A^{n-1}$ is the length of the shortest path between $v_i$ and $v_j$.

In the graph above, with $6$ vertices, we have
$$
A^5=\begin{pmatrix}
0 & 4 & 7 & 2 & 5 & 6 \\
4 & 0 & 3 & 6 & 4 & 5 \\
7 & 3 & 0 & 6 & 3 & 2 \\
2 & 6 & 6 & 0 & 3 & 4 \\
5 & 3 & 3 & 3 & 0 & 1 \\
6 & 4 & 2 & 4 & 1 & 0
\end{pmatrix}
$$
Therefore, the shortest path from $v_1$ to $v_6$ is of length $6$.

An interesting fact about this example is that $A^3=A^5$. This happens because in this graph, no shortest path has more than $3$ edges. So, no improvements in length can occur after $3$ iterations. This gives us the following theorem.

**Theorem 2** For a connected, weighted graph with $n$ vertices, if the Hedetniemi matrix $A^k\ne A^{k-1}$, but $A^k=A^{k+1}$, then $A^k$ represents the set of lengths of shortest paths, and no shortest path contains more than $k$ edges.

# Shortest Path Calculations

To compute the actual shortest path from one point to another, it is necessary to have not only the final matrix, but also its predecessor and $A$ itself. We want to find the shortest path from $v_1$ to $v_6$. Now
$$
a_{16}^{(3)}=a_{1k}^{(2)}\dashv\vdash a_{k6}
$$
for some $k$. The entries $a_{1k}^{(2)}$ form the row vector
$$
(0,4,7,2,5,\infty)
$$
and the entries $a_{k6}$ form the column vector
$$
(\infty,\infty,2,\infty,1,0)
$$
Since the only way in which $6$ arises is as the sum $5+1$ when $k=5$, the shortest path ends with an edge of length $1$ from $v_5$ to $v_6$, following a path with $2$ or fewer edges from $v_1$ to $v_5$.

Now we can look for the previous edge ending at $v_5$. Note that $a_{15}^{(2)}=5$. The entries $a_{k5}$ form the column vector
$$
(\infty,3,\infty,3,0,1)
$$
This time $5$ arises, when $k=4$, as $2+3$, so there is an edge of length $2$ from $v_1$ to $v_4$. So the shortest path from $v_1$ to $v_6$ is $v_1, v_4, v_5, v_6$ (the edges of lengths $2, 3, 1$).

Thus, the Hedetniemi method provides a graphical interpretation at each stage of the computation, and the matrices can be used to retrieve the paths themselves.

# Reference

1. [Dijkstra's Algorithm](https://en.wikipedia.org/wiki/Dijkstra%27s_algorithm)
2. [Shortest path problem](https://en.wikipedia.org/wiki/Shortest_path_problem)
