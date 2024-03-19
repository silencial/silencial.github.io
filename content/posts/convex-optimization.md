---
title: Convex Optimization
date: 2019-07-05
categories:
- Study
tags:
- UW
- Math
---

AA/EE/ME 578 Review

<!--more-->

---

# Convex Sets

## Examples

### Subspaces

$S \subseteq \mathbb{R}^{n}$ is a subspace if
$$
x, y \in S, \quad \lambda, \mu \in \mathbb{R} \implies \lambda x+\mu y \in S
$$
Geometrically: $x, y \in S \to$ plane through $0, x, y \subseteq S$.

### Affine Sets

$S \subseteq \mathbb{R}^n$ is affine if
$$
x, y \in S, \quad \lambda, \mu \in \mathbb{R}, \quad \lambda+\mu=1 \implies \lambda x+\mu y \in S
$$
Geometrically: $x, y \in S \to$ line through $x, y \subseteq S$.

### Convex Sets

$S \subseteq \mathbb{R}^n$ is a convex set if
$$
x, y \in S, \quad \lambda, \mu \ge 0, \quad \lambda+\mu=1 \implies \lambda x+\mu y \in S
$$
Geometrically: $x, y \in S \to$ segment $[x, y] \subseteq S$.

### Convex Cone

$S \subseteq \mathbb{R}^n$ is a cone if
$$
x \in S, \quad \lambda \ge 0 \implies \lambda x \in S
$$
$S \subseteq \mathbb{R}^n$ is a convex cone if
$$
x, y \in S, \quad \lambda, \mu \ge 0 \implies \lambda x+\mu y \in S
$$
Geometrically: $x, y \in S \to$ 'pie slice' between $x, y \subseteq S$.

### Combinations and Hulls

$y=\theta_{1} x_{1}+\dots+\theta_{k} x_{k}$ is a

- linear combination of $x_1, \dots, x_k$
- affine combination if $\sum \theta_i = 1$
- convex combination if $\sum \theta_i = 1,\ \theta_i \ge 0$
- conic combination if $\theta_i \ge 0$.

(linear, ...) hull of $S$ is the set of all (linear, ...) combinations from $S$.
$$
\DeclareMathOperator{\conv}{conv}
\conv(S)=\bigcap\{G \mid S \subseteq G, \ G \text { convex }\}
$$

convex hull $\conv(S)$ is the set of all convex combinations of points in $S$.

### Hyperplanes and Halfspaces

- Hyperplane: $\{x \mid a^{T} x=b,\ a \ne 0\}$
- Halfspace: $\{x \mid a^{T} x \le b,\ a \ne 0\}$

$a$ is the normal vector. Hyperplanes are affine and convex; halfspaces are convex.

### Euclidean Balls and Ellipsoids

Euclidean ball with center $x_c$ and radius $r$:
$$
\require{mathtools}
\DeclarePairedDelimiters\norm{\lVert}{\rVert}
\DeclarePairedDelimiters\abs{\lvert}{\rvert}
B(x_{c}, r)=\{x \mid \norm{x-x_{c}}_{2} \le r\}=\{x_{c}+r u \mid \norm{u}_{2} \le 1\}
$$
Ellipsoid:
$$
\{x \mid (x-x_{c})^{T} P^{-1}(x-x_{c}) \le 1\}
$$
with $P \in \mathbb{S}_{++}^{n}$. Other representation: $\{x_{c}+A u \mid \norm{u}_{2} \le 1\}$ with $A$ square and nonsingular.

### Norm Balls and Norm Cones

Norm: a function $\norm{\cdot}$ that satisfies

- $\norm{x} \ge 0 ; \ \norm{x}=0$ if and only if $x=0$
- $\norm{t x}=\abs{t}\norm{x}$ for $t \in \mathbb{R}$
- $\norm{x+y} \le\norm{x}+\norm{y}$

Norm ball with center $x_c$ and radius $r$: $\{x \mid \norm{x-x_{c}} \le r\}$

Norm cone: $\{(x, t) \mid \norm{x} \le t\}$

### Polyhedron

Solution set of finitely many linear inequalities and equalities
$$
A x \preceq b, \qquad C x=d
$$
($A \in \mathbb{R}^{m \times n},\ C \in \mathbb{R}^{p \times n},\ \preceq$ is component-wise inequality)

Polyhedron is intersection of finite number of halfspaces and hyperplanes.

### Positive Semidefinite Cone

- $\mathbb{S}_{+}^{n}=\{X \in \mathbb{S}^{n} \mid X \succeq 0\}$: PSD matrices. A convex cone.
- $\mathbb{S}_{++}^{n}=\{X \in \mathbb{S}^{n} \mid X\succ 0\}$: PD matrices.

## Operations That Preserve Convexity

