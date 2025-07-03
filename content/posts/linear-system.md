---
title: Linear System
date: 2018-12-20
lastmod: 2025-04-29
categories:
- Study
tags:
- UW
- Robotics
---

ME 547 Review

<!--more-->

---

# State-Space Model

$$
\begin{gather*}
\dot{X}=AX+Bu \\
Y=CX+Du
\end{gather*}
$$

Transfer function

$$
G(s)=\frac{Y(s)}{U(s)}=C(sI-A)^{-1}B+D
$$

Impulse response

$$
g(t)=\mathcal{L}^{-1}[G(s)]
$$

Output

$$
\begin{gather*}
y(t)=Ce^{At}X(0)+\int_0^t g(t-\tau)u(\tau)d\tau \\
y(t)=Ce^{A(t-t_0)}X(0)+\int_{t_0}^t g(t-\tau)u(\tau)d\tau
\end{gather*}
$$

where

$$
e^{At}=\mathcal{L}^{-1}[(sI-A)^{-1}]
$$

# Matrix Exponential

## Diagonalizable

If $A$ is diagonalizable: $A=QDQ^{-1}$, then $A^k=QD^kQ^{-1}$. Using Taylor series, we can get

$$
e^{At}=Q\begin{bmatrix}
e^{\lambda_1 t} & & \\
& \ddots & \\
& & e^{\lambda_n t}
\end{bmatrix}Q^{-1}
$$

where $\lambda_1, \cdots, \lambda_n$ are the eigenvalues of $A$.

## Jordan Normal Form

If $A$ is not diagonalizable, it can be transformed to the Jordan normal form.

If $\lambda_i$ has multiplicity $k$, then there are $n-\operatorname{rank}(A-\lambda_iI)$ Jordan blocks associated with $\lambda_i$. For example, if $\lambda_i$ has multiplicity 3, the matrix can have either 1 or 2 Jordan blocks:

$$
\begin{pmatrix}
\lambda_i & 1 & 0 \\
0 & \lambda_i &1 \\
0 & 0 & \lambda_i
\end{pmatrix} \qquad
\begin{pmatrix}
\lambda_i & 0 & 0 \\
0 & \lambda_i &1 \\
0 & 0 & \lambda_i
\end{pmatrix}
$$

How to find $Q$ with the example of 1 Jordan block:

$$
\begin{cases}
(A-\lambda_iI)q_1=0 \\
(A-\lambda_iI)q_2=q_1 \\
(A-\lambda_iI)q_3=q_2
\end{cases}
$$

Let $A_i$ be a size $n$ Jordan block associated with $\lambda_{i}$, then we have $(A_i-\lambda_{i} I)^n=0$.

Using this property, the Taylor series becomes a finite sum:

$$
e^{At}=e^{\lambda t + (A -\lambda I)t} =  e^{\lambda t} \left[ I+t(A-\lambda I)+\frac{t^2}{2}(A-\lambda I)^2 +\cdots + \frac{t^{n-1}}{(n-1)!}(A-\lambda I)^{n-1} \right]
$$

# Time-Variant System

Given $n$ independent initial states, find the fundamental matrix of $\dot{X}=AX$

$$
\hat{X}=[X_1(t),X_2(t),\dots,X_n(t)]
$$

then form the state-transition matrix

$$
\Phi(t,t_0)=\hat{X}(t)\hat{X}^{-1}(t_0)
$$

which satisfies

$$
\begin{dcases}
\frac{\partial}{\partial t}\Phi(t,t_0)=A(t)\Phi(t,t_0) \\
\Phi(t_0,t_0)=I
\end{dcases}
$$

The solution is

$$
\begin{align*}
X&=\Phi(t,t_0)X(t_0)+\int_{t_0}^t \Phi(t,\tau)B(\tau)U(\tau)d\tau \\
Y&=C(t)X(t)+D(t)U(t) \\
&=C(t)\Phi(t,t_0)X(t_0)+\int_{t_0}^t [C(t)\Phi(t,\tau)B(\tau)+D(t)\delta(t-\tau)]U(\tau)d\tau
\end{align*}
$$

If $X(t_0)=0$, then

$$
Y=\int_{t_0}^t G(t,\tau)U(\tau)d\tau
$$

which is the impulse response for an LTV system, compared to an LTI system:

$$
y=\int_{t_0}^t [Ce^{A(t-\tau)}B+D\delta(t-\tau)]u(\tau)d\tau
$$

# Discrete Time System

