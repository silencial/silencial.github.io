---
title: Linear System
date: 2018-12-20
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

# Finding $e^{At}$

## Diagonalizable

$$
A=QDQ^{-1}
$$

then
$$
A^k=QD^kQ^{-1}
$$

$$
e^{At}=Q\begin{bmatrix}
e^{a_1t} & & \\
 & \ddots & \\
 & & e^{a_nt}
\end{bmatrix}Q^{-1}
$$

## Jordan Forms

$\lambda_i$ has multiplicity $k$, then there are $n-\operatorname{rank}(A-\lambda_iI)$ Jordan block associated with $\lambda_i$. For example, $\lambda_i$ has multiplicity $3$ and $1$ Jordan block, then the matrix is
$$
\begin{pmatrix}
\lambda_i & 1 & 0 \\
0 & \lambda_i &1 \\
0 & 0 & \lambda_i
\end{pmatrix}
$$
When finding $Q$
$$
\require{mathtools}
\require{centernot}
\DeclarePairedDelimiters\norm{\lVert}{\rVert}
\DeclarePairedDelimiters\abs{\lvert}{\rvert}
\DeclareMathOperator{\eig}{eig}
\begin{dcases}
(A-\lambda_iI)q_1=0 \\
(A-\lambda_iI)q_2=q_1 \\
(A-\lambda_iI)q_3=q_2
\end{dcases}
$$

## $e^{At}$ and $A^k$

$A_i$ be a Jordan block associated with $\lambda$. Let $n$ be the size of largest Jordan block. For example, the matrix
$$
\begin{bmatrix}
2 & 0 & 0 \\
0 & 2 & 1 \\
0 & 0 & 2
\end{bmatrix}
$$
have $\lambda=2,n=2$

then $(A_i-\lambda I)^n=0$.

With this equation we can get
$$
\begin{gather*}
e^{At}=e^{\lambda t}I+te^{\lambda t}(A-\lambda I)+\frac{t^2 e^{\lambda t}}{2}(A-\lambda I)^2+\frac{t^3e^{\lambda t}}{3!}(A-\lambda I)^3+\cdots \\
A^k=\lambda^kI+k\lambda^{k-1}(A-\lambda I)+\frac{k(k-1)}{2}(A-\lambda I)^2\lambda^{k-2}+\cdots
\end{gather*}
$$

# Time-Varying System

Given $n$ independent initial states, find the **fundamental matrix** of $\dot{X}=AX$
$$
\hat{X}=[X_1(t),X_2(t),\dots,X_n(t)]
$$
then form the state transition matrix
$$
\Phi(t,t_0)=\hat{X}(t)\hat{X}^{-1}(t_0)
$$
it satisfies
$$
\begin{dcases}
\frac{\partial}{\partial t}\Phi(t,t_0)=A(t)\Phi(t,t_0) \\
\Phi(t_0,t_0)=I
\end{dcases}
$$
The solution
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
which is the impulse response to LTV system, compared to LTI system
$$
y=\int_{t_0}^t [Ce^{A(t-\tau)}B+D\delta(t-\tau)]u(\tau)d\tau
$$

# Discrete-Time System

$$
\begin{align*}
X[k+1]&=(I+AT)X[k]+BTu[k] \\
y[k]&=CX[k]+Du[k]
\end{align*}
$$

With **zero-hold** we have another discretization. From
$$
X(t)=e^{A(t-t_0)}X(t_0)+\int_{t_0}^t e^{A(t-\tau)}Bu(\tau)d\tau
$$
make $t=(k+1)T, t_0=kT$
$$
\begin{split}
X[k+1]&=e^{AT}X[k]+\int_0^T e^{A\tau}d\tau Bu[k] \\
&=A_dX[k]+B_du[k]
\end{split}
$$
From this we have
$$
X[k]=A_d^kX[0]+\sum_{m=0}^{k-1}A_d^{(k-1)-m}B_du[m]
$$

# Stability

Transfer function does not change under transformation.

equivalent system $\implies$ same TF

same TF $\centernot\implies$ equivalent system

BIBO state response

1. $u(t)=a$.
   $$
   \lim_{t\to\infty} y(t)=aG(s)|_{s=0}
   $$

