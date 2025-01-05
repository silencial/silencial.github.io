---
title: 使用对偶图证明欧拉公式
date: 2025-01-05
categories:
- Fun
tags:
- Math
- 3Blue1Brown
---

使用 dual graph 证明欧拉公式：$V-E+F=2$
<!-- more -->

---

对于一个 connected planar graph $G$，如下图蓝色部分所示，其所对应的 dual graph $G^*$ 则为红色部分：

- $G^*$ 的每个顶点对应 $G$ 中的每个面
- $G^*$ 中的每条边连接两个顶点，这两个顶点对应于 $G$ 中相邻的两个面（即共享一条边的两个面）

对偶图有一些性质：

- 如果 $G^*$ 是 $G$ 的对偶图，那么 $G$ 也是 $G^*$ 的对偶图
- $G$ 的顶点数等于 $G^*$ 的面数
- $G$ 和 $G^*$ 的边数相等，对应的边被称为对偶边

![Dual Graph](https://i.imgur.com/eemPX5a.png)

考虑 $G$ 中的一个 spanning tree $S$，即包括 $G$ 中所有顶点和一些边组成的连通无环图。可以看出，剩下的边在 $G^*$ 中的对偶边也组成了 $G^*$ 的一个 spanning tree $S^*$。因为：

1. 因为 $S$ 无环，所以 $G$ 中剩下的边一定和所有面接触，即 $G^*$ 中所有顶点都被涵盖
2. 如果 $S^*$ 有环，则对应 $S$ 中必定有顶点不能取到，因此 $S^*$ 无环

又根据 tree 的性质我们有 $V-1=E$，从对偶图的性质又有 $V_{S^*} = F_G$，$E_S + E_{S^*} = E_G$，因此可以得到：
$$
\begin{align*}
&V_S - 1 + V_{S^*} - 1 = E_S + E_{S^*} \\
\Rightarrow \ & V_G + F_G - 2 = E_G
\end{align*}
$$

# 参考

1. [3Blue1Brown Video](https://youtu.be/-9OUyo8NFZg)
1. [Dual Graph — Wiki](https://en.wikipedia.org/wiki/Dual_graph)