To show $C$ is convex set

1. Definition
   $$
   x_{1}, x_{2} \in C, \quad 0 \le \theta \le 1 \implies \theta x_{1}+(1-\theta) x_{2} \in C
   $$

2. Show that $C$ is obtained from simple convex sets by operations that preserve convexity

   - intersection
   - affine function
   - perspective function
   - linear-fractional functions

### Intersection

The intersection of (any number of, even infinite) convex sets is convex.

### Affine Function

Suppose $f : \mathbb{R}^{n} \to \mathbb{R}^{m}$ is affine: $f(x)=A x+b$ with $A \in \mathbb{R}^{m \times n}, \ b \in \mathbb{R}^{m}$

- The image of a convex set under $f$ is convex
  $$
  S \subseteq \mathbb{R}^{n} \text { convex} \implies f(S)=\{f(x) \mid x \in S\} \text{ convex}
  $$

- The inverse image $f^{-1}(C)$ of a convex set under $f$ is convex
  $$
  C \subseteq \mathbb{R}^{m} \text{ convex} \implies f^{-1}(C)=\{x \in \mathbb{R}^{n} \mid f(x) \in C\} \text{ convex }
  $$

### Perspective and Linear-Fractional Function

Perspective function $P : \mathbb{R}^{n+1} \to \mathbb{R}^{n}$
$$
\DeclareMathOperator*{\dom}{dom}
P(x, t)=x / t, \quad \dom P=\{(x, t) \mid t>0\}
$$
images and inverse images of convex sets under perspective are convex.

Linear-fractional function $f : \mathbb{R}^{n} \to \mathbb{R}^{m}$
$$
f(x)=\frac{Ax+b}{c^{T} x+d}, \quad \text { dom } f=\{x \mid c^{T} x+d>0\}
$$
images and inverse images of convex sets under linear-fractional functions are convex.

## Generalized Inequalities

A convex cone $K \subseteq \mathbb{R}^{n}$ is a proper cone if

- $K$ is closed (contains its boundary)
- $K$ is solid (has nonempty interior)
- $K$ is pointed (contains no line)

Generalized inequality defined by a proper cone $K$:
$$
x \preceq_{K} y \iff y-x \in K, \qquad x\preceq_{K} y \iff y-x \in \operatorname{int} K
$$

## Hyperplane Theorem

### Separating Hyperplane

If $C$ and $D$ are disjoint convex sets, then there exists $a \ne 0, \ b$ s.t.
$$
a^{T} x \le b,\ \forall x \in C; \quad a^{T} x \ge b,\ \forall x \in D
$$
the hyperplane $\{x \mid a^{T} x=b\}$ separates $C$ and $D$. Strict separation requires additional assumptions (e.g. $C$ is closed, $D$ is a singleton).

### Supporting Hyperplane

Supporting hyperplane to set $C$ at boundary point $x_0$:
$$
\{x \mid a^{T} x=a^{T} x_{0}\}
$$
where $a \ne 0$ and $a^T x \le a^T x_0$ for all $x \in C$.

Supporting hyperplane theorem: If $C$ is convex, then there exists a supporting hyperplane at every boundary point of $C$.

## Dual Cones

Dual cone of a cone $K$: $K^*=\{y \mid y^{T} x \ge 0, \ \forall x \in K\}$

Examples:

- $K=\mathbb{R}_{+}^{n} : K^{*}=\mathbb{R}_{+}^{n}$
- $K=\mathbb{S}_{+}^{n} : K^{*}=\mathbb{S}_{+}^{n}$
- $K=\{(x, t) \mid\norm{x}_{2} \le t\} : K^{*}=\{(x, t) \mid\norm{x}_{2} \le t\}$
- $K=\{(x, t) \mid\norm{x}_{1} \le t\} : K^{*}=\{(x, t) \mid\norm{x}_{\infty} \le t\}$

# Convex Functions

$f: \mathbb{R}^{n} \to \mathbb{R}$ is convex if $\dom f$ is a convex set and
$$
f(\theta x+(1-\theta) y) \le \theta f(x)+(1-\theta) f(y)
$$
for all $x, y \in \dom f, \ 0\le \theta \le 1$

## Examples

### Convex

- affine: $a^T x + b$
- exponential: $e^{ax}$, for any $a \in \mathbb{R}$
- powers: $x^\alpha$ on $\mathbb{R}_{++}$, for $\alpha \ge 1$ or $a \le 0$
- powers of absolute value: $\abs{x}^p$ on $\mathbb{R}$, for $p \ge 1$
- negative entropy: $x\log x$ on $\mathbb{R}_{++}$
- norms: $\norm{x}_{p}=\left(\sum_{i=1}^{n}\left|x_{i}\right|^{p}\right)^{1 / p}$ for $p \ge 1$
- affine on matrices: $f(X)=\operatorname{tr}\left(A^{T} X\right)+b$
- spectral norm: $f(X) = \norm{X}_2 = \sigma_{\max}(X)$
- quadratic: $f(x)=(1 / 2) x^{T} P x+q^{T} x+r$ with $P \in \mathbb{S}^{n}$
- least-squares: $f(x) = \norm{Ax - b}_2^2$
- quadratic-over-linear: $f(x, y) = x^2/y$ with $y > 0$
- log-sum-exp: $f(x)=\log \sum_{k=1}^{n} e^{x_{k}}$