2. $u(t)=A\cos(\omega t)$
   $$
   \lim_{t\to\infty} y(t)=AM\cos(\omega t+\varphi)
   $$
   where
   $$
   \begin{dcases}
   M =\abs{G(s)}_{s=j\omega} \\
   \varphi =\angle G(s)|_{s=j\omega}
   \end{dcases}
   $$

|      | LTI | LTV |
| :--: | :-------: | :--: |
| BIBO | $\int_0^\infty\abs{g(t)}<\infty$<br>RF: all poles on LHP<br> RF: $g(t)\to 0$ as $t\to\infty$<br>$\centernot\implies$ asymptotically stable<br><font color="red">unchanged</font> | $\int_0^\infty\abs{g(t,\tau)}<\infty$<br>$\begin{cases}\norm{D(t)}<\infty \\ \int_{t_0}^t\norm{C(t)\Phi(t,\tau)B(\tau)}d\tau<\infty \end{cases}$<br><font color="red">unchanged</font> |
| marginally | $\eig(A)$ non-positive. $0$ real part are simple root<br><font color="red">unchanged</font> | $\norm{\Phi(t,t_0)}<\infty$ for any $t_0,t$ that $t>t_0$<br><font color="red">unchanged</font> |
| asymptotically | $\eig(A)$ negative $\implies$ BIBO<br><font color="red">unchanged</font> | $\begin{cases}\norm{\Phi(t,t_0)||<\infty \\ \norm\Phi(t,t_0)}\to 0 \text{ as } t\to\infty \end{cases}$ $\centernot\implies$ BIBO<br><font color="red">unchanged under Lyapunov transformation:</font> $P(t),P^{-1}(t)$ bounded |

Discrete system just like Continuous system except

1. $g[k]$ absolute summable $\iff$ $g(t)$ absolute integrable
2. all poles magnitude$<1$ $\iff$ all poles on LHP
3. $\abs{\eig(A)}\le 1$ $\iff$ $\eig(A)$ non-positive

# Controllability & Observability

**Controllability:** For any $x(0)$ and $x(n)$, there exist an input of finite time length that transfer $x(0)$ to $x(n)$.
$$
\mathcal{C}=[B,AB,A^2B,\dots,A^{n-1}B]
$$
**Observability:** Estimate $x(0)$ when $u$ and $y$ are known.
$$
\mathcal{O}=\begin{bmatrix}
C \\ CA \\ \vdots \\ CA^{n-1}
\end{bmatrix}
$$
controllability of $(A,B)$ is the same as the observability of $[A^T,B^T]$ and vice versa.

# Continuous Time

$$
W_c(t)=\int_0^t e^{A\tau}BB'e^{A'\tau}d\tau
$$

has to be non-singular.

Input is
$$
u=-B'e^{A'(t_f-t)}W_c^{-1}(t_f) [e^{At_f}X_0-X_f]
$$
If $W_c(t)$ is nonsingular for any time $t>0$, it is nonsingular for any time $t>0$.

Time can be arbitrarily small, but input could get large.

Controllability grammiar is
$$
W_c=\lim_{t\to\infty} W_c(t)
$$
When $A$ is Hurwitz, Lyapunov equation can be used to solve for $W_c$
$$
AW_c+W_cA'=-BB'
$$
If the system is stable, $W_c$ is invertible $\implies$ $W_c(t)$ is invertible.

$[A-\lambda I, B]$ has full row rank for every eigenvalue $\lambda$ of $A$.

**controllability is invariant under equivalence transformation.**

# Kalman Decomposition

Let $\bar{X}=PX$ then
$$
\begin{gather*}
\bar{A}=PAP^{-1}\qquad \bar{B}=PB\qquad \bar{C}=CP^{-1}\qquad \bar{D}=D \\
\bar{\mathcal{C}}=P\mathcal{C}\qquad \bar{\mathcal{O}}=\mathcal{O}P^{-1}
\end{gather*}
$$
If $\rho(\mathcal{C})=n_1$, choose
$$
P^{-1}=\begin{bmatrix}
q_1 & q_2 & \cdots & q_{n_1} & \cdots & q_n
\end{bmatrix}
$$
where the first $n_1$ column are independent columns from $\mathcal{C}$, the rest $n-n_1$ columns are chosen for $P$ to be invertible. Then we have
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

If $\rho(\mathcal{O})=n_2$, choose
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
change the system to
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
Combine these two we can get
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
Transfer function is only affected by $\bar{X}_{co}$.

