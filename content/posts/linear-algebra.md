---
title: Linear Algebra Review
date: 2019-01-18
lastmod: 2025-05-03
categories:
- Study
tags:
- UW
- Math
---

From ME 510 & AMATH 584

<!--more-->

---

A subspace is a subset of a vector space that satisfies the requirements to be a vector space itself, using the same vector addition and scalar multiplication operations.

# QR Decomposition

$$
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

- $Q_1 R_1$ is called the reduced QR decomposition
- $Q_1^T Q_1 = I_r$
- usually computed using a variation on Gram-Schmidt process which is less sensitive to numerical (rounding) errors
- The columns of $Q_1$ are an orthonormal basis of the column space of $A$: $\mathcal{R}(Q_1) = \mathcal{R}(A)$
- The columns of $Q_2$ are an orthonormal basis of the null space of $A^T$: $\mathcal{R}(Q_2) = \mathcal{N}(A^T)$

# Least Squares

Consider $y = Ax$ where $A \in \mathbb{R}^{m \times n}$, $m>n$. To find an approximate solution $x$, we first define the residual $r = Ax-y$ and find $x = x_{\mathrm{ls}}$ that minimizes $\norm{r}$.

$$
\norm{r}^2 = x^TA^TAx - 2y^TAx + y^Ty
$$

Setting the gradient to zero gives

$$
\nabla_x \norm{r}^2 = 2A^TAx - 2A^Ty = 0
$$

we get the solution

$$
x_\mathrm{ls} = (A^TA)^{-1}A^Ty
$$

- $A^\dagger = (A^TA)^{-1} A^T$ is a left inverse of $A$
- $Ax_\mathrm{ls}$ is the point in $\mathcal{R}(A)$ that is closest to $y$, i.e. it is the projection of $y$ onto $\mathcal{R}(A)$: $Ax_\mathrm{ls} = \mathcal{P}_{\mathcal{R}(A)}(y) = A(A^TA)^{-1}A^Ty$
- $A(A^TA)^{-1}A^T$ gives projection onto $\mathcal{R}(A)$
- optimal residual $r = Ax_\mathrm{ls} - y$ is orthogonal to $\mathcal{R}(A)$: $\langle r, Az \rangle = 0$ for all $z \in \mathbb{R}^n$

# Least Squares via QR Decomposition

$$
\norm{Ax - y}^2 = \norm*{\begin{bmatrix}
Q_1 & Q_2
\end{bmatrix}
\begin{bmatrix}
R_1 \\ 0
\end{bmatrix}
x - y}^2
$$

Since multiplication by an orthogonal matrix does not change the norm, we have

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

This is minimized by choosing $x_\mathrm{ls} = R_1^{-1} Q_1^T y$.

- residual with optimal $x$ is $Ax_\mathrm{ls} - y = -Q_2 Q_2^T y$
- $Q_1 Q_1^T$ gives projection onto $\mathcal{R}(A)$
- $Q_2 Q_2^T$ gives projection onto $\mathcal{R}(A)^\perp$

# Gauss-Newton Method for Nonlinear Least Squares Problem

Find $x \in \mathbb{R}^n$ that minimizes

$$
\norm{r(x)}^2 = \sum_{i = 1}^m r_i(x)^2
$$

where $r:\mathbb{R}^n \to \mathbb{R}^m$.

Linearize $r(x)$ near a current iterate $x^{(k)}$:

$$
r(x) \approx r(x^k) + J(x^{(k)})(x-x^{(k)})
$$

where $J(x^{(k)})$ is the Jacobian matrix of $r$ at $x^{(k)}$.

Let $\delta x = x - x^{(k)}$. At the $k$-th iteration, we solve the linear least squares problem for $\delta x$:

$$
\min_{\delta x} \norm{r(x^{(k)}) + J(x^{(k)}) \delta x}^2
$$

The solution $\delta x^{(k)}$ is given by the normal equations:

$$
J(x^{(k)})^T J(x^{(k)}) \delta x^{(k)} = -J(x^{(k)})^T r(x^{(k)})
$$

The next iterate is then

$$
x^{(k+1)} = x^{(k)} + \delta x^{(k)}
$$

# Underdetermined Linear Equations

Consider

$$
y = Ax
$$

where $A \in \mathbb{R}^{m \times n}$, $(m < n)$. Assume $\operatorname{rank}(A) = m$. The solution set has the form

$$
\{ x \mid Ax = y \} = \{ x_p + z \mid z\in \mathcal{N}(A) \}
$$

where $x_p$ is any particular solution that satisfies $A x_p = y$.

- The solution set has $\dim \mathcal{N}(A) = n - m$ degrees of freedom.
- The least norm solution is $x_\mathrm{ln} = A^T (A A^T)^{-1} y$.

    $$
    \begin{align*}
    &\min & &\norm{x} \\
    &\text{ s.t.} & &Ax = y
    \end{align*}
    $$

- $A^\dagger = A^T (A A^T)^{-1}$ is a right inverse of $A$
- $I - A^T (A A^T)^{-1} A$ gives the projection onto $\mathcal{N}(A)$.
- Applying the QR factorization $A^T = QR$ gives the same solution $x_\mathrm{ln} = QR^{-T} y$.
- The derivation via Lagrange multiplier: $L(x, \lambda) = x^T x + \lambda^T (A x - y)$

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

The Lagrangian is

$$
\begin{split}
L(x, \lambda) &= \frac{1}{2} \norm{Ax - b}^2 + \lambda^T (Cx -d) \\
&= \frac{1}{2} x^T A^T Ax - b^T Ax +\frac{1}{2} b^T b + \lambda^T Cx - \lambda^T d
\end{split}
$$

The optimality conditions are

