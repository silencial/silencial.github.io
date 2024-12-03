---
title: Interesting Problems
date: 2023-10-13
lastmod: 2024-06-18
categories:
- Fun
tags:
- Math
---


一些有趣的数学、物理、计算机问题

<!--more-->

---

# 1/89

```
 0.01
 0.001
 0.0002
 0.00003
 0.000005
 0.0000008
 0.000000013
 0.0000000021
 .......
```

将以上所有数相加，结果为 $1/89$

令 $F_i$ 为斐波那契数列的第 $i$ 项，则有：

$$
\sum_{i=1}^\infty F_i (10)^{-(i+1)} = \frac{1}{89}
$$

# Fitch's Paradox of Knowability

If all truths were knowable, then all truths are in fact known.

[Reference](https://www3.nd.edu/~jspeaks/courses/2009-10/20229/LECTURES/22-knower.pdf)

# 100 Prisoner Problem

**问题**：假设监狱中有编号 1~100 的囚犯，同时有一个房间有 100 个抽屉，监狱长将 1~100 的号码牌随机放入每个抽屉中，且每个抽屉一张。每个囚犯逐个进入房间并打开 50 个抽屉，之后关上。如果每个囚犯都在自己打开的 50 个抽屉中找到了自己的号码牌则所有囚犯都会被赦免。在第一个囚犯进入房间之前，囚犯之间可以讨论打开抽屉的策略。如何做能使被赦免的概率最大？

如果每个囚犯随机打开 50 个抽屉，则被赦免的概率为：

$$
\left(\frac{1}{2}\right)^{100} \approx 8\mathrm{e}{-31}
$$

事实上，有一种方法可以达到 >30% 的成功率，即依靠抽屉中号码牌的信息来指导下一个应该打开的抽屉，这样每个囚犯的赦免概率便不再独立，而是都依赖于号码的分布。

具体方法如下：

1. 首先打开自己号码对应的抽屉
2. 如果号码相同，则成功，若不同，则打开抽屉中号码对应的抽屉
3. 重复步骤 2，直到找到自己的号码

在这种策略下，囚犯被赦免的概率即为 1~100 的排列中最大的环的长度不超过 50 的概率。

1~100 的排列中存在 $l>50$ 的长度的环的概率为：

$$
\frac{\binom{100}{l}\cdot (l-1)!\cdot(100-l)!}{100!} = \frac{1}{l}
$$

则不存在长度超过 50 的环的概率为

$$
1-\sum_{l=51}^{100} \frac{1}{l} \approx 0.312
$$

同时当人数增加时，该概率趋近于 $1-\ln 2 \approx 0.307$

该策略被证明为最优的

[100 prisoners problem - Wiki](https://www.wikiwand.com/en/100_prisoners_problem)

# Reservoir Sampling

**问题**：如何从 $n$ 个项目的集合中选取 $k$ 个元素？$n$ 事先未知且较大，无法全部存放到内存中。

1. 将前 $k$ 个数储存至一个数组 $R$ 中
2. 对于第 $i$ 个元素 $x_i$，随机生成一个 $\{1,\dots,i\}$ 中的随机数 $j$
3. 若 $j\in \{1,\dots,k\}$，则令 $R[j] := x_i$，否则丢弃 $x_i$

使用归纳法可以证明每个元素被选取的概率相同

[Reservoir sampling - Wiki](https://www.wikiwand.com/en/Reservoir_sampling)

# Ramsey's Numbers

**问题**：在一个聚会上，至少需要邀请多少人，能使得其中至少有 $m$ 人相互认识，或至少 $n$ 人相互不认识？该数被定义为拉姆齐数：$R(m, n)$

一个有趣的例子为 $R(3, 3) =6$

$R(5,5)$ 的数值依旧未知，暴力计算复杂度为 $\mathcal{O}(2^{n^2})$

[Ramsey's theorem - Wiki](https://www.wikiwand.com/en/Ramsey%27s_theorem)

# Nim Game

**问题**：假设有 $n$ 堆石子，每堆石子的数量都 $>0$。现在有 $A,B$ 两人轮流取石子，每次只能从一堆中取走任意数量 $>0$ 的石子，将石子最后拿完的人判定为赢。请问是否有必胜策略？

一个非平凡的例子为 $(3,4,5)$，即三堆石子，分别有 $3,4,5$ 个石子，则先走的人有必胜策略。

1. 计算所有堆的 **nim-sum**，也即异或，$3\oplus 4 \oplus 5 = 2$
2. 需要使得取完后的 nim-sum 为 $0$
   1. 计算每堆和之前 nim-sum 的 nim-sum：$3\oplus 2 = 1$，$4\oplus 2=6$，$5\oplus 2=7$，找到结果小于原先石子数的堆，即 $3$
   2. 则取法为在 $3$ 的堆中取，并使得取完后的石子数为减少后的 nim-sum，即 $1$
3. 重复步骤 1-2，直至取完最后一个

使用异或的性质可以证明，对于 nim-sum 不为 $0$ 的堆，总有取法使得 nim-sum 为 $0$；而当 nim-sum 为 $0$，时，所有取法都会使得 nim-sum 不为 $0$，因此有必胜的策略。

在另一种玩法中：将最后石子拿完的人判定为输，必胜的策略几乎相同，只有在当石子数 $>1$ 的堆刚好只有一个时存在区别，此时玩家可以选取将该堆的石子全部拿走或只留下一个来控制最后一个拿走石子的人是自己或对手。

[Nim - Wiki](https://www.wikiwand.com/en/Nim)

# Fisher–Yates Shuffle

一种在 $\mathcal{O}(n)$ 时间随机洗牌的算法：

1. An array $a$ with length $n$ (indices $0..n-1$)
2. for $i$ from $n-1$ to $1$, do
   1. generate a random integer $j$ s.t. $0\le j \le i$
   2. swap $a[j]$ and $a[i]$

[Fisher–Yates shuffle - Wiki](https://www.wikiwand.com/en/Fisher%E2%80%93Yates_shuffle)

# Shape in a Lattice

**问题**：设想在一个平面上充满纵横间距为 1 的网格。同时存在一个面积小于 1 的任意图形。证明该图形必定能放在平面上并使得图形内部不包含任何格点

可以这样考虑：将图形放在平面上，移动网格使得条件成立。

1. 将包含图形的网格切下来（1x1 的正方形）并堆叠在一起
2. 从堆的正上方向下看，并想象这些格子是透明的，则图形会占据这个方格的部分。由于图形面积小于 1，则必存在一点没有被图形占据
3. 令这一点为这些网格的新格点，放回原平面中，则图形不包含任何格点。证明完毕

[Shape in a lattice](https://www.cut-the-knot.org/proofs/lattice.shtml)

# Cake Cutting

**问题**：三人分一块蛋糕，如何设计策略使得三人都满意？

这是 [Cake Cutting 问题](https://www.wikiwand.com/en/Fair_cake-cutting#Proportionality)，首先我们需要定义满意，常见的有两种

- Proportionality: 每个人都认为自己的一份不少于 1/n
- Envy-free: 每个人都不觉得别人拿的比自己多

可以看出 Envy-free 一定满足 Proportionality，反之则不一定成立。

对于两人情况 envy-free 策略很简单：一人切，另一人先选。

对于三人情况的 envy-free 策略：

1. 一个裁判从左向右连续走刀，三个人拿刀站在裁判右边，保持在自己认为平分右边蛋糕的位置
2. 一旦有人喊切，则此人获得裁判左边的蛋糕
3. 同时三人中间的人也把刀切下，剩下两人中靠左的人获得中间的蛋糕，靠右的人获得右边的蛋糕

对于离散情况的 envy-free 策略：

1. P1 将蛋糕平分三份 A, B, C
2. P2 将自己认为最大的那块蛋糕 A 切下一块，变为 A1 和 A2，使得 A1 和第二大的蛋糕一样大
3. P3 从 A1 和 B, C 中选一块
4. P2 从剩下两块中选，但如果 P3 没有选 A1 则 P2 必须选 A1
5. P1 分到剩下的一块，只剩下 A2 需要分
6. A1 一定被 P2 或 P3 分到，将分到的人记为 PA，另一人为 PB
7. PB 将 A2 分为三份
8. 按照 PA，P1， PB 的顺序依次选

[Stromquist moving-knives procedure](https://www.wikiwand.com/en/Stromquist_moving-knife_procedure)

[Selfridge–Conway procedure](https://www.wikiwand.com/en/Selfridge%E2%80%93Conway_discrete_procedure)

# 红眼问题

**问题**：一个岛上有 100 个人，其中有 5 个红眼睛，95 个蓝眼睛。岛上有三个奇怪的怪则：

1. 他们不能照镜子，不能看到自己眼睛的颜色。
2. 他们不能告诉别人对方的眼睛是什么颜色。
3. 一旦有人知道了自己眼睛的颜色，他就必须在当天夜里自杀。

某天有个旅行者到了这个岛上，但不知道这里的规矩。当他在和全岛人狂欢的时候，说了一句话：你们这里有红眼睛的人。假设这个岛上的人都足够聪明，即都有严密的逻辑推理能力。请问接下来会发生什么事？



通过数学归纳法可以容易得出，假设这个岛上有 N 个红眼睛的人，那么在第 N 天红眼睛的人会一起自杀，在第 N+1 天蓝眼睛的人也会一起自杀。

这里的有趣之处在于，看似旅行者说了一句废话，因为岛上的每个人都知道「这里有红眼睛的人」。但其实是混淆了 Mutual Knowledge 和 Common Knowledge 的区别。对于命题 P，前者只需要每个人都知道 P，而后者需要：

1. 所有人都知道 P
2. 所有人都知道所有人知道 P
3. ...

一直下去，直到无穷。
