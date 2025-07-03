---
title: Convex Optimization
date: 2019-07-05
lastmod: 2025-06-30
categories:
- Study
tags:
- UW
- Math
---

AA/EE/ME 578 Review

<!--more-->

---

# Convex Set

## Examples

### Subspace

A set $S \subseteq \mathbb{R}^{n}$ is a subspace if

$$
\forall \mathbf{x}, \mathbf{y} \in S, \quad  \forall \lambda, \mu \in \mathbb{R}, \quad \lambda \mathbf{x}+\mu \mathbf{y} \in S
$$

Geometrically: $\forall \mathbf{x}, \mathbf{y} \in S$, the plane passing through $\mathbf{0}, \mathbf{x}, \mathbf{y}$ is a subset of $S$.

### Affine Set

A set $S \subseteq \mathbb{R}^n$ is affine if

$$
\forall \mathbf{x}, \mathbf{y} \in S, \quad \forall \lambda, \mu \in \mathbb{R}, \quad \lambda+\mu=1 \implies \lambda \mathbf{x}+\mu \mathbf{y} \in S
$$

Geometrically: $\forall \mathbf{x}, \mathbf{y} \in S$, the line passing through $\mathbf{x}, \mathbf{y}$ is a subset of $S$.

### Convex Set

A set $S \subseteq \mathbb{R}^n$ is a convex set if

$$
\forall \mathbf{x}, \mathbf{y} \in S, \quad \forall \lambda, \mu \ge 0, \quad \lambda+\mu=1 \implies \lambda \mathbf{x}+\mu \mathbf{y} \in S
$$

Geometrically: $\forall \mathbf{x}, \mathbf{y} \in S$, the line segment connecting $\mathbf{x}, \mathbf{y}$ is a subset of $S$.

### Convex Cone

A set $S \subseteq \mathbb{R}^n$ is a cone if

$$
\forall \mathbf{x} \in S, \quad \lambda \ge 0 \implies \lambda \mathbf{x} \in S
$$

A set $S \subseteq \mathbb{R}^n$ is a convex cone if

$$
\forall \mathbf{x}, \mathbf{y} \in S, \quad \lambda, \mu \ge 0 \implies \lambda \mathbf{x}+\mu \mathbf{y} \in S
$$

Geometrically: $\forall \mathbf{x}, \mathbf{y} \in S$, the conic sector (or "pie slice") between $\mathbf{x}, \mathbf{y}$ is a subset of $S$.

### Combination and Hull

A points $\mathbf{y}=\theta_{1} \mathbf{x}_{1}+\dots+\theta_{k} \mathbf{x}_{k}$ is a

- Linear combination of $\mathbf{x}_1, \dots, \mathbf{x}_k$
- Affine combination if $\sum \theta_i = 1$
- Convex combination if $\sum \theta_i = 1$ and $\theta_i \ge 0$
- Conic combination if $\theta_i \ge 0$.

The (linear, affine, convex, or conic) hull of a set $S$ is the set of all possible (linear, affine, convex, or conic) combinations of points from $S$. The convex hull, $\operatorname{conv}(S)$, can also be defined as the intersection of all convex sets containing $S$.

$$
\operatorname{conv}(S)=\bigcap\{G \mid S \subseteq G, \ G \text { is convex}\}
$$

convex hull $\operatorname{conv}(S)$ is the set of all convex combinations of points in $S$.

### Hyperplane and Halfspace

- Hyperplane: $\{\mathbf{x} \mid \mathbf{a}^{T} \mathbf{x}=b,\ \mathbf{x} \ne \mathbf{0}\}$
- Halfspace: $\{\mathbf{x} \mid \mathbf{a}^{T} \mathbf{x} \le b,\ \mathbf{x} \ne \mathbf{0}\}$

The vector $\mathbf{a}$ is the normal to the hyperplane. Hyperplanes are both affine and convex; halfspaces are convex.

### Euclidean Ball and Ellipsoid

A Euclidean ball with center $\mathbf{x}_c$ and radius $r$:

$$
\DeclarePairedDelimiters\norm{\lVert}{\rVert}
\DeclarePairedDelimiters\abs{\lvert}{\rvert}
B(\mathbf{x}_{c}, r)=\{\mathbf{x} \mid \norm{\mathbf{x}-\mathbf{x}_{c}}_{2} \le r\}=\{\mathbf{x}_{c}+r \mathbf{u} \mid \norm{\mathbf{u}}_{2} \le 1\}
$$

An ellipsoid:

$$
\{\mathbf{x} \mid (\mathbf{x}-\mathbf{x}_{c})^{T} P^{-1}(\mathbf{x}-\mathbf{x}_{c}) \le 1\}
$$

with $P \in \mathbb{S}_{++}^{n}$ (the set of symmetric positive definite $n\times n$ matrices). An alternative representation is $\{\mathbf{x}_c+A\mathbf{u} \mid \norm{\mathbf{u}}_{2} \le 1\}$ with $A$ being a square, nonsingular matrix.

### Norm Ball and Norm Cone

A function $\norm{\cdot}$ is a norm if it satisfies

- $\norm{\mathbf{x}} \ge 0$; $\norm{\mathbf{x}}=0$ if and only if $\mathbf{x}=\mathbf{0}$.
- $\norm{t\mathbf{x}}=\abs{t}\norm{\mathbf{x}}$ for any $t \in \mathbb{R}$.
- $\norm{\mathbf{x}+\mathbf{y}} \le \norm{\mathbf{x}}+\norm{\mathbf{y}}$ (triangle inequality).

A norm ball with center $\mathbf{x}_c$ and radius $r$: $\{\mathbf{x} \mid \norm{\mathbf{x}-\mathbf{x}_c} \le r\}$.

A norm cone: $\{(\mathbf{x}, t) \mid \norm{\mathbf{x}} \le t\}$.

### Polyhedron

A polyhedron is the solution set of a finite number of linear inequalities and equalities:

$$
A\mathbf{x} \preceq \mathbf{b}, \quad C\mathbf{x}=\mathbf{d}
$$