$$
\begin{gather*}
\nabla_x L = A^T Ax - A^T b + C^T \lambda = 0 \\
\nabla_\lambda L = Cx - d = 0
\end{gather*}
$$

Writing this in block matrix form yields:

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

If the block matrix is invertible, we can solve for $x$ and $\lambda$.

If $A^T A$ is invertible, $x$ can be explicitly solved as:

$$
x = (A^T A)^{-1} \Big(A^T b - C^T \big(C (A^T A)^{-1} C^T\big)^{-1} \big(C (A^T A)^{-1} A^T b\big)\Big)
$$

# Householder Transformation

This is one of the principal methods for computing QR factorizations (the other being Gram-Schmidt orthogonalization).

Gram-Schmidt orthogonalization: orthogonalizing the columns of $A$ to form the orthonormal columns

$$
A R_1 R_2 \cdots R_n = \hat{Q}
$$

Householder transformation: applying a sequence of orthogonal transformations to $A$ to reduce it to upper triangular form

$$
Q_n \cdots Q_2 Q_1 A = R
$$

After $k-1$ steps, the first $k-1$ columns of the matrix have been transformed into upper triangular form. The purpose of $Q_k$ is to zero out the entries below the diagonal in the $k$-th column, while preserving the zeros created in previous steps.

$$
Q_k = \begin{bmatrix}
I_{k-1} & 0 \\
0 & F
\end{bmatrix}
$$

where $I_{k-1}$ is the identity matrix and $F$ is an $(m-k+1) \times (m-k+1)$ unitary matrix.

$F$ is chosen to transform the current subvector $x$ (which consists of the entries in column $k$ from row $k$ downwards) into a multiple of the first standard basis vector $e_1$ of size $m-k+1$:

$$
Fx = \alpha e_1
$$

where $\alpha = \norm{x}_2$

$F$ is a Householder reflector, defined as $F = I - 2vv^*/(v^*v)$. This matrix represents a reflection across the hyperplane whose normal vector is $v$. To transform $x$ to $\alpha e_1$, we choose the vector $v$ that bisects the angle between $x$ and $\alpha e_1$. A suitable choice is $v = x - \alpha e_1$.

For numerical stability, choose $\alpha = -\operatorname{sign}(x_1)\norm{x}_2$, where $x_{1}$ is the first component of $x$. This choice ensures $v$ is not close to zero.

# Conditioning and Condition Numbers

A problem is **well-conditioned** if small perturbations in the input lead to only small changes in the output.

- Absolute Condition Number for a function $f$:

    $$
    \hat{\kappa}  = \lim_{\delta \to 0} \sup_{\norm{\delta x} \le \delta} \frac{\norm{f(x+\delta x) - f(x)}}{\norm{\delta x}}
    $$

    If $f$ is differentiable, then $\hat{\kappa} = \norm{J(x)}$, where $J(x)$ is the Jacobian matrix.

- Relative Condition Number for a function $f$:

    $$
    \kappa = \lim_{\delta \to 0} \sup_{\norm{\delta x} \le \delta}
    \left(
    \frac{\norm{f(x+\delta x) - f(x)}}{\norm{f(x)}}
    \bigg/
    \frac{\norm{\delta x}}{\norm{x}}
    \right)
    $$

    If $f$ is differentiable and $f(x) \ne 0$, then

    $$
    \kappa = \frac{\norm{J(x)}}{\norm{f(x)} / \norm{x}}
    $$

Relative condition numbers are more important in numerical analysis because floating-point arithmetic introduces relative errors.

- Condition Number of a matrix $A$: For an invertible square matrix $A$, the condition number is $\kappa(A) = \norm{A}\norm{A^{-1}}$. It quantifies how much the solution $x$ of $Ax=b$ can change with respect to a change in $b$.

    $$
    \frac{\norm{\delta x}}{\norm{x}} \le \kappa(A) \frac{\norm{\delta b}}{\norm{b}}
    $$

    For the 2-norm ($\norm{\cdot}_2$), $\norm{A} = \sigma_{\max}(A)$ and $\norm{A^{-1}} = 1/\sigma_{\min}(A)$. Thus, for an invertible matrix $A$,

    $$
    \kappa_2(A) = \frac{\sigma_{\max}(A)}{\sigma_{\min}(A)}
    $$

    where $\sigma_{\max}(A)$ is the largest singular value and $\sigma_{\min}(A)$ is the smallest singular value. This ratio can be interpreted as the eccentricity of the hyperellipse.

When $A$ is non-invertible matrix, the condition number is generalized using the pseudoinverse: $\kappa(A) = \norm{A} \norm{A^+}$.

# Cholesky Factorization

A Hermitian matrix is a square matrix $A$ that satisfies $A = A^*$.

The Cholesky factorization states that every Hermitian positive definite matrix $A \in \mathbb{C}^{m \times m}$ has a unique decomposition $A = R^* R$, where $R$ is an upper triangular matrix with positive diagonal entries.

The factorization can be shown recursively by partitioning $A$:

$$
A = \begin{bmatrix}
a_{11} & w^* \\
w & K
\end{bmatrix}
$$

If $A$ is positive definite, $a_{11} > 0$, and $K - ww^*/a_{11}$ (the Schur complement of $a_{11}$) is also positive definite. One step of the factorization is:

$$
A = \begin{bmatrix}
\sqrt{a_{11}} & 0 \\
w/\sqrt{a_{11}} & I
\end{bmatrix}
\begin{bmatrix}
1 & 0 \\
0 & K-ww^*/a_{11}
\end{bmatrix}
\begin{bmatrix}
\sqrt{a_{11}} & w^*/\sqrt{a_{11}} \\
0 & I
\end{bmatrix}
$$

Continuing this block factorization on $K-ww^*/a_{11}$ yields the upper triangular matrix $R$.