$$
\begin{align*}
X_{k+1}&=(I+AT)X_{k}+BTu_{k} \\
y_{k}&=CX_{k}+Du_{k}
\end{align*}
$$

With a zero-order hold, we have another discretization. From

$$
X(t)=e^{A(t-t_0)}X(t_0)+\int_{t_0}^t e^{A(t-\tau)}Bu(\tau)d\tau
$$

setting $t=(k+1)T$ and $t_0=kT$ gives

$$
\begin{split}
X_{k+1}&=e^{AT}X_{k}+\int_0^T e^{A\tau}d\tau Bu_{k} \\
&=A_dX_{k}+B_du_{k}
\end{split}
$$

From this, the solution for $X_{k}$ is

$$
X_{k}=A_d^kX_{0}+\sum_{m=0}^{k-1}A_d^{(k-1)-m}B_du_{m}
$$

# Stability

Transfer function does not change under transformation.

- equivalent system $\implies$ same TF
- same TF $\centernot\implies$ equivalent system

BIBO stable and steady state response

1. For a constant input $u(t)=a$:

    $$
    \lim_{t\to\infty} y(t)=aG(s)|_{s=0}
    $$

2. For a sinusoidal input $u(t)=A\cos(\omega t)$:

    $$
    \lim_{t\to\infty} y(t)=AM\cos(\omega t+\varphi)
    $$

    where

    $$
    \begin{dcases}
    M =\lvert G(s) \rvert_{s=j\omega}  \\
    \varphi =\angle G(s)|_{s=j\omega}
    \end{dcases}
    $$

|                           |                                                                                                                        LTI                                                                                                                         |                                                                                 LTV                                                                                  |
| :-----------------------: | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: | :------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
|      BIBO stable      | $\int_0^\infty\lvert g(t) \rvert dt<\infty$<br>$g(t)\to 0$ as $t\to\infty$ is a necessary but not sufficient condition.<br>All poles of the transfer function have negative real parts.<br>BIBO stable $\centernot\implies$ asymptotically stable. |                                  $\int_{t_0}^t \lVert C(t)\Phi(t,\tau)B(\tau) \rVert d\tau<\infty$ and $\lVert D(t) \rVert<\infty$.                                  |
|   marginally stable   |                                                                        Eigenvalues of $A$ have non-positive real parts; those with zero real part must be simple roots.<br>                                                                        |                                              $\lVert \Phi(t,t_0) \rVert<\infty$ for all $t_0,t$ such that $t>t_0$.<br>                                               |
| asymptotically stable |                                                                           Eigenvalues of $A$ have negative real parts.<br>Asymptotically stable $\implies$ BIBO stable.                                                                            | $\lVert \Phi(t,t_0) \rVert \to 0 \text{ as } t\to\infty$.<br>Invariant under Lyapunov transformation.<br>Asymptotically stable $\centernot\implies$ BIBO stable. |

Discrete time systems behave like continuous time systems, except:

1. $g_k$ is absolutely summable $\iff$ $g(t)$ is absolutely integrable.
2. All poles have magnitude less than 1 $\iff$ all poles of the continuous time system have negative real parts.
3. Eigenvalues of $A_d$ have magnitude $\leq 1$  $\iff$ Eigenvalues of $A$ have non-positive real parts.

# Controllability and Observability

Controllability: For any initial state $x_{0}$ and any target state $x_f$, there exists an input $u(t)$ over a finite time interval $[0, T]$ that transfers $x_{0}$ to $x_f$.

$$
\mathcal{C}=[B,AB,A^2B,\dots,A^{n-1}B]
$$

Observability: The ability to determine the initial state $x_{0}$ from measurements of the output $y(t)$ and input $u(t)$ over a finite time interval.

$$
\mathcal{O}=\begin{bmatrix}
C \\ CA \\ \vdots \\ CA^{n-1}
\end{bmatrix}
$$

The controllability of $(A,B)$ is equivalent to the observability of $(A^T,B^T)$, and vice versa.

# Continuous Time Controllability

$$
W_c(t)=\int_0^t e^{A\tau}BB'e^{A'\tau}d\tau
$$

must be nonsingular for the system to be controllable. The input can be found by

$$
u=-B'e^{A'(t_f-t)}W_c^{-1}(t_f) [e^{At_f}X_0-X_f]
$$

- If $W_c(t)$ is nonsingular for any time $t>0$, it is nonsingular for all time $t>0$.
- The time to achieve state transfer can be arbitrarily small, but may require a large control input.

The controllability Gramian is

