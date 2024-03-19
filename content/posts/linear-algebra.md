---
title: Linear Algebra Review
date: 2019-01-18
categories:
- Study
tags:
- UW
- Math
---

From ME 510 & AMATH 584

<!--more-->

---

A subspace of a vector space is a subset of a vector space which is itself a vector space.

# QR Decomposition

$$
\require{mathtools}
\DeclarePairedDelimiters\norm{\lVert}{\rVert}
A = \begin{bmatrix}
Q_1 & Q_2
\end{bmatrix}
\begin{bmatrix}
R_1 \\ 0
\end{bmatrix}
= Q_1R_1
$$
where $A \in \mathbb{R}^{n \times k}$, $(n \ge k)$, $[Q_1, Q_2]$ is orthogonal, $Q_1 \in \mathbb{R}^{n \times r}$, $Q_2 \in \mathbb{R}^{n \times (n-r)}$, $R_1 \in \mathbb{R}^{r \times r}$, $\operatorname{rank}(A) = r$

- $Q_1 R_1$ is called the reduced QR factorization

- $Q^T Q = I_r$, and $R_1$ is upper triangular & invertible
- usually computed using a variation on Gram-Schmidt procedure which is less sensitive to numerical (rounding) errors
- column of $Q_1$ are orthonormal basis for $\mathcal{R}(A)$, $\mathcal{R}(Q_1) = \mathcal{R}(A)$
- column of $Q_2$ are orthonormal basis for $\mathcal{N}(A^T)$, $\mathcal{R}(Q_2) = \mathcal{N}(A^T)$

# Least-Squares

Consider $y = Ax$ where $A \in \mathbb{R}^{m \times n}$, $m>n$. To solve this equation approximately, we first define the residual $r = Ax-y$ and trying to find $x = x_{\mathrm{ls}}$ that minimizes $\norm{r}$
$$
\norm{r}^2 = x^TA^TAx - 2y^TAx + y^Ty
$$
set gradient to zero
$$
\nabla_x \norm{r}^2 = 2A^TAx - 2A^Ty = 0
$$
we get
$$
x_\mathrm{ls} = (A^TA)^{-1}A^Ty
$$

- $A^\dagger = (A^TA)^{-1} A^T$ is a left inverse of $A$

- $Ax_\mathrm{ls}$ is the point in $\mathcal{R}(A)$ that is closest to $y$, i.e. it is the projection of $y$ onto $\mathcal{R}(A)$: $Ax_\mathrm{ls} = \mathcal{P}_{\mathcal{R}(A)}(y) = A(A^TA)^{-1}A^Ty$

- $A(A^TA)^{-1}A^T$ gives projection onto $\mathcal{R}(A)$
- optimal residual $r = Ax_\mathrm{ls} - y$ is orthogonal to $\mathcal{R}(A)$: $\langle r, Az \rangle = 0$ for all $z \in \mathbb{R}^n$

# Least-Squares via $QR$ Factorization

$$
\norm{Ax - y}^2 = \norm*{\begin{bmatrix}
Q_1 & Q_2
\end{bmatrix}
\begin{bmatrix}
R_1 \\ 0
\end{bmatrix}
x - y}^2
$$
since multiplication by orthogonal matrix does not change norm, so
$$
\begin{split}
\norm{Ax - y}^2 &= \norm*{\begin{bmatrix}
Q_1 & Q_2
\end{bmatrix}^T
\begin{bmatrix}
Q_1 & Q_2
\end{bmatrix}
\begin{bmatrix}
R_1 \\ 0
\end{bmatrix}
x -
\begin{bmatrix}
Q_1 & Q_2
\end{bmatrix}^T
y}^2 \\
&=\norm*{\begin{bmatrix}
R_1 x - Q_1^T y \\
-Q_2^T y
\end{bmatrix}}^2 \\
&=\norm{R_1 x - Q_1^T y}^2 + \norm{Q_2^T y}^2
\end{split}
$$
this is minimized by choosing $x_\mathrm{ls} = R_1^{-1} Q_1^T y$

- residual with optimal $x$ is $Ax_\mathrm{ls} - y = -Q_2 Q_2^T y$
- $Q_1 Q_1^T$ gives projection onto $\mathcal{R}(A)$
- $Q_2 Q_2^T$ gives projection onto $\mathcal{R}(A)^\perp$

# Gauss-Newton Method for Nonlinear Least-Squares (NLLS) Problem