Here, $A \in \mathbb{R}^{m \times n}$, $C \in \mathbb{R}^{p \times n}$, and $\preceq$ denotes component-wise inequality.

A polyhedron is the intersection of a finite number of halfspaces and hyperplanes.

### Positive Semidefinite Cone

- $\mathbb{S}_{+}^{n}=\{X \in \mathbb{S}^{n} \mid X \succeq 0\}$ is the set of PSD matrices. It is a convex cone.
- $\mathbb{S}_{++}^{n}=\{X \in \mathbb{S}^{n} \mid X\succ 0\}$ is the set of PD matrices.

## Operations That Preserve Convexity

To show a set $C$ is convex, one can either:

1. Use the definition

    $$
    \forall \mathbf{x}_{1}, \mathbf{x}_{2} \in C, \quad 0 \le \theta \le 1 \implies \theta\mathbf{x}_{1}+(1-\theta)\mathbf{x}_{2} \in C
    $$

2. Show that $C$ is formed by applying convexity-preserving operations to simpler convex sets. These operations include:
    - Intersection
    - Affine transformation
    - Perspective transformation
    - Linear fractional transformation

### Intersection

The intersection of any collection of convex sets (finite or infinite) is a convex set.

### Affine Function

Suppose $f : \mathbb{R}^{n} \to \mathbb{R}^{m}$ is an affine function, defined as $f(\mathbf{x})=A\mathbf{x}+\mathbf{b}$ for $A \in \mathbb{R}^{m \times n}$ and $\mathbf{b} \in \mathbb{R}^{m}$.

- The image of a convex set under $f$ is convex:

    $$
    \forall S \subseteq \mathbb{R}^{n}, S \text { is convex} \implies f(S)=\{f(\mathbf{x}) \mid \mathbf{x} \in S\} \text{ is convex}
    $$

- The inverse image of a convex set under $f$, denoted $f^{-1}(C)$, is convex:

    $$
    \forall C \subseteq \mathbb{R}^{m}, C \text{ is convex} \implies f^{-1}(C)=\{\mathbf{x} \in \mathbb{R}^{n} \mid f(\mathbf{x}) \in C\} \text{ is convex}
    $$

### Perspective and Linear-Fractional Function

The perspective function $P : \mathbb{R}^{n+1} \to \mathbb{R}^{n}$ is defined as

$$
\DeclareMathOperator*{\dom}{dom}
P(\mathbf{x}, t)=\mathbf{x}/t, \quad \dom P=\{(\mathbf{x}, t) \mid t>0\}
$$

Images and inverse images of convex sets under the perspective function are convex.

The linear-fractional function $f : \mathbb{R}^{n} \to \mathbb{R}^{m}$ is defined as

$$
f(\mathbf{x})=\frac{A\mathbf{x}+\mathbf{b}}{\mathbf{c}^{T}\mathbf{x}+d}, \quad \dom f=\{\mathbf{x} \mid \mathbf{c}^{T}\mathbf{x}+d>0\}
$$

Images and inverse images of convex sets under the linear-fractional function are convex.

## Generalized Inequalities

A convex cone $K \subseteq \mathbb{R}^{n}$ is called a proper cone if it is:

- Closed: contains its boundary.
- Solid: has a nonempty interior.
- Pointed: contains no lines (i.e., if $\mathbf{x} \in K$ and $-\mathbf{x} \in K$, then $\mathbf{x}=\mathbf{0}$).

A proper cone $K$ defines a generalized inequality:

$$
\begin{gather*}
\mathbf{x} \preceq_{K} \mathbf{y} \iff \mathbf{y}-\mathbf{x} \in K \\
\mathbf{x} \prec_{K} \mathbf{y} \iff \mathbf{y}-\mathbf{x} \in \operatorname{int} K
\end{gather*}
$$

### Minimum and Minimal Element

With generalized inequalities, the ordering is not linear, meaning we can have two elements $\mathbf{x}, \mathbf{y}$ for which neither $\mathbf{x} \preceq_K \mathbf{y}$ nor $\mathbf{y} \preceq_K \mathbf{x}$ holds. This leads to two distinct concepts of optimality:

- An element $\mathbf{x} \in S$ is the **minimum element** of $S$ with respect to $\preceq_K$ if for every $\mathbf{y} \in S$, we have $\mathbf{x} \preceq_K \mathbf{y}$. A minimum element, if it exists, is unique.
- An element $\mathbf{x} \in S$ is a **minimal element** of $S$ with respect to $\preceq_K$ if $\mathbf{y} \in S$ and $\mathbf{y} \preceq_{K} \mathbf{x}$ implies $\mathbf{y}=\mathbf{x}$. There can be many minimal elements.

## Hyperplane Theorem

### Separating Hyperplane Theorem

If $C$ and $D$ are disjoint convex sets, then there exists a vector $\mathbf{a} \ne \mathbf{0}$ and a scalar $b$ such that:

$$
\forall \mathbf{x} \in C , \mathbf{a}^{T}\mathbf{x} \le b \quad \text{and} \quad \forall \mathbf{x} \in D, \mathbf{a}^{T}\mathbf{x} \ge b
$$

The hyperplane $\{\mathbf{x} \mid \mathbf{a}^{T}\mathbf{x}=b\}$ separates $C$ and $D$. Strict separation ($\mathbf{a}^{T}\mathbf{x} < b < \mathbf{a}^{T}\mathbf{y}$) requires additional assumptions (e.g., $C$ is closed, $D$ is a singleton).

### Supporting Hyperplane Theorem

A supporting hyperplane to a set $C$ at a boundary point $\mathbf{x}_0$ is defined as:

$$
\{\mathbf{x} \mid \mathbf{a}^{T}\mathbf{x}=\mathbf{a}^{T}\mathbf{x}_{0}\}
$$

where $\mathbf{a} \ne \mathbf{0}$ and $\mathbf{a}^T\mathbf{x} \le \mathbf{a}^T\mathbf{x}_0$ for all $\mathbf{x} \in C$.

The theorem states that if $C$ is a convex set, a supporting hyperplane exists at every point on its boundary.

## Dual Cone