$$
W_c=\lim_{t\to\infty} W_c(t)
$$

When $A$ is a Hurwitz matrix (asymptotically stable), the Lyapunov equation can be used to solve for $W_c$:

$$
AW_c+W_cA'=-BB'
$$

**Controllability is invariant under equivalence transformation.**

# Kalman Decomposition

Let $\bar{X}=PX$ then

$$
\begin{gather*}
\bar{A}=PAP^{-1}\qquad \bar{B}=PB\qquad \bar{C}=CP^{-1}\qquad \bar{D}=D \\
\bar{\mathcal{C}}=P\mathcal{C}\qquad \bar{\mathcal{O}}=\mathcal{O}P^{-1}
\end{gather*}
$$

If $\operatorname{rank}(\mathcal{C})=n_1$, choose

$$
P^{-1}=\begin{bmatrix}
q_1 & q_2 & \cdots & q_{n_1} & \cdots & q_n
\end{bmatrix}
$$

where the first $n_1$ columns are independent columns from $\mathcal{C}$, and the remaining $n-n_1$ columns are chosen such that $P$ is invertible. Then we have

$$
\bar{A}=\begin{bmatrix}
\bar{A}_c & \bar{A}_{12} \\
0 & \bar{A}_{\bar{c}}
\end{bmatrix}
\qquad
\bar{B}=\begin{bmatrix}
\bar{B}_c\\
0
\end{bmatrix}
\qquad
\bar{C}=\begin{bmatrix}
\bar{C}_c & \bar{C}_{\bar{c}}
\end{bmatrix}
$$

If $\operatorname{rank}(\mathcal{O})=n_2$, choose

$$
P=\begin{bmatrix}
p_1 \\
p_2 \\
\vdots \\
p_{n_2} \\
\vdots \\
p_n
\end{bmatrix}
$$

to transform the system to

$$
\bar{A}=\begin{bmatrix}
\bar{A}_o & 0 \\
\bar{A}_{21} & \bar{A}_{\bar{o}}
\end{bmatrix}
\qquad
\bar{B}=\begin{bmatrix}
\bar{B}_o \\
\bar{B}_{\bar{o}}
\end{bmatrix}
\qquad
\bar{C}=\begin{bmatrix}
\bar{C}_o & 0
\end{bmatrix}
$$

Combining these two, we can get the Kalman decomposition:

$$
\frac{d}{dt}\bar{X}=\begin{bmatrix}
\bar{A}_{co} & 0 & \bar{A}_{13} & 0 \\
\bar{A}_{21} & \bar{A}_{c\bar{o}} & \bar{A}_{23} & \bar{A}_{24} \\
0 & 0 & \bar{A}_{\bar{c}o} & 0 \\
0 & 0 & \bar{A}_{43} & \bar{A}_{\bar{c}\bar{o}}
\end{bmatrix}
\begin{bmatrix}
\bar{x}_{co} \\
\bar{x}_{c\bar{o}} \\
\bar{x}_{\bar{c}o} \\
\bar{x}_{\bar{c}\bar{o}}
\end{bmatrix}
+
\begin{bmatrix}
\bar{B}_{co} \\
\bar{B}_{c\bar{o}} \\
0 \\
0
\end{bmatrix}u
\\ \
\\
\bar{Y}=\begin{bmatrix}
\bar{C}_{co} & 0 & \bar{C}_{\bar{c}o} & 0
\end{bmatrix}
\bar{X}+Du
$$

The transfer function is determined solely by the controllable and observable subsystem ($\bar{X}_{co}$).

# Jordan Form Criteria

Hautus lemma: if  $[A-\lambda I, B]$ has full row rank for every eigenvalue $\lambda$ of $A$, then the system is controllable.

After the system is transformed into Jordan forms:

- Controllability: the rows of $\bar{B}$ corresponding to the last row of each Jordan block associated with eigenvalue $\lambda_i$ must be linearly independent.
- Observability: the columns of $\bar{C}$ corresponding to the first column of each Jordan block associated with eigenvalue $\lambda_i$ must be linearly independent.

If the system is SISO:

- Controllability: each eigenvalue $\lambda_i$ can only have one Jordan block, and every entry of $\bar{B}$ corresponding to the last row of each Jordan block is non-zero.
- Observability: each eigenvalue $\lambda_i$ can only have one Jordan block, and every entry of $\bar{C}$ corresponding to the first column of each Jordan block is non-zero.

# Sampling

If the continuous time system is controllable, then the discrete time system obtained by zero-order hold is controllable if

