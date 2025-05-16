---
title: Catalan Numbers
date: 2018-12-05
categories:
- Fun
tags:
- Math
- Discrete Math
---

In combinatorial mathematics, the Catalan numbers form a sequence of natural numbers that occur in various counting problems, often involving recursively-defined objects.

<!--more-->

---

# Paths and Catalan Numbers

Suppose a path in the plane from the origin to the point $(2n,0)$ whose steps are the line segments between $(i-1,s_{i-1})$ and $(i,s_i)$, for $i$. The $i$-th step in this path has slope $s_i-s_{i-1}=x_i\in\{1,-1\}$.

We call a path from $(0,0)$ to $(2n,0)$ **nonnegative** if all $s_i\ge 0$ and **positive** if $s_i>0$ for $i=1,2,\dots,2n-1$. The number of nonnegative paths and positive paths from the origin to the point $(2n,0)$ are both Catalan numbers:

$$
c_n=\frac{1}{n+1} \binom{2n}{n}
$$

The number of positive paths from the origin to $(2n,0)$ are $c_{n-1}$, the number of nonnegative paths are $c_n$.

**Proof:** Let $(s_0,s_1,\dots,s_{2n})$ be the partial sum sequence of a positive path $P$. Then $s_0=s_{2n}=0$ and $s_i\ge 1$ for $i=1,2,\dots,2n-1$. Let $x_i=s_i-s_{i-1}\in\{1,-1\}$. Since $P$ must pass through the point $(1,1)$ and $(2n-1,1)$. If we omit the first and last terms from the slope sequence, we have a sequence $x'$ that has $n-1$ $1$s and $n-1$ $-1$s. Further, the partial sums for $x'$ satisfy

$$
\begin{equation*}
\begin{split}
s_i'&=x_1'+x_2'+\cdots+x_i' \\
&=x_2+x_3+\cdots+x_{i+1} \\
&=s_{i+1}-1 \\
&\ge 0
\end{split}
\end{equation*}
$$

for $0\le i \le 2n-2$. Thus the positive path $P$ from the origin to $(2n,0)$ corresponds to a nonnegative path $P'$ from the origin to $(2n-2,0)$.

A positive path $P$ from the origin to $(2n,0)$ is determined uniquely by its segment $P'$ from $(1, 1)$ to $(2n − 1,1)$, which lies entirely above the $x$-axis. The number of such paths $P'$ is the difference between the total number of paths $C^{2n-2}_{n-1}$, and the number of paths $Q$ that meet the $x$-axis.

Consider a path $Q^*$ from $(1,-1)$ to $(2n-1,1)$. Every such path must cross the $x$-axis. We can obtain the path $Q$ from $Q^*$ by reflecting the segment of $Q^*$ from $(1,-1)$ to its first point on the $x$-axis. Since $Q^*$ must have two more increasing steps than decreasing steps, the number of such paths is $C^{2n-2}_{n-2}$.

Now we have the number of $P$:

$$
\begin{equation*}
\begin{split}
a_n &= \binom{2n-2}{n-1}-\binom{2n-2}{n-2} \\
&= c_{n-1}
\end{split}
\end{equation*}
$$

# Well-Formed Sequences of Parentheses

A sequence of parentheses is **well-formed** if and only if it can be derived by a finite sequence of the following rules:

- The empty sequence is well-formed.
- If $A$ is well-formed, then $(A)$ is well-formed.
- If $A$ and $B$ are well-formed, then $AB$ is well-formed.

We can easily see the number of well-formed sequences of parentheses of length $2n$ is the Catalan number $c_n$.

# Stack Permutations

A **stack** is a list which can only be changed by insertions or deletions at one end, called the top of the list. When characters are individually inserted and deleted from the stack, the last one inserted must be the first one deleted from the stack (lifo). An insertion to the top of the stack is called a **push**; a deletion from the top is called a **pop**.

