---
title: Theoretical Minimum
date: 2025-01-05
categories:
- Study
tags:
- Physics
---

*Theoretical Minimum* 笔记

想作为重启理论物理自学的第一套书。

<!--more-->

---

# Classical Mechanics

## The Nature of Classical Physics

Classical Physics 是指 Quantum Mechanics 出现之前的物理学，是一套原则和规则，一种基本逻辑，用来支配一切 quantum uncertainty 不重要的现象。这些一般规则被称为 classical mechanics。

Classical mechanics 的工作是预测未来：

> 我们可以把宇宙当前的状态视为过去的果和未来的因。假如存在一个智慧，它在某一时刻知道驱动这个自然运动的所有的力，以及组成这个自然的所有物体的位置，并且这个智慧足够强大，可以对这些数据进行分析，那么宇宙之中从最大的物体到最小的粒子都将包含在一个运动方程中；对这个智慧而言，没有什么是不确定的，未来只会像过去一样展现在眼前。
>
> ——Laplace

### System

如果在某个时刻得知这个系统的一切状态，和驱动这个系统变化的方程，就可以预测未来。这样的系统被称为 deterministic 的。如果可以用同样的方式推断过去，则说这个系统是 reversible 的。经典物理中，所有的系统都是 deterministic 和 reversible 的。

系统的定义：一些物体的集合。如果一个系统可以被孤立出来，不受其它任何系统外物体的作用，则称这个系统为 closed system。

一个系统能够占据的所有状态被抽象为一个数学上的集合，称为 state-space。

连续性假设：经典物理中，假设系统是连续变化的（continuous）。离散变化的系统为 stroboscopic。

### Dynamic Law

随时间变化的系统称为 dynamic system。该系统除了 state-space 之外还有 dynamic law，即通过当前状态得到下一状态的法则。

我们可以将 dynamic law 表示成数学公式的形式，描述这个系统的独立变量的个数是这个系统的 degrees of freedom。从该公式上也能确定此系统是否为 deterministic：由一种状态只能得到一种确定的下一时刻状态。

Reversible 的含义是，如果我们将时间反演，该系统仍然是 deterministic 的。即不可能由两种不同的状态得到一个相同的下一时刻状态。

### Diagram

我们也可以用图来表示（离散）物理系统：将 state 表示为点，dynamic law 表示为连接 state 之间的箭头。

Deterministic + reversible 在图中等价于：每个点都只有一个箭头指入和指出。

当图中出现环路时，则表明该系统从某个环路的任意状态出发，经过任意时间演化，都将保持在这个环路中。即某种「记忆」，在物理上则意味着存在某种 conservation law。

## Dynamics

经典物理中对时间的两条隐含假设：

- 均匀：1 秒的定义在任何时候都是一致的。
- 绝对：在不同参考系中的时间流速都是相同的。

为了研究连续系统，需要引入微积分，将运动写成微分方程的形式：
$$
\begin{equation}
\label{motion}
m \ddot{x} = F
\end{equation}
$$
最简单的连续系统是粒子系统，这里我们将粒子抽象为空间上的一个点。虽然在现实中没有「点」的概念，但当我们无须考虑这个物体的结构时，可以进行这样的「理想化」操作。

如果自然是粒子的集合（还原论），那么自然法则就等价于描述这些粒子运动的微分方程。根据方程 $\eqref{motion}$，我们还需要知道右端项 $F$。对于某个粒子来说，施加在其上的作用力只和其它所有粒子的位置相关。虽然宏观上看似有很多不同种类的力，但其实都来源于微观上原子、分子之间的相互作用。而这种力也被称为基本力，有四种：

- 引力
- 电磁力
- 强相互作用力
- 弱相互作用力

因此 $\eqref{motion}$ 右端项可以写作 $F = F(\{x\})$，其中 $\{x\}$ 为系统中所有粒子位置的集合。在三维空间中，对于 $N$ 个粒子，总共有 $3N$ 个这样的方程。由于方程为二阶微分方程，我们不仅需要知道粒子的初始位置，还有初始速度，因此这个系统 state-space 的维度为 $6N$。

### Momentum

除了采取位置和速度的描述方法，我们还可以用位置和动量对 state-space 进行描述，此时又被称为 phase space，方程 $\eqref{motion}$ 可被写作：
$$
\begin{gather*}
\dot{p} = F \\
\dot{r} = \frac{p}{m}
\end{gather*}
$$

Configuration space + Momentum space = Phase space

记粒子 $j$ 对 $i$ 的作用力为 $f_{ij}$，则有 $\dot{p}_i = \sum_j f_{ij}$，根据牛顿第三定律 $f_{ij} =-f_{ji}$，可以得到动量守恒定律：
$$
\dot{p} = \sum_i\sum_j f_{ij} = 0
$$

## Energy

和力类似，能量也有各种不同种类，但如果只考虑粒子系统，则只存在两种：

- 动能
- 势能

其中势能 $V(x)$ 在一维下的定义为：
$$
\begin{equation}
\label{potential}
F(x) = -\frac{d V(x)}{dx}
\end{equation}
$$
即力永远指向势能降低的方向。

动能 $T$ 的定义为：
$$
T = \frac{1}{2}mv^2
$$
总能量 $E$ 是动能和势能的总和：
$$
E = \frac{1}{2}mv^2 + V(x)
$$
对该等式两边求导可以得出能量守恒定律。

在高维情况下，为了使能量守恒定律成立，我们必须假设系统存在一个势能函数使得下式对所有维度成立：
$$
F_i(\{x\}) = -\frac{\partial V(\{x\})}{\partial x_i}
$$
这实际上是一个很强的假设，它也可以通过最小作用量原理推导出来，而最小作用量原理又可以从量子场论中得到。追根溯源总会有一个尽头，我们总得相信点什么。

## The Principle of Least Action

定义粒子系统的 Lagrangian 为：
$$
L(x, \dot{x}) = T - V
$$
系统的作用量为：
$$
\mathcal{A} = \int_{t_0}^{t_1} L(x, \dot{x}) dt
$$
则通过 $\delta \mathcal{A} = 0$ 可以得到欧拉-拉格朗日方程：
$$
\frac{d}{dt}\frac{\partial L}{\partial \dot{x}} - \frac{\partial L}{\partial x} = 0
$$
该方程与牛顿定律等价。

最小作用量原理比牛顿定律更加本质。只要给定了一个系统的 Lagrangian 和初始条件，我们就可以通过 E-L 方程预测该系统。另外一个优点是在坐标变换上，使用 E-L 方程比牛顿第二定律要简便很多。

### Generalized Coordinates

引入广义坐标 $q$ 和广义速度 $\dot{q}$，E-L 方程依旧成立：
$$
\frac{d}{dt}\frac{\partial L}{\partial \dot{q}} - \frac{\partial L}{\partial q} = 0
$$
当 $q$ 为空间直角坐标系时，$\partial L / \partial \dot{q}$ 为动量 $p$，因此对于广义坐标而言，$\partial L / \partial \dot{q}$ 即是广义动量。比如在极坐标下即可以推导出角动量的表达式，并且得到角动量守恒定律。

从 E-L 方程可以看出，当 $L$ 不包含某个广义坐标 $q_i$ 时，关于 $q_i$ 的广义动量守恒，这样的坐标被称为 cyclic coordinate。有时 $L$ 中没有包含 cyclic coordinate，但经过某个坐标变换后会出现。

## Symmetries and Conservation Laws

==P138==