The dual cone of a cone $K$ is defined as:

$$
K^*=\{\mathbf{y} \mid \mathbf{y}^{T}\mathbf{x} \ge 0, \ \forall \mathbf{x} \in K\}
$$

Examples:

- If $K=\mathbb{R}_{+}^{n}$, then $K^{*}=\mathbb{R}_{+}^{n}$
- If $K=\mathbb{S}_{+}^{n}$, then $K^{*}=\mathbb{S}_{+}^{n}$
- If $K=\{(\mathbf{x}, t) \mid \norm{\mathbf{x}}_{2} \le t\}$, then $K^{*}=\{(\mathbf{x}, t) \mid \norm{\mathbf{x}}_{2} \le t\}$ (the second-order cone is self-dual).
- If $K=\{(\mathbf{x}, t) \mid \norm{\mathbf{x}}_{1} \le t\}$, then its dual is $K^{*}=\{(\mathbf{x}, t) \mid \norm{\mathbf{x}}_{\infty} \le t\}$.

# Convex Function

A function $f: \mathbb{R}^{n} \to \mathbb{R}$ is convex if its domain, $\dom f$, is a convex set and for all $\mathbf{x}, \mathbf{y} \in \dom f$ and $0 \le \theta \le 1$:

$$
f(\theta\mathbf{x}+(1-\theta)\mathbf{y}) \le \theta f(\mathbf{x})+(1-\theta)f(\mathbf{y})
$$

## Examples

### Convex

- Affine: $f(\mathbf{x})=\mathbf{a}^T\mathbf{x} + b$
- Exponential: $e^{ax}$, for any $a \in \mathbb{R}$
- Powers: $f(x)=x^\alpha$ on $\mathbb{R}_{++}$ for $\alpha \ge 1$ or $a \le 0$
- Powers of absolute value: $f(x)=\abs{x}^p$ on $\mathbb{R}$ for $p \ge 1$
- Negative entropy: $f(x)=x\log x$ on $\mathbb{R}_{++}$
- Norms: $f(\mathbf{x})=\norm{\mathbf{x}}_{p}=\left(\sum_{i=1}^{n}\left|x_{i}\right|^{p}\right)^{1 / p}$ for $p \ge 1$
- Affine on matrices: $f(X)=\operatorname{tr}\left(A^{T} X\right)+b$
- Spectral norm: $f(X) = \norm{X}_2 = \sigma_{\max}(X)$
- Quadratic: $f(\mathbf{x})=(1/2)\mathbf{x}^{T}P\mathbf{x}+\mathbf{q}^{T}\mathbf{x}+r$ with $P \in \mathbb{S}_{+}^{n}$
- Least squares: $f(\mathbf{x}) = \norm{A\mathbf{x} - \mathbf{b}}_2^2$
- Quadratic over linear: $f(x, y) = x^2/y$ on the domain $y > 0$
- LogSumExp: $f(\mathbf{x})=\log \sum_{k=1}^{n} e^{x_{k}}$

### Concave

A function $f$ is concave if $-f$ is convex.

- Affine
- Powers: $f(x)=x^\alpha$ on $\mathbb{R}_{++}$ for $0\le \alpha \le 1$
- Logarithm: $f(x)=\log x$ on $\mathbb{R}_{++}$
- Log-determinant: $f(X)=\log\det X$ on $\mathbb{S}_{++}^n$
- Geometric mean: $f(\mathbf{x})=\left(\prod_{k=1}^{n} x_{k}\right)^{1 / n}$ on $\mathbb{R}_{++}^n$

## Properties of Convex Function

### Restriction to a Line

A function $f: \mathbb{R}^{n} \to \mathbb{R}$ is convex if and only if its restriction to any line is convex. That is, the function $g: \mathbb{R} \to \mathbb{R}$ defined by

$$
g(t)=f(\mathbf{x}+t\mathbf{v}), \quad \dom g=\{t \mid \mathbf{x}+t\mathbf{v} \in \dom f\}
$$

is convex in $t$ for any $\mathbf{x} \in \dom f$ and $\mathbf{v} \in \mathbb{R}^n$.

### First-Order Condition

A differentiable function $f$ with a convex domain is convex if and only if:

$$
\forall \mathbf{x}, \mathbf{y} \in \dom f, \quad f(\mathbf{y}) \ge f(\mathbf{x})+\nabla f(\mathbf{x})^{T}(\mathbf{y}-\mathbf{x})
$$

This means the tangent line at any point is a global underestimator of the function.

### Second-Order Condition

A twice-differentiable function $f$ with a convex domain is convex if and only if

$$
\forall \mathbf{x} \in \dom f, \quad \nabla^{2} f(\mathbf{x}) \succeq 0
$$

### Epigraph and Sublevel Set

The $\alpha$-sublevel set of a function $f : \mathbb{R}^{n} \to \mathbb{R}$ is

$$
C_{\alpha}=\{\mathbf{x} \in \dom f \mid f(\mathbf{x}) \le \alpha\}
$$

The sublevel sets of a convex function are always convex (the converse is not true).

The epigraph of a function $f : \mathbb{R}^{n} \to \mathbb{R}$ is

$$
\operatorname{epi}f=\{(\mathbf{x}, t) \in \mathbb{R}^{n+1} \mid \mathbf{x} \in \dom f, f(\mathbf{x}) \le t\}
$$

A function $f$ is convex if and only if its epigraph is a convex set.

### Jensen's Inequality

If $f$ is convex, then for $0 \le \theta \le 1$:

$$
f(\theta\mathbf{x}+(1-\theta)\mathbf{y}) \le \theta f(\mathbf{x})+(1-\theta)f(\mathbf{y})
$$

This extends to expectations. For any random variable $\mathbf{z}$:

$$
f(\mathbb{E}[\mathbf{z}]) \le \mathbb{E}[f(\mathbf{z})]
$$

## Operations that Preserve Function Convexity

To show a function $f$ is convex, one can either:

1. Verify the definition (often simplified by restricting to a line).
2. For a twice-differentiable function, show $\nabla^2 f(\mathbf{x}) \succeq 0$.
3. Show that $f$ is constructed from simple convex functions using operations that preserve convexity.