### Concave

- $f$ in concave if $-f$ is convex
- affine
- powers: $x^\alpha$ on $\mathbb{R}_{++}$, for $0\le \alpha \le 1$
- logarithm: $\log x$ on $\mathbb{R}_{++}$
- $\log\det X$ on $\mathbb{S}_{++}^n$
- geometric mean: $f(x)=\left(\prod_{k=1}^{n} x_{k}\right)^{1 / n}$ on $\mathbb{R}_{++}^n$

## Properties

### Restriction of a Convex Function to a Line

$f : \mathbb{R}^{n} \to \mathbb{R}$ is convex iff the function $g : \mathbb{R}^{n} \to \mathbb{R}$
$$
g(t)=f(x+t v), \quad \dom g=\{t \mid x+t v \in \dom f\}
$$

is convex (in $t$) for any $x \in \dom f, \ v\in \mathbb{R}^n$

### First-Order Convexity Condition

Differentiable $f$ with convex domain is convex iff
$$
f(y) \ge f(x)+\nabla f(x)^{T}(y-x),\quad \forall x, y \in \dom f
$$

### Second-Order Convexity Condition

Twice differentiable $f$ with convex domain is convex iff
$$
\nabla^{2} f(x) \succeq 0, \quad \forall x \in \dom f
$$

### Epigraph and Sublevel Set

$\alpha$-sublevel set of $f : \mathbb{R}^{n} \to \mathbb{R}$:
$$
C_{\alpha}=\{x \in \dom f \mid f(x) \le \alpha\}
$$
sublevel sets of convex functions are convex (converse is false)

Epigraph of $f : \mathbb{R}^{n} \to \mathbb{R}$:
$$
\operatorname{epi}f=\{(x, t) \in \mathbb{R}^{n+1} \mid x \in \dom f, \ f(x) \le t\}
$$
$f$ is convex iff $\operatorname{epi}f$ is a convex set.

### Jensen's Inequality

If $f$ is convex, then for $0 \le \theta \le 1$
$$
f(\theta x+(1-\theta) y) \le \theta f(x)+(1-\theta) f(y)
$$
can be extended to
$$
f(\mathbb{E}\ z) \le \mathbb{E}\ f(z)
$$
for any random variable $z$

## Operations That Preserve Convexity

To show $f$ is convex function:

1. Verify definition (often simplified by restricting to a line)
2. For twice differentiable functions, show $\nabla^2 f(x) \succeq 0$
3. Show that $f$ is obtained from simple convex functions by operations that preserve convexity

- Nonnegative multiple: $\alpha f$ is convex if $f$ is convex, $\alpha \ge 0$

- Sum: $f_1 + f_2$ is convex if $f_1, f_2$ convex

- Composition with affine function: $f(Ax + b)$ is convex if $f$ is convex

- Pointwise maximum: if $f_1, \dots, f_m$ are convex, then $f(x)=\max \{f_{1}(x), \dots, f_{m}(x)\}$ is convex

- Pointwise supremum: if $f(x, y)$ is convex in $x$ for each $y\in C$, then $g(x)=\sup _{y \in C} f(x, y)$ is convex.

- Composition of $g : \mathbb{R}^{n} \to \mathbb{R}$ and $h : \mathbb{R} \to \mathbb{R}$: $f(x) = h(g(x))$ is convex if
  - $g$ convex, $h$ convex, $\tilde{h}$ nondecreasing
  - $g$ concave, $h$ convex, $\tilde{h}$ nonincreasing

- Composition of $g : \mathbb{R}^{n} \to \mathbb{R}^k$ and $h : \mathbb{R}^k \to \mathbb{R}$: $f(x) = h(g(x)) = h(g_1(x), \dots, g_k(x))$ is convex if
  - $g_i$ convex, $h$ convex, $\tilde{h}$ nondecreasing in each argument
  - $g_i$ concave, $h$ convex, $\tilde{h}$ nonincreasing in each argument

- Minimization: if $f(x, y)$ is convex in $(x, y)$ and $C$ is convex set, then $g(x)=\inf_{y \in C} f(x, y)$ is convex