# Jordan Block

Controllability: For each $\lambda_i$, the last row of $B$ with each Jordan block should be independent.

Observability: For each $\lambda_i$, the first column of $C$ with each Jordan block should be independent.

If **SISO**, there is only one Jordan block for each $\lambda_i$, so the last row of $B$ should be nonzero, the first column of $C$ should be nonzero.

# Sampling

If the continuous system is controllable, then the discrete system is controllable if
$$
\abs{\operatorname{Im}(\lambda_i-\lambda_j)}\ne \frac{2\pi m}{T}
$$
when $\operatorname{Re}(\lambda_i-\lambda_j)=0$. Where $T$ is the sampling time.

If the continuous system is not uncontrollable, then discrete system is not uncontrollable.

# LTV

Jordan form theorem not applicable to LTV system.

**Controllability:**
$$
W_c(t_0,t_1)=\int_{t_0}^{t_1}\Phi(t_1,\tau)B(\tau)B'(\tau)\Phi'(t_1,\tau)d\tau
$$

$$
\begin{dcases}
M_0(t)=B(t) \\
M_{m+1}(t)=-A(t)M_m(t)+\frac{d}{dt}M_m(t)
\end{dcases}
$$

there exists $t_1>t_0$ that
$$
\operatorname{rank}[M_0(t_1),\dots,M_{n-1}(t_1)]=n
$$
**Observability:**
$$
W_c(t_0,t_1)=\int_{t_0}^{t_1}\Phi'(\tau,t_0)C'(\tau)C(\tau)\Phi(\tau,t_0)d\tau
$$
Jordan form theorem not applicable to LTV system.
$$
\begin{dcases}
N_0(t)=C(t) \\
N_{m+1}(t)=N_m(t)A(t)+\frac{d}{dt}N_m(t)
\end{dcases}
$$
there exists $t_1>t_0$ that
$$
rank\begin{bmatrix}
N_0(t_1)\\
\vdots \\
N_{n-1}(t_1)
\end{bmatrix}
=n
$$
$[A(t),B(t)]$ controllable $\iff$ $[-A'(t),B'(t)]$ observable.

# Controllable Form

Let
$$
P^{-1}=\begin{bmatrix}
B & AB & A^2B & A^3B
\end{bmatrix}
\begin{bmatrix}
1 & \alpha_1 & \alpha_2 & \alpha_3 \\
0 & 1 & \alpha_1 & \alpha_2 \\
0 & 0 & 1 & \alpha_1 \\
0 & 0 & 0 & 1
\end{bmatrix}
$$
where $\alpha$ is the coefficients of $\det(sI-A)=s^4+\alpha_1s^3+\alpha_2s^2+\alpha_3s+\alpha_4$

With $\bar{X}=PX$, then
$$
\bar{A}=\begin{bmatrix}
-\alpha_1 & -\alpha_1 & -\alpha_3 & -\alpha_4 \\
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
This is the **controllable form**, it has transfer function
$$
G(s)=\frac{\beta_1s^3+\beta_2s^2+\beta_3s+\beta_4}{s^4+\alpha_1s^3+\alpha_2s^2+\alpha_3s+\alpha_4}
$$

# Pole Placement

$$
\Delta s\to\alpha\\
\bar{\Delta} s\to\bar{\alpha}
$$

Let
$$
P^{-1}=C\begin{bmatrix}
1 & \alpha_1 & \alpha_2 & \alpha_3 \\
0 & 1 & \alpha_1 & \alpha_2 \\
0 & 0 & 1 & \alpha_1 \\
0 & 0 & 0 & 1
\end{bmatrix}
$$

$$
\begin{split}
K&=\begin{bmatrix}
\bar{\alpha}_1-\alpha_1 & \bar{\alpha}_2-\alpha_2 & \bar{\alpha}_3-\alpha_3 & \bar{\alpha}_4-\alpha_4
\end{bmatrix}
P \\
&=\bar{K}P
\end{split}
$$

Then we have
$$
\begin{gather*}
u(t)=-\bar{K}\bar{X}(t)+r(t)=-KX(t)+r(t) \\
\dot{\bar{X}}=[\bar{A}-\bar{B}\bar{K}]\bar{X}(t)+\bar{B}r(t)
\end{gather*}
$$