A sequence of pushes and pops is **admissible** if the sequence has an equal number $n$ of pushes and pops, and at each stage the sequence has at least as many pushes as pops. The number of admissible sequences of pushes and pops of length $2n$ is the Catalan number $c_n$.

After $n$ pushes and $n$ pops, the output string is a permutation of $N$ called a **stack permutation**. The number of stack permutations is also the Catalan number $c_n$.

# Well-Parenthesized Products

Consider a binary operation which we will refer to as multiplication. We assume that the operation is neither commutative nor associative. Then a product $x_1x_2\cdots x_{n+1}$ is defined only after parentheses have been suitably inserted.

A product is **well-parenthesized** if it can be obtained recursively by a finite sequence of the following rules:

- Each single term $x$ is well-parenthesized.
- If $A$ and $B$ are well-parenthesized, then $(AB)$ is well-parenthesized.

We can see that if all the $n+1$ variables are deleted from a well-parenthesized product, the $n$ pairs of parentheses that remain must be a well-formed sequence of parentheses, but not the other way around.

The number of well-parenthesized products of $n+1$ variables is the Catalan number $c_n$.

**Proof:** A well-parenthesized product forms string $p$ of length $3n+1$ with three types of characters: $n$ left parentheses, $n+1$ variables, and $n$ right parentheses. Set another string $q$ of length $2n$ by deleting the last variable $x_{n+1}$ and the $n$ right parentheses. We can show that there is one-to-one correspondence between $p$ and $q$. Also, the number of left parentheses in the string $q$ is at least as large as the number of variables. By defining left parenthesis as $1$ and variable $-1$, we transform this problem to partial sums and get the conclusion.

# Full Binary Trees

A full binary tree is a rooted tree in which each internal vertex has exactly two children. Thus, a full binary tree with $n$ internal vertices have $2n$ edges, $2n+1$ vertices, and $n+1$ leaves.

The number of full binary trees with $n$ internal vertices is the Catalan number $c_n$.

**Proof:** Suppose we label the leaves of a full binary tree $T$ with $x_1,x_2,\dots,x_{n+1}$. Then $T$ recursively defines a well-parenthesized product of $x_1,x_2,\dots,x_{n+1}$ by the following rule:

- Labeling rule: If $v$ is an internal vertex with left child $a$ and right child $b$, having labels $A$ and $B$, respectively, then label $v$ with $(AB)$.

The label on the root of the tree will be the well-parenthesized product.

# Triangulations of a Convex Polygon

An $n$-gon ($n\ge 3$) in the plane is a polygon $P$ with $n$ vertices and $n$ sides. A **diagonal** of $P$ is a line segment joining two nonadjacent vertices of $P$. An $n$-gon $P$ is **convex** if every diagonal lies wholly in the interior of $P$.

Let $D$ be a set of diagonals, no two of which meet in the interior of a convex $n$-gon $P$, that partitions the interior of $P$ into triangles. The sides of the triangles are either diagonals in $D$ or sides of $P$. The set $T$ of triangles obtained in this way is called a **triangulation** of $P$.

A triangulation of a convex $n$-gon has $n-2$ triangles determined by $n-3$ diagonals. Every triangulation $T$ of a convex $(n+2)$-gon $P$ has at least two **outer triangles** when $n\ge 2$. An outer triangle is made of two adjacent sides of $P$ and one diagonal of $D$.

The number of triangulations of a convex $(n + 2)$-gon is the Catalan number $c_n$.

**Proof:** Consider the string $s_1s_2\cdots s_{n+1}$ formed by taking the $n+1$ sides of $P$ other than $s_0$ in order around $P$. Then the product $s_1s_2\cdots s_{n+1}$ is well-parenthesized by a triangulation of $P$. We can always find an outer triangle that does not have $s_0$, and replace sides $s_i,s_{i+1}$ of $P$ by the diagonal labeled $(s_i s_{i+1})$. Now we have a convex $(n+1)$-gon and by the inductive hypothesis we can establish a one-to-one correspondence.

# Reference

1. [Catalan Number](https://en.wikipedia.org/wiki/Catalan_number)