- Perspective if a function $f : \mathbb{R}^{n} \to \mathbb{R}$ is the function $g : \mathbb{R}^{n} \times \mathbb{R} \to \mathbb{R}$
   $$
   g(x, t)=t f(x / t), \quad \dom g=\{(x, t) \mid x / t \in \dom f, t>0\}
   $$
   $g$ is convex if $f$ is convex

## Conjugate Function

The conjugate of a function $f$: $f^{*}(y)=\sup_{x \in \dom f}\left(y^{T} x-f(x)\right)$ is always convex.

## Quasiconvex Function

$f : \mathbb{R}^{n} \to \mathbb{R}$ is quasiconvex if $\dom f$ is convex and the sublevel sets
$$
S_{\alpha}=\{x \in \dom f \mid f(x) \le \alpha\}
$$
are convex for all $\alpha$

$f$ is quasiconcave if $-f$ is quasiconvex.

- Modified Jensen inequality: $0 \le \theta \le 1 \implies f(\theta x+(1-\theta) y) \le \max \{f(x), f(y)\}$
- First-order condition: $f(y) \le f(x) \implies \nabla f(x)^{T}(y-x) \le 0$
- Sums of quasiconvex functions are not necessarily quasiconvex

## Log-Concave and Log-Convex Function

A positive function $f$ is log-concave if $\log f$ is concave:
$$
f(\theta x+(1-\theta) y) \ge f(x)^{\theta} f(y)^{1-\theta}, \quad \forall  0 \le \theta \le 1
$$
Many common probability densities are log-concave, e.g., normal distribution.

- Second-order condition: $f(x) \nabla^{2} f(x) \preceq \nabla f(x) \nabla f(x)^{T}$
- Product of log-concave functions is log-concave
- Sum of log-concave functions is not always log-concave
- Integration: if $f : \mathbb{R}^{n} \times \mathbb{R}^{m} \to \mathbb{R}$ is log-concave, then $g(x)=\int f(x, y) dy$ is log-concave

# Convex Optimization Problems

## Optimization Problem

$$
\begin{align*}
&\text{minimize} & &{f_{0}(x)} \\
&\text {subject to } & &{f_{i}(x) \le 0, \quad i=1, \ldots, m} \\
& & &{h_{i}(x)=0, \quad i=1, \ldots, p}
\end{align*}
$$

- $x \in \mathbb{R}^n$ is the optimization variable
- $f_0: \mathbb{R}^n \to \mathbb{R}$ is the objective or cost function
- $f_i: \mathbb{R}^n \to \mathbb{R},\ i = 1, \dots, m$ are the inequality constraint functions
- $h_i: \mathbb{R}^n \to \mathbb{R}$ are the equality constraint functions

Optimal value $p^{\star}=\inf \{f_{0}(x) \mid f_{i}(x) \le 0,\  i=1, \dots, m,\ h_{i}(x)=0, \ i=1, \dots, p\}$

- $p^\star = \infty$ if problem is infeasible
- $p^\star = -\infty$ if problem is unbounded below

## Feasibility Problem

$$
\begin{align*}
&\text{minimize} & &0 \\
&\text{subject to} & &f_{i}(x) \le 0, \quad i=1, \dots, m \\
& & &h_{i}(x)=0, \quad i=1, \dots, p
\end{align*}
$$

- $p^\star = 0$ if constraints are feasible; any feasible $x$ is optimal
- $p^\star = \infty$ if constraints are infeasible

## Convex Optimization

$$
\begin{align*}
&\text{minimize} & &f_{0}(x) \\
&\text {subject to} & &f_{i}(x) \le 0, \quad i=1, \dots, m \\
& & &Ax = b
\end{align*}
$$

- $f_0, \dots, f_m$ are convex; equality constraints are affine
- Feasible set of a convex optimization problem is convex
- Any locally optimal point of a convex problem is globally optimal

### Optimality Criterion

$x$ is optimal iff it is feasible and $\nabla f_{0}(x)^{T}(y-x) \ge 0$ for all feasible $y$. If nonzero, $\nabla f_0(x)$ defines a supporting hyperplane to feasible set $X$ at $x$.

- unconstrained problem:
  $$
  x \in \dom f_{0}, \quad \nabla f_{0}(x)=0
  $$

- equality constrained problem:
  $$
  \text{minimize } f_{0}(x) \quad \text{subject to } Ax=b
  $$
  x is optimal iff there exists a $\nu$ s.t.
  $$
  x \in \dom f_{0}, \quad A x=b, \quad \nabla f_{0}(x)+A^{T} \nu=0
  $$

- minimization over nonnegative orthant:
  $$
  \text{minimize } f_{0}(x) \quad \text{subject to } x \succeq 0
  $$
  x is optimal iff
  $$
  x \in \dom f_{0}, \quad x \succeq 0, \quad
  \begin{dcases}
  \nabla f_{0}(x)_{i} \ge 0 \quad x_{i}=0 \\
  {\nabla f_{0}(x)_{i}=0} \quad x_{i}>0
  \end{dcases}
  $$