- Nonnegative multiple: If $f$ is convex and $\alpha \ge 0$, then $\alpha f$ is convex.
- Sum: If $f_1$ and $f_2$ are convex, then $f_1 + f_2$ is convex.
- Composition with an affine function: If $f$ is convex, then $g(\mathbf{x})=f(A\mathbf{x} + \mathbf{b})$ is convex.
- Pointwise maximum: If $f_1, \dots, f_m$ are convex, then $f(\mathbf{x})=\max\{f_1(\mathbf{x}), \dots, f_m(\mathbf{x})\}$ is convex.
- Pointwise supremum: If $f(\mathbf{x}, \mathbf{y})$ is convex in $\mathbf{x}$ for each $\mathbf{y} \in C$, then $g(\mathbf{x})=\sup_{\mathbf{y} \in C} f(\mathbf{x}, \mathbf{y})$ is convex.
- Scalar composition: Let $h: \mathbb{R} \to \mathbb{R}$ and $g: \mathbb{R}^{n} \to \mathbb{R}$. The composition $f(\mathbf{x}) = h(g(\mathbf{x}))$ is convex if:
    - $g$ is convex, $h$ is convex, and $\tilde{h}$ is non-decreasing.
    - $g$ is concave, $h$ is convex, and $\tilde{h}$ is non-increasing.
- Vector composition: Let $h: \mathbb{R}^k \to \mathbb{R}$ and $g: \mathbb{R}^{n} \to \mathbb{R}^k$. The composition $f(\mathbf{x}) = h(g(\mathbf{x})) = h(g_1(\mathbf{x}), \dots, g_k(\mathbf{x}))$ is convex if:
    - $g_i$ are convex, $h$ is convex, and $h$ is non-decreasing in each argument.
    - $g_i$ are concave, $h$ is convex, and $h$ is non-increasing in each argument.
- Minimization: If $f(\mathbf{x}, \mathbf{y})$ is convex in $(\mathbf{x}, \mathbf{y})$ and $C$ is a convex set, then $g(\mathbf{x})=\inf_{\mathbf{y} \in C} f(\mathbf{x}, \mathbf{y})$ is convex.
- Perspective: The perspective of a function $f: \mathbb{R}^n \to \mathbb{R}$ is the function $g: \mathbb{R}^{n} \times \mathbb{R} \to \mathbb{R}$ defined by $g(\mathbf{x}, t) = t f(\mathbf{x}/t)$, with domain $\dom g=\{(\mathbf{x}, t) \mid \mathbf{x}/t \in \dom f, t>0\}$. If $f$ is convex, then $g$ is convex.

## Convex Conjugate

The convex conjugate of a function $f$ is defined as:

$$
f^{*}(\mathbf{y})=\sup_{\mathbf{x} \in \dom f}(\mathbf{y}^{T}\mathbf{x}-f(\mathbf{x}))
$$

The conjugate function $f^*$ is always convex, regardless of whether $f$ is.

## Quasiconvex Function

A function $f: \mathbb{R}^{n} \to \mathbb{R}$ is quasiconvex if its domain is convex and its sublevel sets $S_{\alpha}=\{\mathbf{x} \in \dom f \mid f(\mathbf{x}) \le \alpha\}$ are convex for all $\alpha \in \mathbb{R}$. A function $f$ is quasiconcave if $-f$ is quasiconvex.

$$
S_{\alpha}=\{x \in \dom f \mid f(x) \le \alpha\}
$$

- Modified Jensen inequality: For $0 \le \theta \le 1$, $f(\theta\mathbf{x}+(1-\theta)\mathbf{y}) \le \max\{f(\mathbf{x}), f(\mathbf{y})\}$.
- First-order condition: If $f$ is differentiable, it is quasiconvex if and only if $f(\mathbf{y}) \le f(\mathbf{x}) \implies \nabla f(\mathbf{x})^{T}(\mathbf{y}-\mathbf{x}) \le 0$.
- The sum of quasiconvex functions is not necessarily quasiconvex.

## Log-Concave and Log-Convex Function

A positive function $f$ is log-concave if $\log f$ is concave. This is equivalent to:

$$
\forall  0 \le \theta \le 1, \quad f(\theta x+(1-\theta) y) \ge f(x)^{\theta} f(y)^{1-\theta}
$$

- Examples: Many common probability density function (e.g., normal distribution, exponential distribution) are log-concave.
- Second-order condition: $f(x) \nabla^{2} f(x) \preceq \nabla f(x) \nabla f(x)^{T}$.
- The product of log-concave functions is log-concave.
- The sum of log-concave functions is not necessarily log-concave.
- Integration: If $f: \mathbb{R}^{n} \times \mathbb{R}^{m} \to \mathbb{R}$ is log-concave, then $g(\mathbf{x})=\int f(\mathbf{x}, \mathbf{y})d\mathbf{y}$ is also log-concave.

Similarly, $f$ is log-convex if $\log f$ is convex.

# Optimization Problem

## Standard Optimization Problem

The general form of an optimization problem is:

$$
\begin{align*}
&\text{minimize} & &f_{0}(\mathbf{x}) \\
&\text{subject to} & &f_{i}(\mathbf{x}) \le 0, \quad i=1, \ldots, m \\
& & &h_{i}(\mathbf{x})=0, \quad i=1, \ldots, p
\end{align*}
$$

- $\mathbf{x} \in \mathbb{R}^n$ is the optimization variable.
- $f_0: \mathbb{R}^n \to \mathbb{R}$ is the objective or cost function.
- $f_i: \mathbb{R}^n \to \mathbb{R}$ are the inequality constraint functions.
- $h_i: \mathbb{R}^n \to \mathbb{R}$ are the equality constraint functions.

The optimal value, $p^{\star}$, is the infimum of the objective function over the feasible set.

- If the problem is infeasible, $p^\star = \infty$.
- If the problem is unbounded below, $p^\star = -\infty$.

## Convex Optimization Problem

A standard convex optimization problem has the form:

$$
\begin{align*}
&\text{minimize} & &f_{0}(\mathbf{x}) \\
&\text{subject to} & &f_{i}(\mathbf{x}) \le 0, \quad i=1, \dots, m \\
& & &A\mathbf{x} = \mathbf{b}
\end{align*}
$$

- The objective function $f_0$ and inequality constraint functions $f_1, \dots, f_m$ are convex.
- The equality constraint functions are affine.

**Key Properties:**

- The feasible set of a convex optimization problem is a convex set.
- Any locally optimal point is also globally optimal.

### Optimality Criterion

For a differentiable objective function $f_0$, a point $\mathbf{x}$ is optimal if and only if it is feasible and for all feasible $\mathbf{y}$:

$$
\nabla f_{0}(\mathbf{x})^{T}(\mathbf{y}-\mathbf{x}) \ge 0
$$

This means that if nonzero, $\nabla f_0(\mathbf{x})$ defines a supporting hyperplane to the feasible set at $\mathbf{x}$.

- Unconstrained problem: The optimality condition is

    $$
    \nabla f_{0}(\mathbf{x})=0
    $$

- Equality constrained problem ($\min f_0(\mathbf{x})$ s.t. $A\mathbf{x}=\mathbf{b}$): The optimality condition is

    $$
    \exists \boldsymbol{\nu}, \quad A \mathbf{x}=\mathbf{b}, \quad \nabla f_{0}(\mathbf{x})+A^{T} \boldsymbol{\nu}=0
    $$

- Minimization over the nonnegative orthant ($\min f_0(\mathbf{x})$ s.t. $\mathbf{x} \succeq \mathbf{0}$): The optimality condition is

    $$
    \mathbf{x} \succeq 0, \quad
    \begin{cases}
    \nabla f_{0}(\mathbf{x})_{i} \ge 0, \quad \text{if } x_{i}=0 \\
    {\nabla f_{0}(\mathbf{x})_{i}=0}, \quad \text{if } x_{i}>0
    \end{cases}
    $$

### Equivalent Convex Problem

- Eliminating equality constrains: If the equality constraints are $A\mathbf{x}=\mathbf{b}$, we can express the feasible set as $\{\mathbf{x} \mid \mathbf{x}=F\mathbf{z}+\mathbf{x}_0\}$ for some matrix $F$ and vector $\mathbf{x}_0$. The problem becomes an unconstrained one in terms of $\mathbf{z}$:

    $$
    \begin{align*}
    &\text{minimize} & &f_{0}(F \mathbf{z}+x_{0}) \\
    &\text{subject to} & &f_{i}(F \mathbf{z}+x_{0}) \le 0, \quad i=1, \dots, m
    \end{align*}
    $$

- Introducing slack variable: An inequality $\mathbf{a}_i^T\mathbf{x} \le b_i$ is equivalent to $\mathbf{a}_i^T\mathbf{x}+s_i=b_i$ with $s_i \ge 0$. This converts inequality constraints to equality and non-negativity constraints:

    $$
    \begin{align*}
    &\text{minimize} & &f_{0}(\mathbf{x}) \\
    &\text{subject to} & &\mathbf{a}_{i}^{T} \mathbf{x} \le b_{i}, \quad i=1, \dots, m
    \end{align*}
    $$

    is equivalent to

    $$
    \begin{align*}
    &\text{minimize}_{\mathbf{x}, \mathbf{s}} & &f_{0}(x) \\
    &\text{subject to} & &\mathbf{a}_{i}^{T} \mathbf{x}+s_{i}=b_{i}, \quad i=1, \dots, m \\
    & & &s_{i} \ge 0, \quad i=1, \dots m
    \end{align*}
    $$

- Epigraph form: Standard convex optimization problem can be transformed into minimizing a linear objective:

    $$
    \begin{align*}
    &\text{minimize}_{\mathbf{x}, t} & &t \\
    &\text{subject to} & &f_{0}(\mathbf{x})-t \le 0 \\
    & & &f_{i}(\mathbf{x}) \le 0, \quad i=1, \dots, m \\
    & & &A\mathbf{x}=\mathbf{b}
    \end{align*}
    $$

## Quasiconvex Optimization

A quasiconvex optimization problem has the form:

$$
\begin{align*}
&\text{minimize} & &f_0(\mathbf{x}) \\
&\text{subject to} & &f_{i}(\mathbf{x}) \le 0, \quad i=1, \dots, m \\
& & &A\mathbf{x}=\mathbf{b}
\end{align*}
$$

where $f_0$ is quasiconvex and $f_1, \dots, f_m$ are convex. Such problems can have locally optimal points that are not globally optimal. They can be solved using bisection method on the optimal value $t$. For a fixed $t$, checking if $f_0(\mathbf{x}) \le t$ is a convex feasibility problem.

## Linear Optimization

### Linear Programming (LP)

An LP minimizes a linear objective over a polyhedron:

$$
\begin{align*}
&\text{minimize} & &\mathbf{c}^{T}\mathbf{x}+d \\
&\text{subject to} & &G\mathbf{x} \preceq \mathbf{h} \\
& & &A\mathbf{x}=\mathbf{b}
\end{align*}
$$

### Linear-Fractional Programming

A linear-fractional program minimizes a ratios of linear functions over polyhedron:

$$
\begin{align*}
&\text{minimize} & &f_{0}(\mathbf{x}) \\
&\text{subject to} & &G \mathbf{x} \preceq \mathbf{h} \\
& & &A \mathbf{x}=\mathbf{b}
\end{align*}
$$

where the objective function is quasiconvex:

$$
f_{0}(\mathbf{x})=\frac{\mathbf{c}^{T}\mathbf{x}+d}{\mathbf{e}^{T}\mathbf{x}+f}, \quad \dom f_{0}(\mathbf{x})=\{\mathbf{x} \mid \mathbf{e}^{T}\mathbf{x}+f>0\}
$$

It can be transformed into an equivalent LP:

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

### Quadratic Programming (QP)

A QP minimizes a convex quadratic function over a polyhedron:

$$
\begin{align*}
&\text{minimize} & &(1/2)\mathbf{x}^{T}P\mathbf{x}+\mathbf{q}^{T}\mathbf{x}+r \\
&\text{subject to} & &G\mathbf{x} \preceq \mathbf{h} \\
& & &A\mathbf{x}=\mathbf{b}
\end{align*}
$$

where $P \in \mathbb{S}_+^n$.

### Quadratically Constrained Quadratic Programming (QCQP)

A QCQP minimizes a convex quadratic function subject to convex quadratic inequality constraints:

$$
\begin{align*}
&\text{minimize} & &(1/2)\mathbf{x}^{T}P_{0}\mathbf{x}+\mathbf{q}_{0}^{T}\mathbf{x}+r_{0} \\
&\text{subject to} & &(1/2)\mathbf{x}^{T}P_{i}\mathbf{x}+\mathbf{q}_{i}^{T}\mathbf{x}+r_{i} \le 0, \quad i=1, \dots, m \\
& & &A\mathbf{x}=\mathbf{b}
\end{align*}
$$

where each $P_i \in \mathbb{S}_+^n$.

If $P_1, \dots, P_m \in \mathbb{S}_{++}^n$, then the feasible region is the intersection of $m$ ellipsoids and an affine set.

### Second-Order Cone Programming (SOCP)

SOCPs are more general than LPs and QPs:

$$
\begin{align*}
&\text{minimize} & &\mathbf{f}^{T}\mathbf{x} \\
&\text{subject to} & &\norm{A_{i}\mathbf{x}+\mathbf{b}_{i}}_{2} \le \mathbf{c}_{i}^{T}\mathbf{x}+d_{i}, \quad i=1, \dots, m \\
& & &F\mathbf{x}=\mathbf{g}
\end{align*}
$$

- The inequalities are called second-order cone constraints.
- This reduces to an LP if $A_{i} = 0$, and a QCQP if $\mathbf{c}_i = 0$.

## Geometric Programming (GP)

A GP minimizes a posynomial subject to posynomial inequality constraints and monomial equality constraints:

$$
\begin{align*}
&\text{minimize} & &f_{0}(\mathbf{x}) \\
&\text{subject to} & &f_{i}(\mathbf{x}) \le 1, \quad i=1, \dots, m \\
& & &h_{i}(\mathbf{x})=1, \quad i=1, \dots, p
\end{align*}
$$

where $f_i$ are posynomials and $h_i$ are monomials.

In the context of GP, a monomial function is

$$
f(\mathbf{x})=c x_{1}^{a_{1}} x_{2}^{a_{2}} \cdots x_{n}^{a_{n}}, \quad \dom f=\mathbb{R}_{++}^{n}
$$

where $c>0$ and $\alpha_i \in \mathbb{R}$. A posynomial is any sum of monomials.

A GP is not convex in its original form but can be transformed into a convex problem by a change of variables ($y_i = \log x_i$) and taking the log of the objective and constraints.

## Generalized Inequality Constraint

A convex optimization problem can be formulated using generalized inequalities defined by proper cones:

$$
\begin{align*}
&\text{minimize} & &f_{0}(\mathbf{x}) \\
&\text{subject to} & &f_{i}(\mathbf{x}) \preceq_{K_{i}} \mathbf{0}, \quad i=1, \dots, m \\
& & &A\mathbf{x}=\mathbf{b}
\end{align*}
$$

- $f_0: \mathbb{R}^n \to \mathbb{R}$ is a convex function.
- $K_i \subseteq \mathbb{R}^{k_i}$ are proper cones.
- $f_i: \mathbb{R}^n \to \mathbb{R}^{k_i}$ is a $K_i$-convex function.

This generalized form retains all the key properties of standard convex problems: the feasible set is convex, and any local optimum is a global optimum.

### Conic Form Problem

A conic form problem is a linear program with a generalized inequality constraint:

$$
\begin{align*}
&\text{minimize} & &\mathbf{c}^{T}\mathbf{x} \\
&\text{subject to} & &F\mathbf{x}+\mathbf{g} \preceq_{K} \mathbf{0} \\
& & &A\mathbf{x}=\mathbf{b}
\end{align*}
$$

This extends standard LPs (where $K = \mathbb{R}_+^m$) to problems involving non-polyhedral cones, such as the second-order cone or the semidefinite cone.

### Semidefinite Programming (SDP)

An SDP is a conic form problem where the cone is the positive semidefinite matrices, $\mathbb{S}_+^k$. The constraint is a linear matrix inequality (LMI):

$$
\begin{align*}
&\text{minimize} & &\mathbf{c}^{T}\mathbf{x} \\
&\text{subject to} & &x_{1}F_{1}+x_{2}F_{2}+\cdots+x_{n}F_{n}+G \preceq 0 \\
& & &A\mathbf{x}=\mathbf{b}
\end{align*}
$$

where the matrices $F_i, G \in \mathbb{S}^k$ are given symmetric matrices.

## Vector Optimization

A general vector optimization problem minimizes a vector objective $f_0: \mathbb{R}^n \to \mathbb{R}^q$ with respect to a proper cone $K \subseteq \mathbb{R}^q$.

$$
\begin{align*}
&\text{minimize}_K & &f_{0}(x) \\
&\text{subject to} & &f_{i}(x) \le 0, \quad i=1, \dots, m \\
& & &h_{i}(x) \le 0, \quad i=1, \dots, p
\end{align*}
$$

We say the vector optimization problem is a convex vector optimization problem if the objective function is $K$-convex, the inequality constraint functions $f_1,\dots, f_m$ are convex, and the equality constraint functions $h_{1},\dots h_{p}$ are affine (in the scalar case, it becomes $A\mathbf{x} = \mathbf{b}$).

### Optimal and Pareto Optimal Point

Set of achievable objective values $\mathcal{O}=\{f_{0}(x) \mid x \text{ feasible}\}$

- feasible $x$ is optimal if $f_0(x)$ is a minimum value of $\mathcal{O}$
- feasible $x$ is Pareto optimal if $f_0(x)$ is a minimal value of $\mathcal{O}$

# Duality

## Lagrange Dual Function