Find $x \in \mathbb{R}^n$ that minimizes
$$
\norm{r(x)}^2 = \sum_{i = 1}^m r_i(x)^2
$$
where $r:\mathbb{R}^n \to \mathbb{R}^m$

Linearize $r$ near current iterate $x^{(k)}$
$$
r(x) \approx r(x^k) + J(x^{(k)})(x-x^{(k)})
$$
where $J$ is the Jacobian matrix

At $k$-th iteration, we approximate NLLS problem by linear LS problem:
$$
\norm{r(x)}^2 \approx \norm*{A^{(k)}x - b^{(k)}}^2
$$
where $A^{(k)} = J(x^{(k)})$, $b^{(k)} = J(x^{(k)}) x^{(k)} - r(x^{(k)})$

At $k+1$ iteration
$$
x^{(k+1)} = \left( A^{(k)T} A^{(k)} \right)^{-1} A^{(k)T} b^{(k)}
$$

# Underdetermined Linear Equations

Consider
$$
y = Ax
$$
where $A \in \mathbb{R}^{m \times n}$, $(m < n)$. Assume $\operatorname{rank}(A) = m$, the solution has the form
$$
\{ x \mid Ax = y \} = \{ x_p + z \mid z\in \mathcal{N}(A) \}
$$
where $x_p$ is any solution that satisfies $A x_p = y$

- solution has $\dim \mathcal{N}(A) = n - m$ degrees of freedom

- the least-norm solution is $x_\mathrm{ln} = A^T (A A^T)^{-1} y$
  $$
  \begin{align*}
  &\min & &\norm{x} \\
  &\text{ s.t.} & &Ax = y
  \end{align*}
  $$

- $A^\dagger = A^T (A A^T)^{-1}$ is a right inverse of $A$

- $I - A^T (A A^T)^{-1} A$ gives projection onto $\mathcal{N}(A)$

- apply QR factorization of $A^T = QR$ can get the same result $x_\mathrm{ln} = QR^{-T} y$

- derivation via Lagrange multipliers: $L(x, \lambda) = x^T x + \lambda^T (A x - y)$
  $$
  \nabla_x L = 2 x + A^T \lambda \, , \quad \nabla_\lambda L = A x - y = 0
  $$

# General Norm Minimization With Equality Constraints

$$
\begin{align*}
&\min & &\norm{ A x - b } \\
&\text{ s.t.} & &C x = d
\end{align*}
$$
Lagrangian is
$$
\begin{split}
L(x, \lambda) &= \frac{1}{2} \norm{Ax - b}^2 + \lambda^T (Cx -d) \\
&= \frac{1}{2} x^T A^T Ax - b^T Ax +\frac{1}{2} b^T b + \lambda^T Cx - \lambda^T d
\end{split}
$$
optimality conditions are
$$
\nabla_x L = A^T Ax - A^T b + C^T \lambda = 0\, , \quad
\nabla_\lambda L = Cx - d = 0
$$
write in block matrix form as
$$
\begin{bmatrix}
A^T A & C^T \\
C & 0
\end{bmatrix}
\begin{bmatrix}
x \\
\lambda
\end{bmatrix} =
\begin{bmatrix}
A^T b \\
d
\end{bmatrix}
$$
if the block matrix is invertible, we can solve for $x$ and $\lambda$.

If $A^T A$ is invertible, we can derive a more explicit formula for $x$
$$
x = (A^T A)^{-1} \Big(A^T b - C^T \big(C (A^T A)^{-1} C^T\big)^{-1} \big(C (A^T A)^{-1} A^T b\big)\Big)
$$

# Householder Triangularization

The other principal method for computing $QR$ factorizations (besides Gram-Schmidt orthogonalization).

Gram-Schmidt:
$$
A R_1 R_2 \cdots R_n = \hat{Q}
$$
Householder:
$$
Q_n \cdots Q_2 Q_1 A = R
$$
In general, $Q_k$ operates on row $k,\dots,m$. At the beginning of step $k$, there is a block of zeros in the first $k-1$ columns of these rows. The application of $Q_k$ forms linear combinations of these rows. After $n$ steps, all the entries below the diagonal have been eliminated and $Q_n \cdots Q_2 Q_1 A = R$ is upper-triangular.