### Equivalent Convex Problems

- eliminating equality constrains
  $$
  \begin{align*}
  &\text{minimize}_z & &f_{0}(F z+x_{0}) \\
  &\text{subject to} & &f_{i}(F z+x_{0}) \le 0, \quad i=1, \dots, m
  \end{align*}
  $$
  where $F$ and $x_0$ s.t. $A x=b \iff x=F z+x_{0}$ for some $z$

- introducing slack variables for linear inequalities
  $$
  \begin{align*}
  &\text{minimize} & &f_{0}(x) \\
  &\text{subject to} & &a_{i}^{T} x \le b_{i}, \quad i=1, \dots, m
  \end{align*}
  $$
  is equivalent to
  $$
  \begin{align*}
  &\text{minimize}_{x, s} & &f_{0}(x) \\
  &\text{subject to} & &a_{i}^{T} x+s_{i}=b_{i}, \quad i=1, \dots, m \\
  & & &s_{i} \ge 0, \quad i=1, \dots m
  \end{align*}
  $$

- epigraph form: standard form convex problem is equivalent to
  $$
  \begin{align*}
  &\text{minimize}_{x, t} & &t \\
  &\text{subject to} & &f_{0}(x)-t \le 0 \\
  & & &f_{i}(x) \le 0, \quad i=1, \dots, m \\
  & & &Ax=b
  \end{align*}
  $$

## Quasiconvex Optimization

$$
\begin{align*}
&\text{minimize} & &f_0(x) \\
&\text{subject to} & &f_{i}(x) \le 0, \quad i=1, \dots, m \\
& & &Ax=b
\end{align*}
$$

with $f_0: \mathbb{R}^n \to \mathbb{R}$ quasiconvex, $f_1, \dots, f_m$ convex. Can have locally optimal points that are not globally optimal

If $f_0$ is quasiconvex, there exists a family of functions $\varphi_t$ s.t.

- $\varphi_t(x)$ is convex in $x$ for fixed $t$
- $t$-sublevel set of $f_0$ is $0$-sublevel set of $\varphi_t$

For a fixed $t$, the quasiconvex optimization problem can be transferred to a convex feasibility problem in $x$. Bisection method can be used to find the optimal $t$.

## Linear Optimization

### Linear Programming

$$
\begin{align*}
&\text{minimize} & &c^{T} x+d \\
&\text{subject to} & &G x \preceq h \\
& & &A x=b
\end{align*}
$$

- feasible set is a polyhedron.

### Linear-Fractional Programming

$$
\begin{align*}
&\text{minimize} & &f_{0}(x) \\
&\text{subject to} & &G x \preceq h \\
& & &A x=b
\end{align*}
$$

where
$$
f_{0}(x)=\frac{c^{T} x+d}{e^{T} x+f}, \quad \dom f_{0}(x)=\{x \mid e^{T} x+f>0\}
$$

- a quasiconvex optimization problem

- equivalent to the LP
  $$
  \begin{align*}
  &\text{minimize} & &c^{T} y+d z \\
  &\text{subject to} & &G y \preceq h z \\
  & & &A y=b z \\
  & & &e^{T} y+f z=1 \\
  & & &z \ge 0
  \end{align*}
  $$

## Quadratic Optimization

### Quadratic Programming

$$
\begin{align*}
&\text{minimize} & &(1 / 2) x^{T} P x+q^{T} x+r \\
&\text{subject to} & &G x \preceq h \\
& & &A x=b
\end{align*}
$$

- $P \in \mathbb{S}_+^n$, so objective is convex quadratic
- minimize a convex quadratic function over a polyhedron

### Quadratically Constrained Quadratic Programming

$$
\begin{align*}
&\text{minimize} & &(1 / 2) x^{T} P_{0} x+q_{0}^{T} x+r_{0} \\
&\text{subject to} & &(1 / 2) x^{T} P_{i} x+q_{i}^{T} x+r_{i} \le 0, \quad i=1, \dots, m \\
& & &A x=b
\end{align*}
$$

- $P_i \in \mathbb{S}_+^n$, objective and constraints are convex quadratic
- if $P_1, \dots, P_m \in \mathbb{S}_{++}^n$, feasible region is intersection of $m$ ellipsoids and an affine set

### Second-Order Cone Programming

$$
\begin{align*}
&\text{minimize} & &f^{T} x \\
&\text{subject to} & &\norm*{A_{i} x+b_{i}}_{2} \le c_{i}^{T} x+d_{i}, \quad i=1, \dots, m \\
& & &F x=g
\end{align*}
$$