$$
\lvert \operatorname{Im}(\lambda_i-\lambda_j) \rvert \ne \frac{2\pi m}{T}, \quad \forall m=1,2,\dots,
$$

when $\operatorname{Re}(\lambda_i-\lambda_j)=0$. Where $T$ is the sampling time.

If the continuous time system is uncontrollable, the discrete time system is also uncontrollable.

# LTV Systems

Jordan form theorem not applicable to LTV system.

Controllability: A system is controllable on $[t_0, t_1]$ if the controllability Gramian is nonsingular

$$
W_c(t_0,t_1)=\int_{t_0}^{t_1}\Phi(t_1,\tau)B(\tau)B'(\tau)\Phi'(t_1,\tau)d\tau
$$

Alternatively, $[A(t), B(t)]$ is controllable on $[t_0, t_f]$ if there exists $t_1 \in [t_0, t_f]$ such that the rank of

$$
[M_0(t_1),\dots,M_{n-1}(t_1)]
$$

is $n$, where

$$
\begin{dcases}
M_0(t)=B(t) \\
M_{m+1}(t)=-A(t)M_m(t)+\frac{d}{dt}M_m(t)
\end{dcases}
$$

Observability: A system is observable on $[t_0, t_1]$ if the observability Gramian is nonsingular.

$$
W_o(t_0,t_1)=\int_{t_0}^{t_1}\Phi'(\tau,t_0)C'(\tau)C(\tau)\Phi(\tau,t_0)d\tau
$$

Alternatively, $[C(t), A(t)]$ is observable on $[t_0, t_f]$ if there exists $t_1 \in [t_0, t_f]$ such that the rank of

$$
\begin{bmatrix}
N_0(t_1)\\
\vdots \\
N_{n-1}(t_1)
\end{bmatrix}
$$

is $n$, where

$$
\begin{dcases}
N_0(t)=C(t) \\
N_{m+1}(t)=N_m(t)A(t)+\frac{d}{dt}N_m(t)
\end{dcases}
$$

Controllability of $[A(t),B(t)]$ is dual to observability of $[-A'(t), C'(t)]$.

# Controllable Form

Let the characteristic polynomial of $A$ (for an $n=4$ system) be $\det(sI-A)=s^4+\alpha_1 s^3+\alpha_2 s^2+\alpha_3 s+\alpha_4$. The system can be transformed using $\bar{X}=PX$ where

$$
P^{-1}= \begin{bmatrix}
B & AB & A^2B & A^3B
\end{bmatrix}
\begin{bmatrix}
1 & \alpha_1 & \alpha_2 & \alpha_3 \\
0 & 1 & \alpha_1 & \alpha_2 \\
0 & 0 & 1 & \alpha_1 \\
0 & 0 & 0 & 1
\end{bmatrix}
$$

into the controllable form

$$
\bar{A}=\begin{bmatrix}
-\alpha_1 & -\alpha_2 & -\alpha_3 & -\alpha_4 \\
1 & 0 & 0 & 0 \\
0 & 1 & 0 & 0 \\
0 & 0 & 1 & 0
\end{bmatrix}
\qquad
\bar{B}=\begin{bmatrix}
1 \\
0 \\
0 \\
0
\end{bmatrix}
\qquad
\bar{C}=\begin{bmatrix}
\beta_1 & \beta_2 & \beta_3 & \beta_4
\end{bmatrix}
$$

The transfer function is

$$
G(s)=\frac{\beta_1s^3+\beta_2s^2+\beta_3s+\beta_4}{s^4+\alpha_1s^3+\alpha_2s^2+\alpha_3s+\alpha_4}
$$

# Pole Placement

1. Find the original and desired characteristic polynomial $\Delta s, \bar{\Delta}s$ to decide the coefficients $\alpha, \bar{\alpha}$
2. Find $P$ that can transform the system into controllable form.
3. Use control law $K=\bar{K}P$ where $\bar{K}=\begin{bmatrix} \bar{\alpha}_1-\alpha_1 & \bar{\alpha}_2-\alpha_2 & \bar{\alpha}_3-\alpha_3 & \bar{\alpha}_4-\alpha_4\end{bmatrix}$

so we have

$$
\begin{gather*}
u(t)=-\bar{K}\bar{X}(t)+r(t)=-KX(t)+r(t) \\
\dot{\bar{X}}=[\bar{A}-\bar{B}\bar{K}]\bar{X}(t)+\bar{B}r(t)
\end{gather*}
$$
