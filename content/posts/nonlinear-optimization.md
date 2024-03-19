---
title: Nonlinear Optimization
date: 2021-05-13
categories:
- Study
tags:
- JHU
- Math
---


EN553.762 Review. Based on [Numerical Optimization Book](https://www.csie.ntu.edu.tw/~r97002/temp/num_optimization.pdf)

<!--more-->

---

# Constrained Optimization

$$
\begin{equation}\label{con-optim}
\min_{x \in \mathbb{R}^n} f(x) \qquad \text{s.t. }
\begin{cases}
c_i(x) = 0, &i \in \mathcal{E} \\
c_i(x) \ge 0, &i \in \mathcal{I}
\end{cases}
\end{equation}
$$

where $f$ and $c_i$ are all smooth, real-valued functions, and $\mathcal{E}$ and $\mathcal{I}$ are two finite sets of indices.

**Definition**:  **feasible set** $\Omega$
$$
\Omega=\left\{x \mid c_{i}(x)=0, \ \ i \in \mathcal{E} ; \quad c_{i}(x) \ge 0, \ \ i \in \mathcal{I}\right\}
$$
**Definition**: Given $x\in\Omega$, the **active set** $\mathcal{A}(x)$ is
$$
\mathcal{A}(x)=\mathcal{E} \cup\left\{i \in \mathcal{I} \mid c_{i}(x)=0\right\}
$$

## Tangent Cone and Constraint Qualifications

**Definition**: The vector $d$ is said to be a **tangent vector** to $\Omega$ at a point $x$ if there are a feasible sequence $\{z_k\}$ approaching $x$ and a sequence of positive scalars $\{t_k\}$ approaching $0$ s.t.
$$
\lim _{k \to \infty} \frac{z_{k}-x}{t_{k}}=d
$$
The set of all tangents to $\Omega$ at $x^*$ is called the **tangent cone** and is denoted by $T_\Omega(x^*)$

**Definition**: Given $x\in\Omega$ and $\mathcal{A}(x)$, the set of **linearized feasible directions** $\mathcal{F}(x)$ is
$$
\mathcal{F}(x)=\left\{
d \biggm\vert \begin{aligned}
&d^{T} \nabla c_{i}(x)=0, & &\forall i \in \mathcal{E} \\
&d^{T} \nabla c_{i}(x) \ge 0, & &\forall i \in \mathcal{A}(x) \cap \mathcal{I}
\end{aligned}
\right\}
$$
**Definition**: Given $x$ and $\mathcal{A}(x)$, the **linear independence constraint qualification (LICQ)** holds if the set of active constraint gradients $\{\nabla c_i(x), i\in\mathcal{A}(x)\}$ is linearly independent.

## First-Order Optimality Conditions

Define the Lagrangian function
$$
\mathcal{L}(x, \lambda)=f(x)-\sum_{i \in \mathcal{E} \cup \mathcal{I}} \lambda_{i} c_{i}(x)
$$
and the first-order necessary conditions (a.k.a. the KKT conditions) are
$$
\begin{align*}
\nabla_{x} \mathcal{L}\left(x^{*}, \lambda^{*}\right) &=0 \\
c_{i}\left(x^{*}\right) &=0, & &\forall i \in \mathcal{E} \\
c_{i}\left(x^{*}\right) & \ge 0, & &\forall i \in \mathcal{I} \\
\lambda_{i}^{*} & \ge 0, & &\forall i \in \mathcal{I} \\
\lambda_{i}^{*} c_{i}\left(x^{*}\right) &=0, & &\forall i \in \mathcal{E} \cup \mathcal{I}
\end{align*}
$$

### Proof

**Lemma**: Let $x^*$ be a feasible point. The following two statements are true

1. $T_{\Omega}\left(x^{*}\right) \subset \mathcal{F}\left(x^{*}\right)$
2. If the LICQ conditions is satisfied at $x^*$, then $\mathcal{F}\left(x^{*}\right) = T_{\Omega}\left(x^{*}\right)$

**Farkas' lemma**: Consider the cone $K=\{By + Cw \mid y\ge 0\}$, given any $g\in \mathbb{R}^n$, we have either

1. $g\in K$
2. $\exists d \in \mathbb{R}^n$ s.t. $g^T d < 0, \ \ B^T d\ge 0, \ \ C^Td = 0$

## Second-Order Conditions

**Definition**: the **critical cone** $\mathcal{C}(x^*, \lambda^*)$ is
$$
\mathcal{C}\left(x^{*}, \lambda^{*}\right)=\left\{w \in \mathcal{F}\left(x^{*}\right) \mid \nabla c_{i}\left(x^{*}\right)^{T} w=0, \text { all } i \in \mathcal{A}\left(x^{*}\right) \cap \mathcal{I} \text { with } \lambda_{i}^{*}>0\right\}
$$
Second-order necessary conditions: if $x^*$ is a local solution and the LICQ condition is satisfied, then
$$
w^{T} \nabla_{x x}^{2} \mathcal{L}\left(x^{*}, \lambda^{*}\right) w \ge 0, \quad \forall w \in \mathcal{C}\left(x^{*}, \lambda^{*}\right)
$$
Second-order sufficient conditions:
$$
w^{T} \nabla_{x x}^{2} \mathcal{L}\left(x^{*}, \lambda^{*}\right) w > 0, \quad \forall w \in \mathcal{C}\left(x^{*}, \lambda^{*}\right),\ w\ne 0
$$

## Geometric Viewpoint

**Definition**: The **normal cone** to the set $\Omega$ at the point $x\in \Omega$ is
$$
N_{\Omega}(x)=\left\{v \mid v^{T} w \le 0, \ \forall w \in T_{\Omega}(x)\right\}
$$
**Theorem**: Suppose $x^*$ is a local minimizer of the following problem:
$$
\min f(x) \quad \text{s.t. } x\in\Omega
$$
then $-\nabla f(x^*) \in N_\Omega(x^*)$

**Lemma**: Suppose the LICQ holds at $x^*$, then $N_\Omega(x^*) = -N$, where $N$ is defined as
$$
N=\left\{\sum_{i \in \mathcal{A}\left(x^{*}\right)} \lambda_{i} \nabla c_{i}\left(x^{*}\right), \quad \lambda_{i} \ge 0 \text { for } i \in \mathcal{A}\left(x^{*}\right) \cap \mathcal{I}\right\}
$$

## Duality

Consider the problem with only inequality constraints:
$$
\begin{gather*}
\min_{x\in\mathbb{R}^n} f(x) \quad \text{s.t. } c(x) \ge 0 \\
c(x) \triangleq (c_1(x), \dots, c_m(x))^T
\end{gather*}
$$
**Definition**: The **dual problem** to the above **primal problem** is
$$
\begin{gather*}
\max_{\lambda\in\mathbb{R}^n} q(\lambda) \quad \text{s.t. } \lambda \ge 0 \\
q(\lambda) \triangleq \inf_x \mathcal{L}(x, \lambda)
\end{gather*}
$$
**Theorem**: $q$ is concave and its domain $D=\{\lambda \mid q(\lambda) > -\infty \}$ is convex

**Theorem**: (Weak Duality) For any feasible $x$ of the primal problem and any $\lambda \ge 0$, we have $q(\lambda) \le f(x)$

**Theorem**: Suppose $x$ is a solution of the primal problem and that $f$ and $-c_i$ are convex and differentiable. Then any $\lambda$ for which $(x,\lambda)$ satisfies the KKT conditions is a solution of the dual problem

### Linear Programming

Linear programming and its dual:
$$
\begin{align*}
&\min_{x} c^T x & &\text{s.t } Ax - b \ge 0 \\
&\max_{\lambda} b^T \lambda & &\text{s.t. } A^T\lambda = c,\ \lambda\ge 0
\end{align*}
$$
Another form:
$$
\begin{align*}
&\max_{x} c^T x & &\text{s.t. } A x - b\le 0, \ x\ge 0 \\
&\min_{\lambda} b^T\lambda & &\text{s.t. } A^T \lambda - c\ge 0, \ \lambda\ge 0 \\
\end{align*}
$$
**Theorem**:

1. If either primal or dual has a (finite) solution, then so does the other, and the objective values are equal
2. If either primal or dual is unbounded, the the other problem is infeasible

### Quadratic Programming

Primal problem:
$$
\begin{align*}
&\min_x & &\frac{1}{2}x^TGx + c^Tx \\
&\text{ s.t. } & &Ax-b\ge 0
\end{align*}
$$
Dual problem:
$$
\begin{align*}
&\max_{(\lambda, x)} &&\frac{1}{2} x^{T} G x+c^{T} x-\lambda^{T}(A x-b) \\
&\text { s.t. } &&G x+c-A^{T} \lambda=0, \quad \lambda \ge 0
\end{align*}
$$

# Penalty and Augmented Lagrangian Methods

## Quadratic Penalty Method

Consider optimization problem with only equality constraints
$$
\begin{equation}\label{equ-con}
\min_x f(x) \quad \text{s.t. } c_i(x) = 0, \ (i\in \mathcal{E})
\end{equation}
$$
The quadratic penalty function $Q(x; \mu)$ for this formulation is
$$
\require{mathtools}
\DeclarePairedDelimiters\norm{\lVert}{\rVert}
\begin{equation}\label{qua}
Q(x; \mu) = f(x) + \frac{\mu}{2}\sum_{i\in\mathcal{E}} c_i^2(x)
\end{equation}
$$
**Algorithm**:

1. Choose $\mu_0$, starting point $x_0^s$ and nonnegative sequence $\{\tau_k\}$ with $\tau_k \to 0$
2. For $k=0,1,2,\dots$
   1. Find $x_k \gets \min Q(\cdot;\mu_k)$, starting at $x_k^s$ and terminating when $\norm{\nabla_x Q(x_k;\mu_k)} \le \tau_k$
   2. Return $x_k$ if final convergence test is satisfied
   3. Otherwise choose new $\mu_{k+1} > \mu_k$, e.g. $\mu_{k+1} = 2\mu_k$ and new starting point $x_{k+1}^s = x_k$

**Theorem**: Suppose $x_k$ is the exact global minimizer of $Q(x;\mu_k)$ defined by $\eqref{qua}$, and that $\mu_k \to \infty$. Then every limit point $x^*$ of the sequence $\{x_k\}$ is a global solution of problem $\eqref{equ-con}$

**Theorem**: Suppose in the general algorithmic framework we have $\tau_k\to 0$ and $\mu_k\to \infty$. If $x^*$ is a limit point of $\{x_k\}$ then

1. If $x^*$ is infeasible, then $x^*$ is a stationary point of $\norm{c(x)}^2$
2. If $x^*$ is feasible and $\nabla c_i(x^*)$ are linearly independent, then $x^*$ is a KKT point for $\eqref{equ-con}$ with some $\lambda^*$ s.t. $\lim_{k\to\infty} -\mu_k c_i(x_k) = \lambda_i^*$

## Nonsmooth Penalty Function

For inequality constraints, we can use the $\ell_1$ penalty function defined by
$$
\phi_{1}(x ; \mu)=f(x)+\mu \sum_{i \in \mathcal{E}}\left|c_{i}(x)\right|+\mu \sum_{i \in \mathcal{I}}\left[c_{i}(x)\right]^{-}
$$
**Theorem**: Suppose $x^*$ is a strict local solution of $\eqref{con-optim}$ at which the KKT conditions are satisfied, with Lagrange multipliers $\lambda_i^*$. Then $x^*$ is a local minimizer of $\phi_1(x;\mu)$ for all $\mu > \mu^*$, where
$$
\mu^* = \norm{\lambda^*}_\infty = \max_{i\in \mathcal{E} \cup \mathcal{I}} |\lambda_i^*|
$$
If, in addition, the second-order sufficient conditions hold and $\mu>\mu^*$, then $x^*$ is a strict local minimizer of $\phi_1(x;\mu)$

## Augmented Lagrangian Method

In the quadratic penalty method, the approximate minimizer $x_k$ of $Q(x;\mu_k)$ might not satisfy the feasibility conditions $c_i(x) = 0$. The are perturbed so that
$$
c_i(x_k) \approx -\lambda_i^*/\mu_k, \quad \forall i \in \mathcal{E}
$$
In order to make the minimizer more nearly satisfy the equality constraints, we defined the augmented Lagrangian function which is a combination of the Lagrangian and the quadratic penalty function
$$
\mathcal{L}_{A}(x, \lambda ; \mu) \triangleq f(x)-\sum_{i \in \mathcal{E}} \lambda_{i} c_{i}(x)+\frac{\mu}{2} \sum_{i \in \mathcal{E}} c_{i}^{2}(x)
$$
During the $k$-th iteration, $\lambda^k$ and $\mu_k$ are fixed and the minimization is performed only on $x$
$$
\nabla_x \mathcal{L}_A = \nabla f(x_k) - \sum_{i\in\mathcal{E}}[\lambda_i^k - \mu_k c_i(x_k)] \nabla c_i(x_k)
$$
After finding the approximate minimizer $x_k$ we can update $\lambda_i^{k+1} = \lambda_i^k - \mu_k c_i(x_k)$

**Algorithm**:

1. Choose $\mu_0 > 0$, tolerance $\tau_0 > 0$, starting point $x_0^s$ and $\lambda^0$
2. For $k=0,1,2,\dots$
   1. Find $x_k \gets \min \mathcal{L}_A(\cdot, \lambda^k;\mu_k)$, starting at $x_k^s$ and terminating when $\norm{\nabla_x \mathcal{L}_A(x_k, \lambda^k;\mu_k)} \le \tau_k$
   2. Return $x_k$ if final convergence test is satisfied
   3. Otherwise update Lagrange multipliers $\lambda^{k+1}=\lambda^k - \mu_k c(x_k)$, choose new penalty parameter $\mu_{k+1} \ge \mu_k$, new tolerance $\tau_{k+1}$ and new starting point $x_{k+1}^s = x_k$

**Theorem**: Suppose $x^*$ is a local solution of $\eqref{equ-con}$ at which the LICQ is satisfied, and the second-order sufficient conditions are satisfied for $\lambda^*$. Then there is a threshold value $\bar{\mu}$ s.t. for all $\mu\ge \bar{\mu}$, $x^*$ is a strict local minimizer of $\mathcal{L}_A(x, \lambda^*; \mu)$

## Practical Augmented Lagrangian Method

Given inequality constraints, we can convert it to equality constraints and bound constraints by introducing slack variables $s_i$
$$
c_i(x) - s_i = 0, \quad s_i \ge 0, \quad \forall i\in\mathcal{I}
$$
By incorporating $s_i$ into $x$ and redefining $c_i$ accordingly, $\eqref{con-optim}$ can be written as
$$
\min_{x \in \mathbb{R}^n} f(x) \quad \text{s.t. }
\begin{cases}
c_i(x) = 0 \\
l\le x\le u
\end{cases}
$$
And the subproblem during each iteration becomes
$$
\begin{equation}\label{sub-lag}
\min_x \mathcal{L}_A(x,\lambda;\mu)\quad \text{s.t. } l\le x\le u
\end{equation}
$$
An efficient technique for solving the nonlinear program with bound constraints is the gradient projection method. The KKT conditions for $\eqref{sub-lag}$ are
$$
x-P\left(x-\nabla_{x} \mathcal{L}_{A}(x, \lambda ; \mu), l, u\right)=0
$$
where $P(g,l,u)$ is the projection of the vector $g$ onto the rectangular box $[l, u]$ defined as
$$
P(g, l, u)_{i}=\begin{cases}
l_{i} & \text {if } g_{i} \le l_{i} \\
g_{i} & \text {if } g_{i} \in\left(l_{i}, u_{i}\right) \\
u_{i} & \text {if } g_{i} \ge u_{i}
\end{cases}
$$

# Quadratic Programming

$$
\begin{equation}\label{qp}
\begin{aligned}
&\min_{x} & &\frac{1}{2} x^{T} G x+x^{T} c \\
&\text{ s.t. } & &a_{i}^{T} x=b_{i}, \quad i \in \mathcal{E} \\
& & &a_{i}^{T} x \ge b_{i}, \quad i \in \mathcal{I}
\end{aligned}
\end{equation}
$$

If $G\in\mathbb{S}_+$, then it is a convex QP, and the difficulty in solving this problem is similar to a linear program.

## Equality-Constraints

$$
\begin{align*}
&\min_{x} & &\frac{1}{2} x^{T} G x+x^{T} c \\
&\text{ s.t. } & &Ax = b
\end{align*}
$$

Where $A$ is the $m\times n$ Jacobian of constrains ($m\le n$). Assume $A$ has full row rank.

The KKT conditions are
$$
\begin{equation}
\begin{bmatrix}
G & -A^T \\
A & 0
\end{bmatrix}
\begin{bmatrix}
x^* \\ \lambda^*
\end{bmatrix} =
\begin{bmatrix}
-c \\ b
\end{bmatrix}
\label{qp-kkt}
\end{equation}
$$
By expressing $x^* = x + p$ where $x$ is some estimate of the solution, then the KKT conditions become
$$
\begin{bmatrix}
G & A^T \\
A & 0
\end{bmatrix}
\begin{bmatrix}
-p \\ \lambda^*
\end{bmatrix} =
\begin{bmatrix}
c + Gx \\ Ax - b
\end{bmatrix}
$$
The matrix in this equation is called the KKT matrix.

**Theorem**: Let $Z$ be the $n\times(n-m)$ matrix whose columns are a basis for the null space of $A$. If $A$ has full row rank and the reduced-Hessian matrix $Z^TGZ$ is P.D., then the KKT matrix is nonsingular. Further the solution of $\eqref{qp-kkt}$ is the unique global solution of $\eqref{qp}$

## Inequality-Constraints

For problem $\eqref{qp}$, we can define the active set as
$$
\mathcal{A}(x^*) =\{i\in\mathcal{E}\cup\mathcal{I} \mid a_i^T x^* = b_i \}
$$
and the KKT conditions are
$$
\begin{equation}\label{qp-kkt-general}
\begin{aligned}
G x^{*}+c-\sum_{i \in \mathcal{A}\left(x^{*}\right)} \lambda_{i}^{*} a_{i} &=0 \\
a_{i}^{T} x^{*} &=b_{i} & & \forall i \in \mathcal{A}\left(x^{*}\right) \\
a_{i}^{T} x^{*} & \ge b_{i} & & \forall i \in \mathcal{I} \setminus \mathcal{A}\left(x^{*}\right) \\
\lambda_{i}^{*} & \ge 0 & & \forall i \in \mathcal{I} \cap \mathcal{A}\left(x^{*}\right)
\end{aligned}
\end{equation}
$$
**Theorem**: If $x^*$ satisfies the KKT conditions for some $\lambda_i^*,\ \ i\in\mathcal{A}(x^*)$, and $G$ is P.S.D., then $x^*$ is a global solution of $\eqref{qp}$

## Active-Set Methods

Assume $G$ is P.S.D. Let $q(x) = \frac{1}{2} x^{T} G x+x^{T} c$. Define the working set $\mathcal{W}$ to be some of the inequality constraints and all the equality constraints.

Given $x_k$ and the working set $\mathcal{W}_k$, first check whether $x_k$ minimizes $q$ in the subspace defined by the working set. If not, compute a step $p$ by solving the subproblem:
$$
\begin{align*}
&\min_p && q(x_k + p) \\
&\text{ s.t. } && a_i^Tp = 0, \quad i\in\mathcal{W}_k
\end{align*}
$$
which is equivalent to
$$
\begin{equation}\label{qp-sub}
\begin{aligned}
&\min_p && \frac{1}{2}p^TGp + g_k^T p \\
&\text{ s.t. } && a_i^Tp = 0, \quad i\in\mathcal{W}_k
\end{aligned}
\end{equation}
$$
where $g_k = G x_k + c$

If the optimal $p_k$ is nonzero, we need to compute a largest step length $\alpha_k \in [0,1]$ s.t. $x_{k+1}=x_k + \alpha_k p_k$ satisfies all constraints:
$$
\begin{equation}\label{alpha}
\alpha_k = \min(1, \min_{i\notin\mathcal{W}_k,\ a_i^Tp_k < 0} \frac{b_i-a_i^T x_k}{a_i^T p_k})
\end{equation}
$$
If $\alpha_k<1$, that means the step along $p_k$ was blocked by some constraint not in $\mathcal{W}_k$, so we construct $\mathcal{W}_{k+1}$ by adding one of the blocking constraints to $\mathcal{W}_k$.

Continue the iterations until the subproblem has the solution $p=0$. Then from $\eqref{qp-kkt}$ we have
$$
\begin{equation}\label{multiplier}
\sum_{i \in \hat{\mathcal{W}}} a_{i} \hat{\lambda}_{i}=g=G \hat{x}+c
\end{equation}
$$
for some $\hat{\lambda}_i,\ i\in\hat{\mathcal{W}}$. Define the multipliers not in the working set to be zero. Then the first three KKT conditions $\eqref{qp-kkt-general}$ all satisfied.

If all the multipliers with indices $i\in \hat{\mathcal{W}} \cap \mathcal{I}$ are nonnegative , then $\hat{x}$ is the global solution of $\eqref{qp}$. If one or more of the multipliers is negative, then we must remove the corresponding indices from the working set and continue the iteration.

**Theorem**: Suppose the point $\hat{x}$ satisfies the KKT conditions for the equality-constrained subproblem with working set $\hat{\mathcal{W}}$. Suppose, too, that the $a_i, \ i\in\hat{\mathcal{W}}$ are linearly independent and there is an index $j\in\hat{\mathcal{W}}$ s.t. $\hat{\lambda}_j < 0$. Let $p$ be the solution obtained by dropping the constraint $j$ from the original problem:
$$
\begin{equation}\label{drop}
\begin{aligned}
&\min_p && \frac{1}{2}p^TGp + g_k^T p \\
&\text{ s.t. } && a_i^Tp = 0, \quad i\in\mathcal{W}_k,\ i\ne j
\end{aligned}
\end{equation}
$$
Then $p$ is a feasible direction for constraint $j$, that is, $a_j^Tp \ge 0$. Moreover, if $p$ satisfies second-order sufficient conditions for $\eqref{drop}$, then $a_j^T p > 0$, and $p$ is a descent direction for $q$.

**Theorem**: Suppose that the solution $p_k$ of $\eqref{qp-sub}$ is nonzero and satisfies the second-order sufficient conditions for that problem. Then $q$ is strictly decreasing along the direction of $p_k$

**Algorithm**:

1. Compute a feasible starting point $x_0$ and set $\mathcal{W}_0$ to be subset of the active constraints at $x_0$
2. For $k=0,1,2,\dots$
   1. Solve $\eqref{qp-sub}$ to find $p_k$
   2. If $p_k = 0$
      1. Compute Lagrange multipliers $\hat{\lambda}_i$ that satisfy $\eqref{multiplier}$
      2. Return $x_k$ if $\hat{\lambda}_i \ge 0$ for all $i\in \hat{\mathcal{W}} \cap \mathcal{I}$
      3. Otherwise find $j=\arg\min_{j\in \hat{\mathcal{W}} \cap \mathcal{I}} \hat{\lambda}_j$ and update $\mathcal{W}_{k+1} = \mathcal{W}_k \setminus \{j\}$, $x_{k+1} \gets x_k$
   3. Else
      1. Compute $\alpha_k$ from $\eqref{alpha}$ and update $x_{k+1} \gets x_k + \alpha_k p_k$
      2. Add blocking constraints to $\mathcal{W}_k$ to obtain $\mathcal{W}_{k+1}$

### Initial Feasible Point

We can determined the initial feasible point $\tilde{x}$ by solving the linear program:
$$
\begin{align*}
& \min _{(x, z)} && e^{T} z \\
&\text { s.t. } && a_{i}^{T} x+\gamma_{i} z_{i} =b_{i}, & &i \in \mathcal{E} \\
& && a_{i}^{T} x+\gamma_{i} z_{i}  \ge b_{i}, & &i \in \mathcal{I} \\
& && z  \ge 0
\end{align*}
$$
where $e=(1,1,\dots, 1)^T$, $\gamma_{i}=-\operatorname{sign}(a_i^T \tilde{x}-b_i)$ for $i\in\mathcal{E}$, and $\gamma_i =1$ for $i\in\mathcal{I}$. A feasible point for this problem is
$$
x = \tilde{x} \qquad z_i = \begin{cases}
|a_i^T \tilde{x} - b_i|, &i\in\mathcal{E} \\
\max(b_i - a_i^T\tilde{x}, 0), &i\in\mathcal{I}
\end{cases}
$$
An alternative approach is a penalty (or **big M**) method which introduces a scalar artificial variable $\eta$ into $\eqref{qp}$ to measure the constraint violation, and solve the problem:
$$
\begin{align*}
&\min_{x} & \frac{1}{2} x^{T} G x+x^{T} c +M\eta \\
&\text { s.t. } & a_{i}^{T} x - b_{i} \le \eta, && i \in \mathcal{E} \\
& & -(a_{i}^{T} x - b_{i}) \le \eta, && i \in \mathcal{E} \\
& & b_i - a_i^T x \le \eta, && i\in \mathcal{I} \\
& & 0 \le \eta,
\end{align*}
$$
for some large positive value of $M$. It can be shown that whenever there exist feasible points for the original problem $\eqref{qp}$, then for all $M$ sufficiently large, the solution of the penalty method will have $\eta=0$, with an $x$ component that is the solution for $\eqref{qp}$.

# Sequential Quadratic Programming

## Local SQP Method

Consider the equality constrained problem:
$$
\begin{align*}
&\min && f(x) \\
&\text{ s.t. } && c(x) = 0
\end{align*}
$$
where $f:\mathbb{R}^n \to \mathbb{R}$ and $c:\mathbb{R}^n\to\mathbb{R}^m$ are smooth. The idea behind SQP is to model this problem at the current iterate $x_k$ by a QP subproblem, then use the minimizer of this subproblem to define a new iterate $x_{k+1}$. The simplest way is to apply Newton's method to the KKT conditions.

Let $A(x)$ denote the Jacobian matrix of the constraints:
$$
A(x)^T = [\nabla c_1(x), \nabla c_2(x), \dots, \nabla c_m(x)]
$$
The KKT conditions are
$$
F(x,\lambda) = \begin{bmatrix}
\nabla f(x) - A(x)^T \lambda \\
c(x)
\end{bmatrix} = 0
$$
This can be solved by Newton's method:
$$
\begin{bmatrix}
x_{k+1} \\ \lambda_{k+1}
\end{bmatrix} =
\begin{bmatrix}
x_k \\ \lambda_k
\end{bmatrix} +
\begin{bmatrix}
p_k \\ p_\lambda
\end{bmatrix}
$$
where $p_k$ and $p_\lambda$ solve the Newton-KKT system:
$$
\begin{equation}\label{newton-kkt}
\begin{bmatrix}
\nabla_{xx}^2 \mathcal{L}_k & -A_k^T \\
A_k & 0
\end{bmatrix}
\begin{bmatrix}
p_k \\ p_\lambda
\end{bmatrix} =
\begin{bmatrix}
-\nabla f_k + A_k^T \lambda_k \\
-c_k
\end{bmatrix}
\end{equation}
$$
The Newton iteration is well defined when the KKT matrix is nonsingular.

### SQP Framework

An alternative way to view the iteration is to consider a quadratic problem at the iterate $(x_k, \lambda_k)$:
$$
\begin{equation}\label{sqp-sub}
\begin{aligned}
&\min_p && \nabla f_k^T p + \frac{1}{2}p^T \nabla_{xx}^2 \mathcal{L}_k p \\
&\text{ s.t. } && A_kp + c_k = 0
\end{aligned}
\end{equation}
$$
If the KKT matrix is nonsingular, this problem has a unique solution $(p_k, l_k)$ that satisfies the KKT conditions:
$$
\begin{equation}\label{sqp-kkt}
\begin{bmatrix}
\nabla_{xx}^2 \mathcal{L}_k & -A_k^T \\
A_k & 0
\end{bmatrix}
\begin{bmatrix}
p_k \\ l_k
\end{bmatrix} =
\begin{bmatrix}
-\nabla f_k \\
-c_k
\end{bmatrix}
\end{equation}
$$
Compared with $\eqref{newton-kkt}$ we can see that $l_k = p_\lambda + \lambda_k = \lambda_{k+1}$.

**Algorithm**:

1. Choose an initial pair $(x_0, \lambda_0)$, set $k=0$
2. Repeat until convergence
   1. Evaluate $\nabla f_k, \nabla_{xx}^2 \mathcal{L}_k, c_k, A_k$
   2. Solve $\eqref{sqp-sub}$ for $p_k$ and $l_k$
   3. Update $x_{k+1} \gets x_k + p_k$ and $\lambda_{k+1} \gets l_k$

### Inequality Constraints

The SQP framework can be extended to general nonlinear programming problem $\eqref{con-optim}$ as
$$
\begin{equation}\label{qp-sub-ineq}
\begin{aligned}
&\min_p && \nabla f_k^T p + \frac{1}{2}p^T \nabla_{xx}^2 \mathcal{L}_k p \\
&\text{ s.t. } && \nabla c_i(x_k)^T p + c_i(x_k) = 0, \quad i\in\mathcal{E} \\
& && \nabla c_i(x_k)^T p + c_i(x_k) \ge 0, \quad i\in\mathcal{I}
\end{aligned}
\end{equation}
$$
**Theorem**: Suppose $(x^*,\lambda^*)$ is a local solution of $\eqref{con-optim}$, and LICQ, strict complementarity condition, second-order sufficient conditions hold. Then if $(x_k, \lambda_k)$ is sufficiently close to $(x^*, \lambda^*)$, there is a local solution of the subproblem $\eqref{qp-sub-ineq}$ whose active set $\mathcal{A}_k$ is the same as the active set $\mathcal{A}(x^*)$ of $\eqref{con-optim}$.

## Algorithmic Development

### Handling Inconsistent Linearizations

The linearizations of the nonlinear constraints $\eqref{qp-sub-ineq}$ may give an infeasible subproblem. To overcome this difficulty, we can reformulate $\eqref{con-optim}$ as the $\ell_1$ penalty problem:
$$
\begin{equation}\label{l1-penalty}
\begin{aligned}
&\min_{x, v, w, t} && f(x)+\mu \sum_{i \in \mathcal{E}}\left(v_{i}+w_{i}\right)+\mu \sum_{i \in \mathcal{I}} t_{i} \\
&\text { s.t. } && c_{i}(x)=v_{i}-w_{i}, \quad i \in \mathcal{E} \\
& &&c_{i}(x) \ge-t_{i}, \qquad\;\,\, i \in \mathcal{I} \\
& &&v,\ w,\ t \ge 0
\end{aligned}
\end{equation}
$$
for some positive choice of the penalty parameter $\mu$. The quadratic subproblem associated with it is always feasible. When $\mu$ is sufficiently large, then the solution $x^*$ coincides with the original problem.

### Merit Functions

A merit function can be used to decide whether a trial step should be accepted. In line search methods, it controls the size of the step; in trust-region methods it determines whether the step is accepted or rejected and whether the radius should be adjusted.

Consider the $\ell_1$ merit function and only equality constraints:
$$
\phi_{1}(x ; \mu)=f(x)+\mu\norm{c(x)}_1
$$
In a line search method, a step $\alpha_k p_k$ will be accepted if the following condition holds:
$$
\begin{equation}\label{direction}
\phi_{1}(x_{k}+\alpha_{k} p_{k} ; \mu_{k}) \le \phi_{1}(x_{k}, \mu_{k})+\eta \alpha_{k} D(\phi_1(x_{k} ; \mu) ; p_{k}), \quad \eta \in(0,1)
\end{equation}
$$
where $D(\phi_1(x_{k} ; \mu) ; p_{k})$ is the directional derivative of $\phi_1$ in the direction $p_k$.

**Theorem**: Let $p_k$ and $\lambda_{k+1}$ be generated by the SQP iteration $\eqref{sqp-kkt}$. Then we have

- $D(\phi_1(x_{k} ; \mu) ; p_{k}) = \nabla f_k^T p_k -\mu \norm{c_k}_1$
- $D(\phi_1(x_{k} ; \mu) ; p_{k}) \le -p_k^T\nabla_{xx}^2 \mathcal{L}_k p_k - (\mu - \norm{\lambda_{k+1}}_\infty) \norm{c_k}_1$

## Line Search SQP Method

**Algorithm**:

1. Choose $\eta\in(0,0.5)$, $\tau\in(0, 1)$ and an initial pair $(x_0, \lambda_0)$
2. Evaluate $f_0, \nabla f_0, c_0, A_0, \nabla_{xx}^2\mathcal{L}_0$
3. Repeat until convergence
   1. Solve $\eqref{qp-sub-ineq}$ for $p_k$ and $\hat{\lambda}$
   2. Set $p_\lambda \gets \hat{\lambda} - \lambda_k$
   3. Choose $\mu_k$ large enough so that $p_k$ is the descent direction for $\phi_1$
   4. Set $\alpha_k \gets 1$ and Repeat $\alpha_k \gets \tau_a \alpha_k$ for some $\tau_a\in(0,\tau]$ until $\eqref{direction}$ holds
   5. Set $x_{k+1}\gets x_k + \alpha_k p_k$ and $\lambda_{k+1}\gets \lambda_k + \alpha_k p_\lambda$
   6. Evaluate $f_{k+1}, \nabla f_{k+1}, c_{k+1}, A_{k+1}, \nabla_{xx}^2\mathcal{L}_{k+1}$

## Trust-Region SQP Methods

Trust-region methods can control the quality of the steps even when the Hessian $\nabla_{xx}^2 \mathcal{L}_k$ is not positive definite, and they provide a mechanism for enforcing global convergence.

Add a trust-region constraint to the subproblem $\eqref{qp-sub-ineq}$ to get
$$
\begin{align*}
&\min_p && \nabla f_k^T p + \frac{1}{2}p^T \nabla_{xx}^2 \mathcal{L}_k p \\
&\text{ s.t. } && \nabla c_i(x_k)^T p + c_i(x_k) = 0, \quad i\in\mathcal{E} \\
& && \nabla c_i(x_k)^T p + c_i(x_k) \ge 0, \quad i\in\mathcal{I} \\
& && \norm{p} \le \Delta_k
\end{align*}
$$
After adding the constraint, the problem may not have a solution. However, the idea is not to satisfy the other constraints at every step, but to improve the feasibility of these constraints.

### Relaxation Method

Consider the equality constraints only. At iteration $x_k$, we solve the subproblem:
$$
\begin{align*}
&\min_p && \nabla f_k^T p + \frac{1}{2}p^T \nabla_{xx}^2 \mathcal{L}_k p \\
&\text{ s.t. } && A_kp + c_k = r_k \\
& && \norm{p}_2 \le \Delta_k
\end{align*}
$$
The choice of the relaxation vector $r_k$ impacts the efficiency of the method. To do so, first solve the auxiliary problem:
$$
\begin{align*}
&\min_v && \norm{A_k v + c_k}_2^2 \\
&\text{ s.t. } && \norm{v}_2 \le 0.8\Delta_k
\end{align*}
$$
and set $r_k = A_k v + c_k$. Now we can compute $p_k$ and update $x_{k+1} = x_k+p_k$ and $\lambda_{k+1} = (A_kA_k^T)^{-1}A_k \nabla f_k$