- $A_{i} \in \mathbb{R}^{n_{i} \times n}, F \in \mathbb{R}^{p \times n}$
- inequalities are called second-order cone constraints
- for $n_i = 0$, reduces to an LP; if $c_i = 0$, reduces to a QCQP

## Geometric Programming

- Monomial function
  $$
  f(x)=c x_{1}^{a_{1}} x_{2}^{a_{2}} \cdots x_{n}^{a_{n}}, \quad \dom f=\mathbb{R}_{++}^{n}
  $$
  with $c>0,\ \alpha_i \in \mathbb{R}$

- Posynomial function: sum of monomials
  $$
  f(x)=\sum_{k=1}^{K} c_{k} x_{1}^{a_{1 k}} x_{2}^{a_{2 k}} \cdots x_{n}^{a_{n k}}, \quad \dom f=\mathbb{R}_{++}^{n}
  $$

- Geometric program
  $$
  \begin{align*}
  &\text{minimize} & &f_{0}(x) \\
  &\text{subject to} & &f_{i}(x) \le 1, \quad i=1, \dots, m \\
  & & &h_{i}(x)=1, \quad i=1, \dots, p
  \end{align*}
  $$
  with $f_i$ posynomial, $h_i$ monomial

Geometric program in convex form:
$$
\begin{align*}
&\text{minimize} & &\log \left(\sum_{k=1}^{K} \exp \left(a_{0 k}^{T} y+b_{0 k}\right)\right) \\
&\text{subject to} & &\log \left(\sum_{k=1}^{K} \exp \left(a_{i k}^{T} y+b_{i k}\right)\right) \le 0, \quad i=1, \dots, m \\
& & &G y+d=0
\end{align*}
$$

## Generalized Inequality Constraints

$$
\begin{align*}
&\text{minimize} & &f_{0}(x) \\
&\text{subject to} & &f_{i}(x) \preceq_{K_{i}} 0, \quad i=1, \dots, m \\
& & &A x=b
\end{align*}
$$

- $f_0: \mathbb{R}^n \to \mathbb{R}$ convex; $f_i: \mathbb{R}^n \to \mathbb{R}^{k_i}$ $K_i$-convex w.r.t. proper cone $K_i$
- same properties as standard convex problem (convex feasible set, local optimum is global, etc.)

Conic form problem:
$$
\begin{align*}
&\text{minimize} & &c^{T} x \\
&\text{subject to} & &F x+g \preceq_{K} 0 \\
& & &A x=b
\end{align*}
$$
extends LP ($K = \mathbb{R}_+^m$) to non-polyhedral cones

Semidefinite programming:
$$
\begin{align*}
&\text{minimize} & &c^{T} x \\
&\text{subject to} & &x_{1} F_{1}+x_{2} F_{2}+\cdots+x_{n} F_{n}+G \preceq 0 \\
& & &A x=b
\end{align*}
$$
with $F_i, G \in \mathbb{S}^k$

### Minimum and Minimal Elements

$\preceq_K$ is not in general a linear ordering: we can have $x \npreceq_K y$ and $y \npreceq_K x$

- $x \in S$ is the minimum element of $S$ w.r.t. $\preceq_K$ if $y \in S \implies x \preceq_K y$
- $x \in S$ is the minimal element of $S$ w.r.t. $\preceq_K$ if $y \in S, \ y \preceq_{K} x \implies y=x$

## Vector Optimization

General vector optimization problem:
$$
\begin{align*}
&\text{minimize}_K & &f_{0}(x) \\
&\text{subject to} & &f_{i}(x) \le 0, \quad i=1, \dots, m \\
& & &h_{i}(x) \le 0, \quad i=1, \dots, p
\end{align*}
$$
vector objective $f_{0} : \mathbb{R}^{n} \to \mathbb{R}^{q}$, minimized w.r.t. proper cone $K \in \mathbb{R}^{q}$

Convex vector optimization problem:
$$
\begin{align*}
&\text{minimize}_K & &f_{0}(x) \\
&\text{subject to} & &f_{i}(x) \le 0, \quad i=1, \dots, m \\
& & &A x=b
\end{align*}
$$

with $f_0$ $K$-convex, $f_1,\dots, f_m$ convex

### Optimal and Pareto Optimal Points

Set of achievable objective values $\mathcal{O}=\{f_{0}(x) \mid x \text{ feasible}\}$

- feasible $x$ is optimal if $f_0(x)$ is a minimum value of $\mathcal{O}$
- feasible $x$ is Pareto optimal if $f_0(x)$ is a minimal value of $\mathcal{O}$

# Duality

## Lagrange Dual Function