For a standard optimization problem, the Lagrangian is defined as:

$$
L(\mathbf{x}, \boldsymbol{\lambda}, \boldsymbol{\nu})=f_{0}(\mathbf{x})+\sum_{i=1}^{m}\lambda_{i}f_{i}(\mathbf{x})+\sum_{i=1}^{p}\nu_{i}h_{i}(\mathbf{x})
$$

The Lagrange dual function is the infimum of the Lagrangian over $\mathbf{x}$:

$$
g(\boldsymbol{\lambda}, \boldsymbol{\nu}) =\inf_{\mathbf{x} \in \mathcal{D}} L(\mathbf{x}, \boldsymbol{\lambda}, \boldsymbol{\nu})
$$

The dual function $g$ is always concave, regardless of the convexity of the original problem. For any $\boldsymbol{\lambda} \succeq \mathbf{0}$, it provides a lower bound on the optimal value: $g(\boldsymbol{\lambda}, \boldsymbol{\nu}) \le p^{\star}$.

## Lagrange Dual Problem

The dual problem seeks the best possible lower bound:

$$
\begin{align*}
&\text{maximize} & &g(\boldsymbol{\lambda}, \boldsymbol{\nu}) \\
&\text{subject to} & &\boldsymbol{\lambda} \succeq \mathbf{0}
\end{align*}
$$

This is always a convex optimization problem. Let its optimal value be $d^{\star}$.

## Optimality Condition

- Weak duality: $d^\star \le p^\star$ always holds, can be expressed as

    $$
    \sup_{\lambda \succeq 0} \inf_{x} L(x, \lambda) \le \inf_x \sup_{\lambda \succeq 0} L(x, \lambda)
    $$

- Strong duality: $d^\star = p^\star$ (usually) holds for convex problems. When strong duality holds, $x^\star$ and $\lambda^\star$ form a saddle point for the Lagrangian (converse also true).

### Slater's Condition

Conditions that guarantee strong duality in convex problems are called constraint qualifications. One simple constraint qualification is Slater's condition: There exists a strictly feasible point, i.e.,

$$
\exists x \in \operatorname{int} \mathcal{D}, \quad f_{i}(x)<0, \ i=1, \dots, m, \quad A x=b
$$

### KKT Conditions

If strong duality holds, and $\mathbf{x}^{\star}, \boldsymbol{\lambda}^{\star}, \boldsymbol{\nu}^{\star}$ are primal and dual optimal points, they must satisfy the KKT conditions:

1. Primal Feasibility: $f_{i}(\mathbf{x}^{\star}) \le 0$ and $h_{i}(\mathbf{x}^{\star})=0$
2. Dual Feasibility: $\boldsymbol{\lambda}^{\star} \succeq \mathbf{0}$
3. Complementary Slackness: $\lambda_i^{\star} f_i(\mathbf{x}^{\star}) = 0$
4. Stationarity: The gradient of the Lagrangian with respect to $\mathbf{x}$ vanishes at $\mathbf{x}^{\star}$:

    $$
    \nabla f_{0}(\mathbf{x}^{\star})+\sum_{i=1}^{m}\lambda_{i}^{\star}\nabla f_{i}(\mathbf{x}^{\star})+\sum_{i=1}^{p}\nu_{i}^{\star}\nabla h_{i}(\mathbf{x}^{\star})=\mathbf{0}
    $$

For a convex problem, any set of points $(\tilde{\mathbf{x}}, \tilde{\boldsymbol{\lambda}}, \tilde{\boldsymbol{\nu}})$ that satisfies the KKT conditions is primal and dual optimal.

# Application

## Geometric Problem

### Minimum Volume Ellipsoid Around a Set

Find the ellipsoid $\mathcal{E}$ of minimum volume that contains a given set $C \subseteq \mathbb{R}^n$.

- The ellipsoid is parametrized as $\mathcal{E}=\{\mathbf{v} \mid \norm{A\mathbf{v}+\mathbf{b}}_{2} \le 1 \}$, where $A \in \mathbb{S}_{++}^n$
- The volume of $\mathcal{E}$ is proportional to $\det A^{-1}$

The problem can be formulated as:

$$
\begin{align*}
&\text{minimize}_{A,\mathbf{b}} & &\log \det A^{-1}\\
&\text{subject to} & &\sup_{\mathbf{v} \in C}\norm{A \mathbf{v}+\mathbf{b}}_{2} \le 1
\end{align*}
$$

### Maximum Volume Inscribed Ellipsoid

Find the ellipsoid $\mathcal{E}$ of maximum volume that fits inside a given convex set $C \subseteq \mathbb{R}^n$.

- The ellipsoid is parametrized as $\mathcal{E}=\{B \mathbf{u}+\mathbf{d} \mid \norm{\mathbf{u}}_{2} \le 1\}$, where $B \in \mathbb{S}_{++}^n$
- The volume of $\mathcal{E}$ is proportional to $\det B$

The problem can be formulated as:

$$
\begin{align*}
&\text{maximize}_{B, \mathbf{d}} & &\log\det B \\
&\text{subject to} & &B \mathbf{u} + \mathbf{d} \in C \quad \text{for all } \norm{\mathbf{u}}_{2} \le 1
\end{align*}
$$

### Linear Discrimination

Find the hyperplane defined by $(\mathbf{a}, b)$ that separates two given sets of points $\{\mathbf{x}_1, \dots, \mathbf{x}_N\},\ \{\mathbf{y}_1, \dots, \mathbf{y}_M\}$:

$$
\mathbf{a}^{T}\mathbf{x}_{i}+b>0, \ i=1, \dots, N \quad \text{and} \quad \mathbf{a}^{T}\mathbf{y}_{i}+b<0, \ i=1, \dots, M
$$

Since the inequalities are homogeneous in $\mathbf{a}$ and $b$, this is equivalent to the following set of linear inequalities, which is a convex feasibility problem:

$$
\mathbf{a}^{T}\mathbf{x}_{i}+b \ge 1, \ i=1, \dots, N \quad \text{and} \quad \mathbf{a}^{T}\mathbf{y}_{i}+b \le-1, \ i=1, \dots, M
$$