Each $Q$ is chosen to be a unitary matrix of the form
$$
Q_k = \begin{bmatrix}
I & 0 \\
0 & F
\end{bmatrix}
$$
where $I_k$ is the $(k-1) \times (k-1)$ identity and $F$ is an $(m-k+1) \times (m-k+1)$ unitary matrix. Multiplication by $F$ must introduce zeros into the $k$th column. The Householder algorithm chooses $F$ to be a particular matrix called a *Householder reflector*.
$$
Fx = \begin{bmatrix}
\norm{x} \\
0 \\
\vdots \\
0
\end{bmatrix}
=\norm{x} e_1
$$
$F$ will reflect the space across the hyperplane $H$ orthogonal to $v = \norm{x} e_1 -x$. Note that
$$
I - \frac{vv^*}{v*v}
$$
projects onto the space $H$. To reflect $y$ across $H$, we have
$$
F = I - 2\frac{vv^*}{v*v}
$$
Due to stability issues, in practice we use $v = \operatorname{sign}(x_1) \norm{x} e_1 + x$, i.e. always reflect $x$ to the vector $\pm \norm{x} e_1$ that is not too close to $x$ itself. $\operatorname{sign}(x_1) = 0$ if $x_1 = 0$.

# Conditioning and Condition Numbers

A *well-conditioned* problem is one with the property that all small perturbations of $x$ lead to only small changes in $f(x)$.

- Absolute Condition Number:
  $$
  \hat{\kappa}  = \lim_{\delta \to 0} \sup_{\norm{\delta x} \le \delta} \frac{\norm{\delta f}}{\norm{\delta x}}
  $$

If $f$ is differentiable, then $\hat{\kappa} = \norm{J(x)}$, where $J(x)$ is the Jacobi matrix.

- Relative Condition Number:
  $$
  \kappa = \lim_{\delta \to 0} \sup_{\norm{\delta x} \le \delta}
  \left(
  \frac{\norm{\delta f}}{\norm{f(x)}}
  \bigg/
  \frac{\norm{\delta x}}{\norm{x}}
  \right)
  $$
  If $f$ is differentiable, then
  $$
  \kappa = \frac{\norm{J(x)}}{\norm{f(x)} / \norm{x}}
  $$

Relative condition numbers are more important in numerical analysis because the floating point arithmetic used by computers introduces relative errors rather than absolute ones.

- Condition Number of a matrix
  $$
  \begin{split}
  \kappa &= \sup_{\delta x}
  \left(
  \frac{\norm{A(x + \delta x) - Ax}}{\norm{Ax}}
  \bigg/
  \frac{\norm{\delta x}}{\norm{x}}
  \right) \\
  &=\sup_{\delta x} \frac{\norm{A \delta x}}{\norm{\delta x}}
  \bigg/
  \frac{\norm{Ax}}{\norm{x}} \\
  &= \norm{A} \frac{\norm{x}}{\norm{Ax}} \\
  &\le \norm{A} \norm{A^{-1}}
  \end{split}
  $$

If $\norm{\cdot} = \norm{\cdot}_2$, then $\norm{A} = \sigma_1$ and $\norm{A^{-1}} = 1/\sigma_m$. Thus
$$
\kappa(A) = \frac{\sigma_1}{\sigma_m}
$$
The ratio can be interpreted as the eccentricity of the hyperellipse.

When $A$ is a rectangular matrix, we can use the pseudoinverse: $\kappa(A) = \norm{A} \norm{A^\dagger}$

# Cholesky Factorization

Hermitian matrix is a kind of matrix that satisfies $A = A^*$.
$$
\begin{split}
A &= \begin{bmatrix}
a_{11} & w^* \\
w & K
\end{bmatrix} \\
&= \begin{bmatrix}
\alpha & 0 \\
w/\alpha & I
\end{bmatrix}
\begin{bmatrix}
1 & 0 \\
0 & K-ww^*/a_{11}
\end{bmatrix}
\begin{bmatrix}
\alpha & w^*/\alpha \\
0 & I
\end{bmatrix} \\
&= R_1^*A_1 R_1
\end{split}
$$
where $\alpha = \sqrt{a_{11}}$. Continue this process until
$$
A = \underbrace{R_1^* R_2^*\cdots R_m^*}_{R^*}\ \underbrace{R_m\cdots R_2 R_1}_{R}
$$
where $R$ is upper-triangular.

Every Hermitian positive definite matrix $A \in \mathbb{C}^{m \times m}$ has a unique Cholesky factorization.