From the standard form optimization problem we define the Lagrangian $L : \mathbb{R}^{n} \times \mathbb{R}^{m} \times \mathbb{R}^{p} \to \mathbb{R}$
$$
L(x, \lambda, \nu)=f_{0}(x)+\sum_{i=1}^{m} \lambda_{i} f_{i}(x)+\sum_{i=1}^{p} \nu_{i} h_{i}(x)
$$

- weighted sum of objective and constraint functions
- $\lambda_i$ is Lagrange multiplier associated with $f_i(x) \le 0$
- $\nu_i$ is Lagrange multiplier associated with $h_i(x) = 0$

Lagrange dual function $g : \mathbb{R}^{m} \times \mathbb{R}^{p} \to \mathbb{R}$
$$
\begin{split}
g(\lambda, \nu) &=\inf_{x \in \mathcal{D}} L(x, \lambda, \nu) \\
&=\inf_{x \in \mathcal{D}}\left(f_{0}(x)+\sum_{i=1}^{m} \lambda_{i} f_{i}(x)+\sum_{i=1}^{p} \nu_{i} h_{i}(x)\right)
\end{split}
$$
$g$ is concave.

**lower bound property**: if $\lambda \succeq 0$, then $g(\lambda, \nu) \le p^{\star}$

## Lagrange Dual Problem

$$
\begin{align*}
&\text{maximize} & &g(\lambda, \nu) \\
&\text{subject to} & &\lambda \succeq 0
\end{align*}
$$

- find best lower bound on $p^\star$, obtained from Lagrange dual function
- a convex optimization problem; optimal value denoted $d^\star$
- $\lambda, \nu$ are dual feasible if $\lambda \succeq 0,\ (\lambda, \nu) \in \dom g$
- often simplified by making implicit constraint $(\lambda, \nu) \in \dom g$ explicit

## Optimality Conditions

- Weak duality $d^\star \le p^\star$ always holds, can be expressed as
  $$
  \sup_{\lambda \succeq 0} \inf_{x} L(x, \lambda) \le \inf_x \sup_{\lambda \succeq 0} L(x, \lambda)
  $$

- Strong duality $d^\star = p^\star$ usually holds for convex problems. It means that $x^\star$ and $\lambda^\star$ from a saddle-point for the Lagrangian.

### Slater's Constraint Qualification

Strong duality holds for a convex problem if it is strictly feasible, i.e.,
$$
\exists x \in \operatorname{int} \mathcal{D} : \quad f_{i}(x)<0, \ i=1, \dots, m, \quad A x=b
$$

### KKT Conditions

If strong duality holds and $x^\star, \lambda^\star, \nu^\star$ are optimal, then they must satisfy:

1. primal constraints: $f_{i}(x^\star) \le 0,\ h_{i}(x^\star)=0$

2. dual constraints: $\lambda^\star \succeq 0$

3. complementary slackness: $\lambda_i^\star f_i(x^\star) = 0$

4. gradient of Lagrangian with respect to $x$ vanishes:
   $$
   \nabla f_{0}(x^\star)+\sum_{i=1}^{m} \lambda_{i}^\star \nabla f_{i}(x^\star)+\sum_{i=1}^{p} \nu_{i}^\star \nabla h_{i}(x^\star)=0
   $$

If $\tilde{x}, \tilde{\lambda}, \tilde{\nu}$ satisfy KKT for a convex problem, then they are optimal

# Applications

## Geometric Problems

### Minimum Volume Ellipsoid Around a Set

Minimum volume ellipsoid $\mathcal{E}$ of a set $C$.

- parametrize $\mathcal{E}$ as $\mathcal{E}=\{v \mid \norm{A v+b}_{2} \le 1\}$, assume $A \in \mathbb{S}_{++}^n$

- $\operatorname{vol} \mathcal{E}$ is proportional to $\det A^{-1}$, can compute $\mathcal{E}$ by solving
  $$
  \begin{align*}
  &\text{minimize}_{A,b} & &\log \det A^{-1}\\
  &\text{subject to} & &\sup_{v \in C}\norm{A v+b}_{2} \le 1
  \end{align*}
  $$

### Maximum Volume Inscribed Ellipsoid

Maximum volume ellipsoid $\mathcal{E}$ inside a convex set $C \subseteq \mathbb{R}^n$

- parametrize $\mathcal{E}$ as $\mathcal{E}=\{B u+d \mid \norm{u}_{2} \le 1\}$, assume $B \in \mathbb{S}_{++}^n$

- $\operatorname{vol} \mathcal{E}$ is proportional to $\det B$, can compute $\mathcal{E}$ by solving
  $$
  \begin{align*}
  &\text{maximize} & &\log\det B \\
  &\text{subject to} & &\sup_{\norm{u}_{2} \le 1} I_{C}(B u+d) \le 0
  \end{align*}
  $$
  where $I_C(x) = 0$ for $x\in C$ and $I_C(x) = \infty$ for $x\notin C$

