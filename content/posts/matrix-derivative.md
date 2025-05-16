---
title: Matrix Derivatives
date: 2019-08-01
lastmod: 2025-05-14
categories:
- Study
tags:
- CS
- Math
---

Notes on doing derivatives w.r.t. matrix/vector.

Based on [matrix vector derivatives for machine learning.pdf](https://github.com/soloice/Matrix_Derivatives/blob/master/matrix%20vector%20derivatives%20for%20machine%20learning.pdf)

Another good resource is [matrixcookbook.pdf](https://www.math.uwaterloo.ca/~hwolkowi/matrixcookbook.pdf)

<!--more-->

---

# Definition

- $f$: real-value function.
- Bold lowercase letter ($\mathbf{x}, \mathbf{y}, \mathbf{z}$): Vector.
- Uppercase letter ($X, Y, Z$): Matrix.

# Properties

- $\nabla_x f = (\nabla_{x^T} f)^T$.
- $\DeclareMathOperator{\tr}{tr}\delta f \approx \sum_{i, j}\left(\nabla_{X} f\right)_{i j}(\delta X)_{i j}=\tr\left((\nabla_X f)^{T} \delta X\right)$
- If $y = f(\mathbf{u}), \mathbf{u}=\mathbf{g}(\mathbf{x})$, then $\displaystyle \frac{\partial f}{\partial \mathbf{x}}=\left(\frac{\partial \mathbf{u}}{\partial \mathbf{x}}\right)^{T} \frac{\partial f}{\partial \mathbf{u}}$.

## Vector

All taking derivative w.r.t. $\mathbf{x}$.

- $\nabla A \mathbf{x}=A$
- $\nabla \left(\mathbf{a}^{\mathrm{T}} \mathbf{x}\right)=\mathbf{a}$
- $\require{mathtools}\DeclarePairedDelimiters\norm{\lVert}{\rVert}\nabla\norm{\mathbf{x}}_{2}^{2}=\nabla\left(\mathbf{x}^{\mathbf{T}} \mathbf{x}\right)=2 \mathbf{x}$
- $\nabla\left(\mathbf{x}^{T} A \mathbf{x}\right)=\left(A+A^{T}\right) \mathbf{x}$
- $\nabla\left(\mathbf{u}^{\mathrm{T}} \mathbf{v}\right)=\left(\nabla_{\mathbf{x}} \mathbf{u}\right)^{T} \mathbf{v}+\left(\nabla_{\mathbf{x}} \mathbf{v}\right)^{T} \mathbf{u}$
- $\nabla (\alpha(\mathbf{x}) \mathbf{f}(\mathbf{x}))=\mathbf{f}(\mathbf{x}) \nabla_{\mathbf{x}^{\mathrm{T}}} \alpha(\mathbf{x})+\alpha(\mathbf{x}) \nabla_{\mathbf{x}} \mathbf{f}(\mathbf{x})$

## Trace

Cyclic property: $\tr\left(A_{1} A_{2} \cdots A_{n}\right)=\tr\left(A_{2} A_{3} \cdots A_{n} A_{1}\right)=\cdots=\tr\left(A_{n-1} A_{n} A_{1} \cdots A_{n-2}\right)=\tr\left(A_{n} A_{1} \cdots A_{n-2} A_{n-1}\right)$

Frobenius norm: $\norm{A}_F^2 = \tr(A^T A)$

- $\nabla \tr\left(A^{T} X\right)=\nabla \tr\left(A X^{T}\right)=A$, $\nabla \tr(A X)=\nabla \tr(X A)=A^{T}$
- $\nabla \tr\left(X A X^{T} B\right)=B^{T} X A^{T}+B X A$
- $\nabla \mathbf{a}^{T} X \mathbf{b}=\mathbf{a} \mathbf{b}^{T}$
- $\nabla \mathbf{a}^{T} X^{T} X \mathbf{a}=2 X \mathbf{a} \mathbf{a}^{T}$
- $\nabla \det(X)=\det(X) \left(X^{-1}\right)^{T}$

## Matrix

- If $y = f(U)$ and $U = G(X)$, then $\displaystyle \frac{\partial y}{\partial x_{i j}}=\tr\left(\left(\frac{\partial y}{\partial U}\right)^{T} \frac{\partial U}{\partial x_{i j}}\right)$.
- If $f(Y): \mathbb{R}^{m\times p} \to \mathbb{R}$ and $Y = AX + B$, then $\nabla_{X} f(A X+B)=A^{T} \nabla_{Y} f$.
- $\nabla_{\mathbf{x}}^{2} f(A \mathbf{x}+\mathbf{b})=A^{T}\left(\nabla_{\mathbf{y}}^{2} f\right) A$.
- $\nabla_{X} f(X C+D)=\left(\nabla_{Y} f\right) C^{T}$.

# Computational Graph

For batch normalization:

$$
\begin{align*}
\mu_B &\gets \frac{1}{m} \sum_{i=1}^m x_i \\
\sigma_B^2 &\gets \frac{1}{m} \sum_{i=1}^m (x_i - \mu_B)^2 \\
\hat{x}_i &\gets \frac{x_i - \mu_B}{\sqrt{\sigma_B^2 + \epsilon}} \\
y_i &\gets \gamma \hat{x}_i + \beta
\end{align*}
$$

Draw the graph

![Computation Graph](https://i.imgur.com/uKI55DO.png)

- For $\hat{x}_i$ there is only one path $\hat{x}_i \to y_i \to l$. So $\displaystyle \frac{\partial l}{\partial \hat{x}_i} = \frac{\partial l}{\partial y_i} \frac{\partial y_i}{\partial \hat{x}_i} = \frac{\partial l}{\partial y_i} \gamma$.
- For $\gamma$ there are $m$ paths $\forall i, \gamma \to y_i \to l$. So $\displaystyle \frac{\partial l}{\partial \gamma} = \sum_i \frac{\partial l}{\partial y_i} \frac{\partial y_i}{\partial \gamma} = \sum_i \frac{\partial l}{\partial y_i} \hat{x}_i$.
- For $\beta$, similar to $\gamma$. So $\displaystyle \frac{\partial l}{\partial \beta} = \sum_i \frac{\partial l}{\partial y_i} \frac{\partial y_i}{\partial \beta} = \sum_i \frac{\partial l}{\partial y_i}$.
- For $\sigma_B^2$, there are $m$ paths. So $\displaystyle \frac{\partial l}{\partial \sigma_B^2} = \sum_i \frac{\partial l}{\partial \hat{x}_i} \frac{\partial \hat{x}_i}{\partial \sigma_B^2} = \sum_i \frac{\partial l}{\partial \hat{x}_i} \cdot -\frac{1}{2} (x_i - \mu_B) (\sigma_B^2 + \epsilon)^{-3/2}$.
- For $\mu_B$, there are $2m$ paths $\forall i, \mu_B \to \hat{x}_i \to l, \mu_B \to \sigma_B^2 \to l$. So $\displaystyle \frac{\partial l}{\partial \mu_B} = \sum_i \left( \frac{\partial l}{\partial \hat{x}_i} \frac{\partial \hat{x}_i}{\partial \mu_B} + \frac{\partial l}{\partial \sigma_B^2} \frac{\partial \sigma_B^2}{\partial \mu_B}\right) = \sum_i \frac{\partial l}{\partial \hat{x}_i} \frac{-1}{\sqrt{\sigma_B^2 + \epsilon}} + \sum_i \frac{\partial l}{\partial \sigma_B^2} \sum_j \frac{2}{m} (x_j - \mu_B) = \sum_i \frac{\partial l}{\partial \hat{x}_i} \frac{-1}{\sqrt{\sigma_B^2 + \epsilon}}$.
- For $x_i$, there are $3$ paths $x_i \to \hat{x}_i \to l, x_i \to \sigma_B^2 \to l, x_i \to \mu_B \to l$. So $\displaystyle \frac{\partial l}{\partial x_i} = \frac{\partial l}{\partial \hat{x}_i} \frac{\partial \hat{x}_i}{\partial x_i} + \frac{\partial l}{\partial \sigma_B^2}\frac{\partial \sigma_B^2}{\partial x_i} + \frac{\partial l}{\partial \mu_B} \frac{\partial \mu_B}{\partial x_i} = \frac{\partial l}{\partial \hat{x}_i} \frac{1}{\sqrt{\sigma_B^2 + \epsilon}} + \frac{\partial l}{\partial \sigma_B^2}\frac{2}{m}(x_i - \mu_B) + \frac{\partial l}{\partial \mu_B} \frac{1}{m}$.

# Example

## Least Squares

Expand:

$$
\begin{split}
\nabla_{\mathbf{x}}\norm{A \mathbf{x}-\mathbf{b}}_{2}^{2} &= \nabla_{\mathbf{x}}(A \mathbf{x}-\mathbf{b})^{T}(A \mathbf{x}-\mathbf{b}) \\
&=\nabla_{\mathbf{x}}\left(\mathbf{x}^{T} A^{T} A \mathbf{x}\right)-2 \nabla_{\mathbf{x}}\left(\mathbf{b}^{T} A \mathbf{x}\right) \\
&= 2 A^{T} A \mathbf{x}-2 A^{T} \mathbf{b} \\
&= 2 A^{T}(A \mathbf{x}-\mathbf{b})
\end{split}
$$

Use linear transformation form:

$$
\begin{split}
\nabla_{\mathbf{x}} \norm{A \mathbf{x}-\mathbf{b} }_{2}^{2} &= A^{T} \nabla_{A \mathbf{x}-\mathbf{b}}\norm{A \mathbf{x}-\mathbf{b}}_{2}^{2} \\
&= A^{T}(2(A \mathbf{x}-\mathbf{b})) \\
&= 2 A^{T}(A \mathbf{x}-\mathbf{b})
\end{split}
$$

## Frobenius Norm

Use trace:

$$
\begin{split}
\nabla_X\norm*{X A^{T}-B}_{F}^{2} &= \nabla_X \tr\left(\left(X A^{T}-B\right)^{T}\left(X A^{T}-B\right)\right) \\
&= \nabla_X \left(\tr\left(A X^{T} X A^{T}\right)-2 \tr\left(A X^{T} B\right)+\tr\left(B^{T} B\right) \right) \\
&= 2 XA^TA - 2BA \\
&= 2(XA^T - B)A
\end{split}
$$

Use linear transformation form:

$$
\begin{split}
\nabla_X\norm*{X A^{T}-B}_{F}^{2} &= \nabla_X\norm*{A X^{T}-B^{T}}_{F}^{2} \\
&=  \left(\nabla_{X^{T}}\norm*{A X^{T}-B^{T}}_{F}^{2}\right)^{T} \\
&=  \left(A^{T}\left(2\left(A X^{T}-B^{T}\right)\right)\right)^{T} \\
&= 2\left(X A^{T}-B\right) A
\end{split}
$$

## PRML

Calculate the gradient:

$$
f(W)=\ln p(T | X, W, \beta)=\text{const}-\frac{\beta}{2} \sum_{n}\norm*{\mathbf{t}_{n}-W^{T} \phi\left(\mathbf{x}_{n}\right)}_{2}^{2}
$$

Use F-norm: the sum of 2-norm square of vectors equals the F-norm square of a big matrix.

$$
\begin{split}
\nabla_W f &= \nabla_W\left( -\frac{\beta}{2} \sum_{n}\norm*{\mathbf{t}_{n}-W^{T} \phi\left(\mathbf{x}_{n}\right)}_{2}^{2} \right) \\
&= -\frac{\beta}{2} \nabla_W \norm{T^T - W^T \Phi^T}_F^2 \\
&= -\frac{\beta}{2} \nabla_W \norm{\Phi W - T}_F^2 \\
&= -\frac{\beta}{2} \Phi^T(2 (\Phi W - T)) \\
&= -\beta \Phi^T (\Phi W - T)
\end{split}
$$

Use inner product: this method is cumbersome but more general.

$$
\begin{split}
\nabla_W f &= \nabla_W\left( -\frac{\beta}{2} \sum_{n}\norm*{\mathbf{t}_{n}-W^{T} \phi\left(\mathbf{x}_{n}\right)}_{2}^{2} \right) \\
&= -\frac{\beta}{2} \nabla_W \left( \sum_n(\mathbf{t}_n - W^T \phi(\mathbf{x}_n))^T (\mathbf{t}_n - W^T \phi(\mathbf{x}_n)) \right) \\
&= -\frac{\beta}{2} \sum_n \left( -2\phi(\mathbf{x}_n) \mathbf{t}_n^T + 2\phi(\mathbf{x}_n)\phi(\mathbf{x}_n)^T W \right) \\
&= -\beta \sum_n \phi(\mathbf{x}_n) \left( -\mathbf{t}_n^T + \phi(\mathbf{x}_n)^T W \right) \\
&= -\beta \Phi^T(\Phi^T W - T)
\end{split}
$$

where $\Phi^T = (\phi(\mathbf{x}_1), \dots, \phi(\mathbf{x}_n))$.

## RNN

Given the state equation, calculate the derivative of loss function $l$ w.r.t. $W$.

$$
\mathbf{h}_t = W f(\mathbf{h}_{t-1}) + U \mathbf{x}_t + \mathbf{b}
$$

Since $l = \sum_t l_t$, we only calculate the derivative of $l_t$ w.r.t. $W$.

$$
\begin{split}
\frac{\partial l_t}{\partial W} &= \sum_{k=1}^t \frac{\partial l_t}{\partial \mathbf{h}_k} \frac{\partial \mathbf{h}_k}{\partial W} \\
&= \sum_{k=1}^t \frac{\partial l_t}{\partial \mathbf{h}_k} (f(\mathbf{h}_{k-1}))^T \\
&= \sum_{k=1}^t\left( f(\mathbf{h}_{k-1}) \frac{\partial l_t}{\partial \mathbf{h}_k^T} \right)
\end{split}
$$

and

$$
\begin{split}
\frac{\partial l_t}{\partial \mathbf{h}_k^T} &= \frac{\partial l_t}{\partial \mathbf{h}_t^T} \frac{\partial \mathbf{h}_t}{\partial \mathbf{h}_{t-1}^T}\dots \frac{\partial \mathbf{h}_{k+1}}{\partial \mathbf{h}_{k}^T} \\
&= \frac{\partial l_t}{\partial \mathbf{h}_t^T} W \operatorname{diag}\big(f'(\mathbf{h}_{t-1})\big)\cdots W \operatorname{diag}(f'(\mathbf{h}_{k}))
\end{split}
$$

Plug it in to get the final equation.

## Autoencoder with Tied Weights

Calculate the gradient: $\sigma(\cdot)$ is element-wise sigmoid function.

$$
f(W) = l(\mathbf{b}_2 + W^T \sigma(W\mathbf{x} + \mathbf{b}_1))
$$

Treat it like $\nabla_W f = \nabla_W l(\mathbf{b}_2 + W^T \sigma(W_c\mathbf{x} + \mathbf{b}_1)) + \nabla_W l(\mathbf{b}_2 + W_c^T \sigma(W\mathbf{x} + \mathbf{b}_1))$, where $W_c$ is treated as constant in each term.

The first term:

$$
\begin{split}
\nabla_W l(\mathbf{b}_2 + W^T \sigma(W_c\mathbf{x} + \mathbf{b}_1)) & = \left( \nabla_{W^T} l(\mathbf{b}_2 + W^T \sigma(W_c\mathbf{x} + \mathbf{b}_1)) \right)^T \\
&= \left( \nabla_{\mathbf{t}}l(t) \cdot \sigma(W_c\mathbf{x} + \mathbf{b}_1)^T \right)^T \\
&= \sigma(W_c\mathbf{x} + \mathbf{b}_1) (\nabla_{\mathbf{t}}l(\mathbf{t}))^T
\end{split}
$$

The second term:

$$
\begin{split}
\nabla_W l(\mathbf{b}_2 + W_c^T \sigma(W\mathbf{x} + \mathbf{b}_1)) &= \nabla_{\mathbf{u}} l(\mathbf{b}_2 + W_c^T \sigma(\mathbf{u})) \mathbf{x}^T \\
&= \nabla_{\mathbf{u}^T} l(\mathbf{b}_2 + W_c^T \sigma(\mathbf{u}))^T \mathbf{x}^T \\
&= \left( \nabla_{\mathbf{t}^T} l(\mathbf{t})\frac{\partial \mathbf{t}}{\partial \mathbf{v}} \frac{\partial \mathbf{v}}{\partial \mathbf{u}} \right)^T \mathbf{x}^T \\
&= \left( \nabla_{\mathbf{t}^T} l(\mathbf{t}) W_c^T \operatorname{diag}(\sigma'(\mathbf{u})) \right)^T \mathbf{x}^T \\
&= \operatorname{diag}(\sigma'(\mathbf{u})) W_c \nabla_\mathbf{t} l(\mathbf{t}) \mathbf{x}^T
\end{split}
$$