### Support Vector Machine (SVM)

The SVM finds the hyperplane that maximizes the margin (the distance between the hyperplane and the nearest point from either set). This can be formulated as a QP:

$$
\begin{align*}
&\text{minimize} & &(1/2)\norm{\mathbf{a}}_{2}^2 \\
&\text{subject to} & &\mathbf{a}^{T}\mathbf{x}_{i}+b \ge 1, \quad i=1, \dots, N \\
& & &\mathbf{a}^{T}\mathbf{y}_{i}+b \le-1, \quad i=1, \dots, M
\end{align*}
$$

To handle data that is not linearly separable, slack variables $(\mathbf{u}, \mathbf{v})$ are introduced, leading to the soft-margin SVM formulation, which balances maximizing the margin against minimizing classification errors:

$$
\begin{align*}
&\text{minimize} & &(1/2)\norm{\mathbf{a}}_{2}^2+\gamma(\mathbf{1}^{T}\mathbf{u}+\mathbf{1}^{T}\mathbf{v}) \\
&\text{subject to} & &\mathbf{a}^{T}\mathbf{x}_{i}+b \ge 1-u_{i}, \quad i=1, \dots, N \\
& & &\mathbf{a}^{T}\mathbf{y}_{i}+b \le -1+v_{i}, \quad i=1, \dots, M \\
& & &\mathbf{u} \succeq \mathbf{0}, \quad \mathbf{v} \succeq \mathbf{0}
\end{align*}
$$

## Data Fitting

### Norm Approximation

Find an $\mathbf{x}$ that minimizes the discrepancy between $A\mathbf{x}$ and a vector $\mathbf{b}$, as measured by a norm:

$$
\text{minimize } \norm{A \mathbf{x}-\mathbf{b}}
$$

where $A \in \mathbb{R}^{m \times n}$ with $m\ge n$. This is a convex problem for any norm.

A common application s a linear measurement model, $\mathbf{y} = A\mathbf{x}_{\text{true}} + \mathbf{v}$, where $y$ represents measurements, $\mathbf{x}_{\text{true}}$ is an unknown parameter vector, and $\mathbf{v}$ is measurement noise. Given an observation $\mathbf{y}=\mathbf{b}$, the solution $\mathbf{x}^\star$ to the norm approximation problem is the best estimate of $\mathbf{x}_{\text{true}}$.

### Least Norm Problem

When a system of linear equations $A\mathbf{x}=\mathbf{b}$ is underdetermined ($m \le n$), there can be infinitely many solutions. The least-norm problem seeks the solution with the minimum norm:

$$
\begin{align*}
&\text{minimize} & &\norm{\mathbf{x}} \\
&\text{subject to} & &A\mathbf{x}=\mathbf{b}
\end{align*}
$$

This is a convex optimization problem that selects the "smallest" solution from the affine subspace of all solutions.

### Regularized Approximation

This approach combines the objectives of norm approximation and least norm problems. It is also known as Tikhonov regularization when the $L_2$-norm is used.

$$
\text{minimize } \norm{A\mathbf{x}-\mathbf{b}}+\gamma\norm{\mathbf{x}}
$$

The parameter $\gamma > 0$ controls the trade-off between minimizing the approximation error $\norm{A\mathbf{x}-\mathbf{b}}$ and the size of the solution $\norm{\mathbf{x}}$. This formulation is useful for improving the conditioning of the problem and preventing overfitting.

## Statistical Estimation

### Maximum Likelihood Estimation (MLE)

MLE is a method for estimating the parameters of a statistical model.

For a linear measurement model with IID noise, $y_{i}=\mathbf{a}_{i}^{T}\mathbf{x}+v_{i}$, the log-likelihood function to be maximized is:

$$
l(\mathbf{x}) = \sum_{i=1}^{m} \log p(y_{i}-\mathbf{a}_{i}^{T}\mathbf{x})
$$

where $p$ is the probability density function of the noise. If $\log p$ is a concave function, this is a convex optimization problem.

Different noise type:

- Gaussian distribution $\mathcal{N}(0, \sigma^{2}) : p(z)=(2 \pi \sigma^{2})^{-1 / 2} e^{-z^{2} /(2 \sigma^{2})}$. Maximizing $l(\mathbf{x})$ is equivalent to minimizing the sum of squares of the residuals, $\sum(y_i - \mathbf{a}_i^T\mathbf{x})^2$, which is a least squares problem.
- Laplace distribution $p(z)=(1 /(2 a)) e^{-\abs{z} / a}$. Maximizing $l(\mathbf{x})$ is equivalent to minimizing the sum of absolute values of the residuals, $\sum|y_i - \mathbf{a}_i^T\mathbf{x}|$, which is an $L_1$-norm approximation problem.
- Uniform distribution on $[-c, c]$. Maximizing $l(\mathbf{x})$ is equivalent to finding a feasible point for the linear inequalities $|\mathbf{a}_{i}^{T}\mathbf{x}-y_{i}| \le c$.

### Logistic Regression

Logistic regression is used for binary classification. It models the probability of an outcome $y \in \{0, 1\}$ given features $\mathbf{u}$ as:

$$
p = \operatorname{prob}(y=1) = \frac{\exp(\mathbf{a}^{T}\mathbf{u}+b)}{1+\exp(\mathbf{a}^{T}\mathbf{u}+b)}
$$

Given $m$ data points $(\mathbf{u}_i, y_i)$, the parameters $(\mathbf{a}, b)$ are found by maximizing the log-likelihood function. If the observed outcomes are $y_1, \dots, y_k = 1$ and $y_{k+1}, \dots, y_m = 0$, the log-likelihood is:

$$
l(\mathbf{a}, b) = \sum_{i=1}^{k}(\mathbf{a}^{T}\mathbf{u}_{i}+b)-\sum_{i=1}^{m} \log(1+\exp(\mathbf{a}^{T}\mathbf{u}_{i}+b))
$$

This function is concave in $\mathbf{a}$ and $b$, so finding the MLE parameters is a convex optimization problem.