### Linear Discrimination

Separate two sets of points $\{x_1, \dots, x_N\},\ \{y_1, \dots, y_M\}$ by a hyperplane:
$$
a^{T} x_{i}+b>0, \ i=1, \dots, N, \quad a^{T} y_{i}+b<0, \ i=1, \dots, M
$$
homogeneous in $a, b$, hence equivalent to
$$
a^{T} x_{i}+b \ge 1, \ i=1, \dots, N, \quad a^{T} y_{i}+b \le-1, \ i=1, \dots, M
$$
To separate two sets of points by maximum margin
$$
\begin{align*}
&\text{minimize} & &(1 / 2)\norm{a}_{2} \\
&\text{subject to} & &a^{T} x_{i}+b \ge 1, \quad i=1, \dots, N \\
& & &a^{T} y_{i}+b \le-1, \quad i=1, \dots, M
\end{align*}
$$

### Support Vector Classifier

$$
\begin{align*}
&\text{minimize} & &\norm{a}_{2}+\gamma\left(\mathbf{1}^{T} u+\mathbf{1}^{T} v\right) \\
&\text{subject to} & &a^{T} x_{i}+b \ge 1-u_{i}, \quad i=1, \dots, N \\
& & &a^{T} y_{i}+b \le -1+v_{i}, \quad i=1, \dots, M \\
& & &u \succeq 0, \quad v \succeq 0
\end{align*}
$$

produces point on trade-off curve between inverse of margin $2/\norm{a}_2$ and classification error, measured by total slack $\mathbf{1}^{T} u+\mathbf{1}^{T} v$

## Data Fitting

### Norm Approximation

$$
\text{minimize } \norm{A x-b}
$$

where $A \in \mathbb{R}^{m \times n}$ with $m\ge n$

Linear measurement model: $y = Ax + v$, $y$ are measurements, $x$ is unknown, $v$ is measurement error. Given $y=b$, best guess of $x$ is $x^\star$

### Least-Norm Problems

$$
\begin{align*}
&\text{minimize} & &\norm{x} \\
&\text{subject to} & &Ax=b
\end{align*}
$$

where $A \in \mathbb{R}^{m \times n}$ with $m\le n$

### Scalarized Problem

$$
\text{minimize } \norm{A x-b}+\gamma\norm{x}
$$

tradeoff between error and norm

## Statistical Estimation

### Maximum Likelihood Estimation

$$
\text{maximize }(\text{over } x ) \quad \log p_{x}(y)
$$
With linear measurement model with IID noise: $y_{i}=a_{i}^{T} x+v_{i}, \ i=1, \dots, m$, the estimation problem becomes
$$
\text{maximize } l(x) = \sum_{i=1}^{m} \log p(y_{i}-a_{i}^{T} x)
$$
where $y$ is observed value, $p$ is the PDF of the measurement noise $v$

- Gaussian noise $\mathcal{N}(0, \sigma^{2}) : p(z)=(2 \pi \sigma^{2})^{-1 / 2} e^{-z^{2} /(2 \sigma^{2})}$
  $$
  l(x)=-\frac{m}{2} \log (2 \pi \sigma^{2}) - \frac{1}{2 \sigma^{2}} \sum_{i=1}^{m}(a_{i}^{T} x-y_{i})^{2}
  $$

- Laplacian noise $p(z)=(1 /(2 a)) e^{-\abs{z} / a}$
  $$
  l(x)=-m \log (2 a)-\frac{1}{a} \sum_{i=1}^{m}\abs*{a_{i}^{T} x-y_{i}}
  $$

- Uniform noise on $[-a, a]$
  $$
  l(x)=\begin{dcases}
  -m \log (2 a) & \abs*{a_{i}^{T} x-y_{i}} \le a, \ i=1, \ldots, m \\
  -\infty & \text{otherwise}
  \end{dcases}
  $$

### Logistic Regression

Random variable $y\in \{0,1\}$ with distribution
$$
p=\operatorname{prob}(y=1)=\frac{\exp (a^{T} u+b)}{1+\exp (a^{T} u+b)}
$$
log-likelihood function (for $y_1 = \dots = y_k = 1, \ y_{k+1} = \dots = y_m = 0$):
$$
\begin{split}
l(a, b) &=\log \left(\prod_{i=1}^{k} \frac{\exp (a^{T} u_{i}+b)}{1+\exp (a^{T} u_{i}+b)} \prod_{i=k+1}^{m} \frac{1}{1+\exp (a^{T} u_{i}+b)}\right) \\
&=\sum_{i=1}^{k}(a^{T} u_{i}+b)-\sum_{i=1}^{m} \log \left(1+\exp (a^{T} u_{i}+b)\right)
\end{split}
$$
concave in $a, b$
