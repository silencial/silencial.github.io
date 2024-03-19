---
title: Applied Optimal Control
date: 2020-12-29
lastmod: 2021-02-13
categories:
- Study
tags:
- JHU
- Robotics
---


[EN530.603](https://asco.lcsr.jhu.edu/en530-603-f2020-applied-optimal-control/) Review.

<!--more-->

---

# Unconstrained Optimization

## Optimality Conditions

### Necessary Optimality Conditions

$$
\begin{align*}
&\nabla f = 0 && \text{First-order Necessary Conditions} \\
&\nabla^2 f \ge 0 && \text{Second-order Necessary Conditions}
\end{align*}
$$

### Sufficient Optimality Conditions

$$
\nabla f(x^*) = 0, \quad \nabla^2 f(x^*) > 0
$$

## Numerical Solution

$$
x^{k+1} = x^k + \alpha^k d^k, \quad k=0,1,\dots
$$

where $d^k$ is called the *search direction* and $\alpha^k > 0$ is called the *stepsize*. The most common methods for finding $\alpha^k$ and $d^k$ are gradient-based.

1. Choose direction $d^k$ so that whenever $\nabla f(x^k) \ne 0$ we have
$$
\nabla f(x^k)^T d^k < 0
$$

2. Choose stepsize $\alpha^k > 0$ so that
$$
f(x^k + \alpha d^k) < f(x^k)
$$

### Search Direction

Many gradient methods are specified in the form
$$
x^{k+1} = x^k - \alpha^k D^k \nabla f(x^k)
$$
where $D^k \in \mathbb{S}^n_{++}$

**Steepest Descent**:
$$
D^k = I
$$
**Newton's Method**:
$$
D^k = [\partial^2 f(x^k)]^{-1}
$$
**Gauss-Newton Method**: When the cost has a special *least squares form*
$$
f(x) = \frac{1}{2} \|g(x)\|^2
$$
we can choose
$$
D^k = \left[ \nabla g(x^k) \nabla g(x^k)^T \right]^{-1}
$$
**Conjugate-Gradient Method**: Choose linearly independent (conjugate) search directions $d^k$
$$
d^k = -\nabla f(x^k) + \beta d^{k-1}
$$
The most common way to compute $\beta^k$ is
$$
\beta^{k}=\frac{\nabla f\left(x^{k}\right)^{T}\left(\nabla f\left(x^{k}\right)-\nabla f\left(x^{k-1}\right)\right)}{\nabla f\left(x^{k-1}\right)^{T} \nabla f\left(x^{k-1}\right)}
$$

### Stepsize

**Minimization Rule**: Choose $\alpha^k \in [0,s]$ so that $f$ is minimized
$$
f(x^k + \alpha^k d^k) = \min_{\alpha \in [0, s]} f(x^k + \alpha d^k)
$$
**Successive Stepsize Reduction - Armijo Rule**:

1. Choose $s>0, 0<\beta<1, 0<\sigma<1$
2. Increase: $m = 0, 1, \dots$
3. Until: $f(x^k) - f(x^k+\beta^m s d^k) \ge -\sigma \beta^m s \nabla f(x^k)^T d^k$

where $\beta$ is the rate of decrease and $\sigma$ is the acceptance ratio

### Regularized Newton Method

Drawbacks of pure Newton's method:

- The inverse Hessian $[\nabla^2 f(x)]^{-1}$ might not be computable
- When $\nabla^2 f(x) \nsucc 0$ the method can be attracted by global maxima

Add a *regularizing* term to the Hessian and solve the system
$$
(\nabla^2 f(x^k) + \Delta^k) d^k = -\nabla f(x^k)
$$
where $\Delta^k$ is chosen so that $\nabla^2 f(x^k) + \Delta^k \succ 0$

In **trust-region** methods one sets
$$
\Delta^k = \delta^k I
$$
and $(\nabla^2 f(x^k) + \delta^k I) d^k = -\nabla f(x^k)$ is equivalent to
$$
\DeclareMathOperator*{\argmin}{arg\,min\,}
\DeclareMathOperator*{\argmax}{arg\,max\,}
d^k \in \argmin_{\|d\| \le \gamma^k} f^k(d)
$$

# Constrained Optimization

## Equality Constraints

Minimize $L(x): \mathbb{R}^n \to \mathbb{R}$, subject to $k$ constraints $f(x) = 0$.

### Necessary Optimality Conditions

Let $H(x, \lambda) = L(x) + \lambda^T f(x)$ be the **Hamiltonian**, where $\lambda$ are scalars and called the *Lagrangian multipliers*
$$
\begin{align*}
&\nabla_x H(x^*, \lambda^*) = 0 & &\text{First-order Necessary Condtions} \\
&dx^T [\nabla_{xx} H(x^*, \lambda^*)] dx \ge 0 & &\text{Secon-order Necessary Condtions}
\end{align*}
$$
Note that $dx$ is not arbitrary, i.e. we require $\nabla f_i(x^*)^T dx = 0$

**Geometric meaning**: any feasible $dx$ must be spanned by the gradients $\nabla f_i$. At an optimum $x^*$ we must also have $\nabla L (x^*)^T dx = 0$, so that $\nabla L(x)$ must be spanned by gradients as well:
$$
\nabla L(x^*) = \sum_{i=1}^n \lambda_i \nabla f_i(x^*)
$$

### Sufficient Optimality Conditions

$$
\nabla_x H = 0, \quad dx^T [\nabla_{xx} H] dx > 0
$$

## Inequality Constraints

Minimize $L(x)$ subject to $f(x) \le 0$.

Now the Lagrangian multipliers have to satisfy
$$
\lambda = \begin{cases}
\ge 0, & f(x)=0 \\
=0, & f(x) < 0
\end{cases}
$$

**Geometric meaning**: the gradient of $L$ w.r.t. $x$ at a minimum must be pointed in a way that decrease of $L$ can only come by violating the constraints:
$$
-\nabla L = \sum_{i=1}^n \lambda_i \nabla f_i \quad (\lambda_i \ge 0)
$$

# Trajectory Optimization

Solving the optimal control problems
$$
\begin{align*}
&\text{minimize} & &J(x(t), u(t), t_f) = \int_{t_0}^{t_f} L(x(t), u(t), t) dt \\
&\text{subject to} & &\dot{x}= f(x,u,t) \\
& & &\text{other constratins}
\end{align*}
$$
The cost $J$ is called a **functional**, i.e. a function of functions.
$$
\text{differential of a function}\quad \nabla g = 0 \quad \iff \quad \text{variation of a functional}\quad \delta J = 0
$$

## Euler-Lagrange Equation

Consider
$$
J = \int_{t_0}^{t_f} g(x(t), \dot{x}(t)) dt
$$
given $x(t_0)$

The variation is
$$
\delta J=\int_{t_{0}}^{t_{f}}\left[g_{x}(x, \dot{x}) \delta x-\frac{d}{d t} g_{\dot{x}}(x, \dot{x}) \delta x\right] dt+g_{\dot{x}}(x(t_{f}), \dot{x}(t_{f})) \delta x(t_{f})
$$
**Fixed boundary conditions**: if $x(t_f)$ is given, $\delta J=0$ is equivalent to
$$
g_x(x, \dot{x}) - \frac{d}{dt}g_{\dot{x}}(x, \dot{x}) = 0
$$
**Free boundary conditions**: if $x(t_f)$ is not fixed, $\delta J=0$ is equivalent to
$$
\begin{gather*}
g_x(x, \dot{x}) - \frac{d}{dt}g_{\dot{x}}(x, \dot{x}) = 0 \\
g_{\dot{x}}(x(t_f), \dot{x}(t_f)) = 0
\end{gather*}
$$

## Free Final-Time

When $t_f$ is allowed to vary
$$
\begin{align*}
\delta J=& \int_{t_{0}}^{t_{f}}\left[g_{x}(x^{*}, \dot{x}^{*}, t) \delta x-\frac{d}{d t} g_{\dot{x}}(x^{*}, \dot{x}^{*}, t) \delta x\right] d t \\
&+g_{\dot{x}}(x^{*}(t_{f}), \dot{x}^{*}(t_{f}), t) \delta x(t_{f})+g(x^{*}(t_{f}), \dot{x}^{*}(t_{f}), t_{f}) \delta t_{f} \\
=& \int_{t_{0}}^{t_{f}}\left[g_{x}(x^{*}, \dot{x}^{*}, t) \delta x-\frac{d}{d t} g_{\dot{x}}(x^{*}, \dot{x}^{*}, t) \delta x\right] d t \\
&+\left(g_{\dot{x}}(x^{*}, \dot{x}^{*}, t) \delta x_{f}+ [g(x^{*}, \dot{x}^{*}, t_{f}) - g_{\dot{x}}(x^{*}, \dot{x}^{*}, t_{f})\dot{x}] \delta t_{f}\right)_{t=t_f} \\
\end{align*}
$$

where $\delta x_f$ is the total space-time variation defined as
$$
\delta x_f = \delta x(t_f) + \dot{x}(t_f)\delta t_f
$$
**Unrelated $t_f$ and $x(t_f)$**: the additional necessary conditions are
$$
\begin{gather*}
g_{\dot{x}}(t_f) = 0 \\
g(t_f) = 0
\end{gather*}
$$
**Function related**: when $x(t_f) = \Theta(t_f)$, then $\delta x_f = \frac{d\Theta}{dt} \delta t_f$, the additional necessary conditions becomes
$$
g_{\dot{x}}(t_f) \left[\frac{d\Theta}{dt} - \dot{x}^*\right]_{t=t_f} + g(t_f) = 0
$$

## Differential Constraints

$$
\begin{align*}
&\text{minimize} &  &J = \int_{t_0}^{t_f} g(x, \dot{x}, t) dt \\
&\text{subject to} & &f(x(t), \dot{x}(t), t) = 0
\end{align*}
$$

where $t_f$ and $x(t_f)$ are fixed. Define the Lagrangian multipliers $\lambda : [t_0, t_f] \to \mathbb{R}^n$ and the augmented cost
$$
\begin{gather*}
J_a = \int_{t_0}^{t_f} g_a dt \\
g_a = g + \lambda^T f
\end{gather*}
$$
The necessary optimality conditions are the Euler-Lagrange equations w.r.t. $g_a$

## General Boundary Constraints

$$
\begin{align*}
&\text{minimize} & &J = \varphi(x(t_f), t_f) + \int_{t_0}^{t_f} g(x, \dot{x}, t) dt \\
&\text{subject to} & &\psi(x(t_f), t_f) = 0
\end{align*}
$$

where $t_f$ is free. Defined the augmented cost
$$
\begin{gather*}
J_a = \omega(x(t_f), \nu, t_f) + \int_{t_0}^{t_f} g(x, \dot{x}, t) dt \\
\omega(x(t_f), \nu, t_f) = \varphi(x(t_f), t_f) + \nu^T \psi(x(t_f), t_f)
\end{gather*}
$$
The necessary optimality conditions are
$$
\begin{align*}
&\nabla_x \omega(x(t_f), \nu, t_f) + \nabla_{\dot{x}} g(x(t_f), \dot{x}(t_f), t_f) = 0 \qquad (\delta x_f) \\
&\frac{\partial}{\partial t_f} \omega(x(t_f), \nu, t_f) + g(x(t_f), \dot{x}(t_f), t_f) - \nabla_{\dot{x}} g(x(t_f), \dot{x}(t_f), t_f)^T \dot{x}(t_f) = 0 \qquad (\delta t_f) \\
&\psi(x(t_f), t_f) = 0 \qquad (\text{constraints}) \\
&\nabla_x g(x,\dot{x}, t) - \frac{d}{dt}\nabla_{\dot{x}}g(x,\dot{x},t) = 0, \quad t\in (t_0, t_f) \qquad (\delta x(t))
\end{align*}
$$

# Continuous Optimal Control

$$
\begin{align*}
&\text{minimize} & &J = \varphi(x(t_f), t_f) + \int_{t_0}^{t_f} L(x, u, t) dt \\
&\text{subject to} & &\psi(x(t_f), t_f) = 0 \\
& & &\dot{x} = f(x, u, t)
\end{align*}
$$

Define the augmented cost
$$
\begin{gather*}
J_a = \varphi(t_f) + \nu^T \psi(t_f) + \int_{t_0}^{t_f} [H(x,u,\lambda,t) -\lambda^T \dot{x}] dt \\
H = L(x,u,t) + \lambda^T(t)f(x,u,t)
\end{gather*}
$$
The necessary optimality conditions are
$$
\begin{align*}
&\text{Euler-Lagrange:} & &\begin{aligned}
&\dot{x} = f(x,u,t) & &(\text{dynamics}) \\
&\dot{\lambda} = -\nabla_x H & &(\delta x(t)) \\
\end{aligned} \\ \\
&\text{Control Opimization:} & &\nabla_u H = 0 \\ \\
&\text{Transversality Conditions (TC):}& &\begin{aligned}
&\psi(x(t_f), t_f) = 0 \qquad(\text{boundary constraint})\\
&\lambda(t_f) = \nabla_x \varphi(t_f) + \nabla_x \psi(t_f)\cdot \nu \qquad (\delta x_f) \\
&(\partial_t \varphi + \nu^T \partial_t \psi + L + \lambda^T f)_{t=t_f} = 0 \qquad (\delta t_f)
\end{aligned}
\end{align*}
$$

## Hamiltonian Conservation

When the Hamiltonian does not depend on time ($f$ and $L$ do not depend on time):
$$
\partial_t H(x,u,\lambda, t) = 0
$$
then $H$ is a conserved quantity along optimal trajectories
$$
\begin{split}
\dot{H}(x,u,\lambda,t) &= \partial_x H \cdot \dot{x} + \partial_u H \cdot \dot{u} + \partial_\lambda H \cdot \dot{\lambda} + \partial_t H \\
&= -\dot{\lambda}^T f(x,u,t) + 0 + f(x,u,t)^T \dot{\lambda} + 0 \\
&= 0
\end{split}
$$

# Linear-Quadratic Regulator

Dynamics is linear and cost function is quadratic
$$
\begin{gather*}
\dot{x} = Ax + Bu \\
J = \frac{1}{2}x^T(t_f) P_f x(t_f) + \int_{t_0}^{t_f} \frac{1}{2} [x(t)^T Q(t) x(t) + u(t)^T R(t) u (t)] dt
\end{gather*}
$$
where $P_f, Q \in \mathbb{S}_{+}$ and $R\in\mathbb{S}_{++}$

Using the optimality conditions to get
$$
\begin{gather*}
\begin{pmatrix}
\dot{x} \\ \dot{\lambda}
\end{pmatrix} = \begin{pmatrix}
A & -BR^{-1}B^T \\
-Q & -A^T
\end{pmatrix} \begin{pmatrix}
x \\ \lambda
\end{pmatrix} \\
u = -R^{-1}B^T\lambda \\
\lambda(t_f) = P_f x(t_f) \\
\end{gather*}
$$
Kalman showed that $\lambda(t)$ are linear functions of the states $\lambda(t) = P(t) x(t)$, then we have the **Riccati ODE**
$$
\dot{P} = -A^T P - PA + PBR^{-1}B^TP - Q, \quad P(t_f) = P_f
$$
and the control is
$$
u(t) = -R^{-1}B^TP(t)x(t) = -K(t) x(t)
$$

## Optimal Cost

$$
\begin{split}
J(t) &=\frac{1}{2} x^{T}\left(t_{f}\right) P_{f} x\left(t_{f}\right)+\int_{t}^{t_{f}} \frac{1}{2}\left[x^{T} Q x+u^{T} R u\right] \mathrm{d} t \\
&=\frac{1}{2} x^{T}\left(t_{f}\right) P_{f} x\left(t_{f}\right)+\int_{t}^{t_{f}} \frac{1}{2}\left[x(t)^{T}\left(Q+K^{T} R K\right) x\right] \mathrm{d} t \\
&=\frac{1}{2} x^{T}\left(t_{f}\right) P_{f} x\left(t_{f}\right)-\int_{t}^{t_{f}} \frac{d}{d t}\left(\frac{1}{2} x^{T} P x\right) \mathrm{d} t \\
&=\frac{1}{2} x^{T}(t) P(t) x(t)
\end{split}
$$

## Trajectory Tracking

Consider the problem of not stabilizing to the origin, i.e. $x\to 0$ but tracking a given reference trajectory $x_d(t)$, i.e. $x\to x_d$.

Define the *error state* and *control error*
$$
\begin{gather*}
e = x - x_d \\
v = u - u_d
\end{gather*}
$$
then
$$
\begin{split}
\dot{e} &= \dot{x} - \dot{x}_d \\
&= f(x_d+e, u_d+v) - f(x_d, u_d) \\
&\approx \frac{\partial f}{\partial x}\Big|_{x=x_d} e + \frac{\partial f}{\partial u}\Big|_{u=u_d} u \\
&= Ae + Bv
\end{split}
$$
Use LQR to solve $v = -K e$ and the control is
$$
u = -K(x - x_d) + u_d
$$

# Constrained Optimal Control

Consider optimal control problems subject to constraints $|u(t)| \le 1$

## Pontryagin's Minimum Principle

The optimal control must minimize the Hamiltonian:
$$
H(x^*(t), u^*(t) + \delta u(t), \lambda^*(t), t) \ge H(x^*(t), u^*(t), \lambda^*(t), t)
$$

## Minimum-Time Problems

Consider a linear system
$$
\dot{x} = Ax + Bu, \quad x(0) = x_0
$$
with a single control $u$ constrained by $|u(t)| \le 1$ and is required to reach to origin $x(t_f) = 0$ in minimum time. The cost function is then
$$
J = \int_{t_0}^{t_f} (1) dt
$$
So the Hamiltonian is $H = 1+ \lambda^T(Ax + Bu)$. According to the minimum principle, we have
$$
\begin{gather*}
u^* = +1, \quad \text{if } \lambda^TB < 0 \\
u^* = -1, \quad \text{if } \lambda^TB > 0
\end{gather*}
$$
where $\lambda^TB$ is called the *switching function*. This is an example of *bang-bang* control since the control is always at its max or min.

In addition, to ensure smoothness in the trajectory during transitions we need
$$
\begin{gather*}
\lambda(t^-) = \lambda(t^+) \\
H(t^-) = H(t^+)
\end{gather*}
$$
where $t$ is the time of transition. These conditions are called *Weierstrass-Erdmann conditions*

## Minimum Control Effort Problems

Consider a nonlinear system with affine controls defined by
$$
\dot{x} = a(x(t),t) + B(x(t), t) u(t)
$$
where $B$ is a $n\times m$ matrix and $|u_i(t)| \le 1$ for $i=1,\dots,m$. The cost function is
$$
J = \int_{t_0}^{t_f} \left(\sum_{i=1}^m |u_i(t)|\right) dt
$$
Express $B$ as $[b_1 \,|\, b_2 \,|\, \cdots \,|\, b_n]$  and assume the components of $u$ are independent of one another, from the minimum principle we have
$$
u_i^* = \begin{cases}
1, & \text{for } \lambda^{*T}b_i < -1 \\
0, & \text{for } -1 < \lambda^{*T}b_i < 1 \\
-1, & \text{for } 1 < \lambda^{*T}b_i \\
\ge 0, & \text{for } \lambda^{*T}b_i = -1 \\
\le 0, & \text{for } \lambda^{*T}b_i = 1
\end{cases}
$$

## Singular Controls

Singular controls are controls that cannot be directly determined by either the optimality conditions or the minimum principle.

In the unconstrained case this generally occurs when $\nabla_u H = 0$ cannot be solved for $u$ which is caused by the conditions $\nabla_u^2 H = 0$, i.e. when the Hamiltonian is not convex.

Consider the LQR setting where $R = 0$, then the first order condition
$$
\nabla_u H = B^T \lambda = 0
$$
which does not provide information about $u$. The solution is to consider higher-order derivative of $H_u$ until $u$ apperas explicitly:
$$
\begin{gather*}
\frac{d}{dt} \nabla_u H = B^T \dot{\lambda} = -B^T(Qx + A^T \lambda) = 0 \\
\frac{d^2}{dt^2} \nabla_u H = B^T \dot{\lambda} = -B^T(Q(Ax + Bu) + A^T (Qx + A^T\lambda)) = 0
\end{gather*}
$$
which now provides enough information to obtain the singular control $u$

## General Constraints

Now consider general constraints $c(x,u,t) \le 0$. The Hamiltonian is defined by
$$
H = L + \lambda^T f + \mu^T c, \quad \text{where } \begin{cases}
\mu \ge 0, & \text{if } c = 0 \\
\mu = 0, & \text{if } c < 0
\end{cases}
$$
The adjoint equations are
$$
\dot{\lambda} = \begin{cases}
-\nabla_x L - \nabla_x f^T \lambda, & c<0 \\
-\nabla_x L - \nabla_x f^T \lambda - \nabla_x c^T \mu, & c=0
\end{cases}
$$
The control is found by setting $\nabla_u H = 0$:
$$
0 = \begin{cases}
\nabla_u L + \nabla_u f^T \lambda, & c<0 \\
\nabla_u L + \nabla_u f^T \lambda + \nabla_u c^T \mu, & c=0
\end{cases}
$$

### Inequality Constraints on the State Only

Constraints with the form $c(x(t), t) \le 0$ is more difficult to handle and require differentiation until the control appears explicitly.

In general, if the constraint is differentiated $q$ times until $u$ shows up, then the Hamiltonian is defined as
$$
H = L + \lambda^T f + \mu^T c^{(q)}
$$
In addition, the *tangency* constraints are enforced at time $t$ when $c(x,t)$ becomes active.
$$
\begin{bmatrix}
c(x,t) \\
\dot{c}(c,t) \\
\vdots \\
c^{(q-1)}(x,t)
\end{bmatrix} = 0
$$

# Dynamic Programming

## Discrete-Time DP

Consider a discrete optimization problem
$$
\begin{align*}
&\text{minimize} & &J = \phi(x_N, t_N) + \sum_{i=0}^{N-1} L_i(x_i, u_i) \\
&\text{subject to} & &x_{i+1} = f_i(x_i, u_i)
\end{align*}
$$
with given $x(t_0)$, $t_0$, $t_N$.

Define the cost from discrete stage $i$ to $N$ by
$$
J_i \triangleq \phi(x_N, t_N) + \sum_{k=i}^{N-1} L_k(x_k, u_k)
$$
and the optimal cost-to-go function (or *optimal value function*) at stage $i$ as
$$
V_i(x) \triangleq \min_{u_{i:N-1}} J_i
$$
**Bellman equation**:
$$
V_i(x) = \min_u [L_i(x, u) + V_{i+1}(x')]
$$
with $V_N(x) = \phi(x, t_N)$

### Discrete-Time LQR

$$
\begin{gather*}
x_{i+1} = A_i x_i + B_i u_i \\
\phi(x) = \frac{1}{2} x^T P_f x, \qquad L_i(x,u) = x^TQ_ix + u^TR_iu
\end{gather*}
$$

Assume the value function is of the form
$$
V_i(x) = \frac{1}{2}x^TP_ix
$$
for $P_i \in \mathbb{S}_{++}$ with boundary condition $P_N = P_f$. Using Bellman's principle we can get
$$
u^{*}=-\left(R_{i}+B_{i}^{T} P_{i+1} B_{i}\right)^{-1} B_{i}^{T} P_{i+1} A_{i} x \equiv -K_{i} x
$$
Substituting $u^*$ back into the Bellman equation to obtain
$$
x^{T} P_{i} x=x^{T}\left[K_{i}^{T} R_{i} K_{i}+Q_{i}+\left(A_{i}-B_{i} K_{i}\right)^{T} P_{i+1}\left(A_{i}-B_{i} K_{i}\right)\right] x
$$
This relationship can be cycled backward starting from $P_N$ to $P_0$.

It can also be expressed without gains $K_i$ according to
$$
P_{i}=Q_{i}+A_{i}^{T}\left[P_{i+1}-P_{i+1} B_{i}\left(R_{i}+B_{i}^{T} P_{i+1} B_{i}\right)^{-1} B_{i}^{T} P_{i+1}\right] A_{i}
$$

## Continuous DP

Define the continuous value function $V(x,t)$ as
$$
V(x(t), t)=\min _{u(t), t \in\left[t, t_{f}\right]}\left[\phi\left(x\left(t_{f}\right), t_{f}\right)+\int_{t}^{t_{f}} L(x(\tau), u(\tau), \tau) d \tau\right]
$$
Bellman equation can be expressed as
$$
V(x(t), t)=\min_{u(t), t \in[t, t+\Delta]}\left[\int_{t}^{t+\Delta} L(x(\tau), u(\tau), \tau) \mathrm{d} \tau+V(x(t+\Delta), t+\Delta)\right]
$$
Expand $V(x(t+\Delta), t+\Delta)$ according to
$$
V(x(t+\Delta), t+\Delta) = V(x(t), t) + [\partial_t V(x(t), t) + \nabla_x V(x(t), t)^T \dot{x}] \Delta + o(\Delta)
$$
Substituting back into Bellman equation we can get the **Hamilton-Jacobi-Bellman equation (HJB)**:
$$
-\partial_{t} V(x, t)=\min_{u(t)}\left[L(x, u, t)+\nabla_{x} V(x, t)^{T} f(x, u, t)\right]
$$

# Numerical Methods for Optimal Control

General optimal control problem:
$$
\begin{align*}
&\text{minimize}\quad & &J=\phi\left(x(t_{f}), t_{f}\right)+\int_{t_{0}}^{t_{f}} L(x(t), u(t), t) d t \\
&\text{subject to}\quad & &x\left(t_{0}\right)=x_{0}, \quad x\left(t_{f}\right) \text{ and } t_{f} \text{ free} \\
& & &\dot{x}(t)=f(x(t), u(t), t) \\
& & &c(x(t), u(t), t) \le 0, \quad \text {for all } t \in\left[t_{0}, t_{f}\right] \\
& & &\psi\left(x\left(t_{f}\right), t_{f}\right) \le 0
\end{align*}
$$

Consider three families of numerical methods for solving this:

- *dynamic programming*: i.e. solution to the HJB equations
- *indirect methods*: base on calculus of variations, and Pontryagin's principle
- *direct methods*: based on a finite-dimensional representation

## Indirect Methods

Start with the necessary conditions (ignore the path constraints):
$$
\begin{align*}
&\dot{x}=f(x, u, t) \\
&\dot{\lambda}=-\nabla_{x} H(x, u, \lambda, t) \\
&u^{*}=\min_{u} H(x, u, \lambda, t) \\
&\lambda\left(t_{f}\right)=\nabla_{x} \phi\left(x\left(t_{f}\right), t_{f}\right)+\nabla_{x} \psi\left(x\left(t_{f}\right), t_{f}\right)^{T} \nu \\
&\left(\partial_{t} \phi+\nu^{T} \partial_{t} \psi+H\right)_{t=t_{f}}=0
\end{align*}
$$

### Indirect Shooting

Shooting methods are based on integrating the EL equations forward from time $t_0$ to time $t_f$ using a starting guess and then satisfying the boundary and transversality conditions at time $t_f$ by formulating a root-finding problem solved by e.g. Newton-type method.

The optimization variables are
$$
\lambda(t_0), \nu, t_f
$$
and the equations to be solved are
$$
\begin{bmatrix}
\psi\left(x\left(t_{f}\right), t_{f}\right) \\
\lambda\left(t_{f}\right)-\left[\nabla_{x} \phi\left(x\left(t_{f}\right), t_{f}\right)+\nabla_{x} \psi\left(x\left(t_{f}\right), t_{f}\right)^{T} \nu\right] \\
\left(\partial_{t} \phi+\nu^{T} \partial_{t} \psi+H\right)_{t=t_{f}}
\end{bmatrix} =0
$$

### Indirect Multiple-Shooting

Split the time-interval into segments and use indirect shooting.

First choose discrete time $[t_0, t_1, \dots, t_N]$ and let $v_i=(x(t_i), \lambda(t_i))$. Let $\bar{v}_i$ be the result of integrating the EL equations on $[t_i, t_{i+1}]$. The optimization variables are
$$
\lambda(t_0), v_1, v_2, \dots, v_N, \nu, t_f
$$
and the equations are
$$
\begin{bmatrix}
\bar{v}_{0}-v_{1} \\
\vdots \\
\bar{v}_{N-1}-v_{N} \\
\psi\left(x\left(t_{f}\right), t_{f}\right) \\
\lambda\left(t_{f}\right)-\left[\begin{array}{c}
\nabla_{x} \phi\left(x\left(t_{f}\right), t_{f}\right)+\nabla_{x} \psi\left(x\left(t_{f}\right), t_{f}\right)^{T} \nu
\end{array}\right] \\
\left(\partial_{t} \phi+\nu^{T} \partial_{t} \psi+H\right)_{t=t_{f}}
\end{bmatrix}=0
$$

## Direct Methods

Start with either discretizing time and solving a discrete-time optimal control problems, or by parametrizing the controls using a finite set of parameters. This then becomes a *nonlinear programming (NLP)* problem.

## Direct Shooting

Based on parametrizing the control $u(t)$ by a finite number of parameters $p_k$:
$$
u(t) = \sum_{k=1}^M p_k B_k(t)
$$
where $B_k(t)$ are a set of basis functions.

The NLP variables are $[p, t_f]$ and the state can be obtained by forward integration of the dynamics.

### Direct Multiple-Shooting

Choose discrete times $[t_0, t_1, \dots, t_N]$, discrete trajectory $[x_0, x_1, \dots, x_N]$ and a discrete set of parametrized control $[u(p_0), u(p_1), \dots, u(p_N)]$. Then perform direct shooting step on each interval $[t_i, t_{i+1}]$ by integrating the dynamics from $x_i$ and obtaining $\bar{x}_i$

The NLP variables are $p_0, \dots, p_{N-1}, x_1,\dots,x_N, t_f$ and the equations to be solved are
$$
\begin{bmatrix}
\bar{x}_{0}-x_{1} \\
\vdots \\
\bar{x}_{N-1}-x_{N} \\
\psi\left(x_{N}, t_{N}\right)
\end{bmatrix}=0
$$

# Optimal State Estimation

Linear model, Gaussian noise:

- Kalman filter (KF): linear discrete-time dynamics, linear measurement model, Gaussian noise
- Kalman-Bucy filter: same as KF but continuous-time dynamics

Nonlinear model, Gaussian noise:

- Extended Kalman filter (EKF): linearize dynamics and sensor model and apply KF
- Unscented Kalman filter (UKF): linearization is avoided during uncertainty propagation by propagating the principle axes of the uncertainty ellipsoid through the nonlinear dynamics and then reconstructing the updated ellipsoid; measurements are still processed through linearization

Nonlinear model, arbitrary noise:

- Particle filter (PF): states are approximated using weighted samples whose weights are updated by obtained measurements

## Least-Squares for Static Estimation

Consider the sensor model with $x\in\mathbb{R}^n$ and $z\in\mathbb{R}^k$
$$
z = Hx + v
$$
where $v$ is a random variable denoting the measurement error. The goal is find the optimal estimate $\hat{x}$ which can be accomplished by first defining the measurement estimate error $e_z =  z - H \hat{x}$ and minimizing
$$
J = \frac{1}{2}e_z^T e_z
$$

- Necessary condition: $\hat{x} = (H^TH)^{-1}H^Tz$
- The sufficient condition: $\nabla^2J = H^TH \succ 0$. This is satisfied when $\operatorname{rank}(H) = n$

Now assume there are some prior information about $x$ and $v$
$$
\begin{gather*}
\mathbb{E}[x] = \hat{x}_0, \qquad \mathbb{E}[(x - \hat{x}_0)(x - \hat{x}_0)^T] = P_0 \\
\mathbb{E}[v] = 0, \qquad \mathbb{E}[vv^T] = R
\end{gather*}
$$
where $R$ is a diagonal matrix. Then a meaningful cost function can be defined as
$$
J(\hat{x})=\frac{1}{2}\left(\hat{x}-\hat{x}_{0}\right)^{T} P_{0}^{-1}\left(\hat{x}-\hat{x}_{0}\right)+\frac{1}{2}(z-H \hat{x})^{T} R^{-1}(z-H \hat{x})
$$

- Necessary condition: $\hat{x}=\left(H^{T} R^{-1} H+P_{0}^{-1}\right)^{-1}\left(H^{T} R^{-1} z+P_{0}^{-1} \hat{x}_{0}\right)$
- The sufficient condition: $\nabla^2J = P_0^{-1} + H^TR^{-1}H \succ 0$

The necessary condition can be expressed as
$$
\begin{split}
\hat{x} &= \hat{x}_0 + (P_0^{-1} + H^TR^{-1}H)^{-1} H^T R^{-1} (z - H \hat{x}_0) \\
&\triangleq \hat{x}_0 + PH^TR^{-1} (z - H \hat{x}_0) \\
&\triangleq \hat{x}_0 + K^{-1} (z - H \hat{x}_0) \\
\end{split}
$$
The matrix $P$ is actually the covariance matrix of the error in the estimate $\hat{x}$, i.e. $P=\mathbb{E}[(\hat{x} - x)(\hat{x}- x)^T]$.

The recursive least-squares algorithm:

1. Given prior: mean $\hat{x}_0$ and covariance $P_0$
2. For each new measurement $z_i = H_ix+v_i$, where $v_i \sim \mathcal{N}(0, R_i)$
   1. $\hat{x}_{i} =\hat{x}_{i-1}+K_{i}\left(z_{i}-H_{i} \hat{x}_{i-1}\right)$
   2. $P_{i} =P_{i-1}-P_{i-1} H_{i}^{T}\left(H_{i} P_{i-1} H_{i}^{T}+R_{i}\right)^{-1} H_{i} P_{i-1}$
   3. $K_{i} =P_{i} H_{i}^{T} R_{i}^{-1}$

Here $P_i$ is updated using the matrix inversion lemma:
$$
\begin{split}
P_i &= \left(P_{i-1}^{-1}+H_{i}^{T} R_{i}^{-1} H_{i}\right)^{-1} \\
&= P_{i-1}-P_{i-1} H_{i}^{T}\left(H_{i} P_{i-1} H_{i}^{T}+R_{i}\right)^{-1} H_{i} P_{i-1}
\end{split}
$$

## Propagation of Uncertainty

Consider the restricted class of systems
$$
\dot{x}(t) = f(x(t), u(t)) + L(t)w(t)
$$
where $L(t)$ is a given matrix and where the noise $w(t)$ evolves in continuous time. Assume $w(t)$ is uncorrelated in time, i.e.
$$
\mathbb{E}[w(t)w(\tau)^T] = Q_c'(t) \delta(t - \tau)
$$

### Linear System

Consider linear systems with Gaussian noise
$$
x_k = \Phi_{k-1}x_{k-1} + \Gamma_{k-1}u_{k-1} + \Lambda_{k-1}w_{k-1}
$$
where $\mathbb{E}[w_k]=0$, $\mathbb{E}[w_k w_l^T] = Q_k'\delta_{kl}$ and $x_0 \sim \mathcal{N}(\hat{x}_0, P_0)$

The goal is to propagate the mean and covariance:
$$
\begin{align*}
\hat{x}_k &= \mathbb{E}[x] = \Phi_{k-1}\hat{x}_{k-1} + \Gamma_{k-1}u_{k-1} \\
P_k &= \mathbb{E}[(x_k - \hat{x}_k)(x_k-\hat{x}_k)^T] \\
&= \Phi_{k-1}P_{k-1}\Phi_{k-1}^T + \Lambda_{k-1}Q_{k-1}'\Gamma_{k-1}^T
\end{align*}
$$

The resulting update $\left(\hat{x}_{k-1}, P_{k-1}\right) \to\left(\hat{x}_{k}, P_{k}\right)$ are called a *Gauss-Markov sequence*, given by
$$
\begin{align*}
\hat{x}_{k} &=\Phi_{k-1} \hat{x}_{k-1}+\Gamma_{k-1} u_{k-1} \\
P_{k} &=\Phi_{k-1} P_{k-1} \Phi_{k-1}^{T}+Q_{k-1} \\
Q_{k} &= \Lambda_k Q_k' \Lambda_k'
\end{align*}
$$

### Continuous to Discrete

Consider
$$
\dot{x}(t)=F(t) x(t)+G(t) u(t)+L(t) w(t)
$$
We have
$$
x(t_k) =\Phi(t_{k}, t_{k-1}) x(t_{k-1})+\int_{t_{k-1}}^{t_{k}} \Phi(t_{k}, \tau) [G(\tau) u(\tau)+L(\tau) w(\tau)] d\tau
$$
where $\Phi(t_k, t_{k-1})$ is the *state transition matrix* such that
$$
\Phi(t_{k}, t_{k-1}) x(t_{k-1})=x(t_{k-1})+\int_{t_{k-1}}^{t_{k}}[F(\tau) x(\tau)] d \tau
$$
The mean evolves according to (assume the control is constant during the sampling interval)
$$
\begin{split}
\hat{x}_{k} &= \Phi_{k-1} \hat{x}_{k-1}+\int_{t_{k-1}}^{t_{k}} \Phi(t_{k}, \tau) G(\tau) u(\tau) d \tau \\
&= \Phi_{k-1} \hat{x}_{k-1}+\left[\int_{t_{k-1}}^{t_{k}} \Phi(t_{k}, \tau) G(\tau) d \tau\right] u_{k-1} \\
&= \Phi_{k-1} \hat{x}_{k-1} + \Gamma_{k-1}u_{k-1}
\end{split}
$$
The covariance evolves according to
$$
\begin{split}
P_{k} &=\Phi_{k-1} P_{k-1} \Phi_{k-1}^{T}+\mathbb{E}\left\{\left[\int_{t_{k-1}}^{t_{k}} \Phi(t_{k}, \tau) L(\tau) w(\tau) d \tau\right]\left[\int_{t_{k-1}}^{t_{k}} \Phi(t_{k}, \alpha) L(\alpha) w(\alpha) d \alpha\right]^{T}\right\} \\
&=\Phi_{k-1} P_{k-1} \Phi_{k-1}^{T}+\int_{t_{k-1}}^{t_{k}} \int_{t_{k-1}}^{t_{k}} \Phi(t_{k}, \tau) L(\tau) \underbrace{\mathbb{E}\left[w(\tau) w(\alpha)^{T}\right]}_{Q_{c}^{\prime} \delta(\tau-\alpha)} L(\alpha)^{T} \Phi(t_{k}, \alpha)^T d \tau d \alpha \\
&=\Phi_{k-1} P_{k-1} \Phi_{k-1}^{T}+\int_{t_{k-1}}^{t_{k}} \Phi(t_{k}, \tau) L(\tau) Q_{c}^{\prime} L(\tau)^{T} \Phi(t_{k}, \tau)^T d \tau \\
& \triangleq \Phi_{k-1} P_{k-1} \Phi_{k-1}^{T}+Q_{k-1}
\end{split}
$$
where $Q_{k-1}$ can be approximated by
$$
Q_{k-1} \approx L(t_{k-1}) Q_{c}^{\prime}(t_{k-1}) L(t_{k-1})^T \Delta t
$$

## Linear-Optimal Estimation

Combine measurement updates and uncertainty propagation to optimally estimate the state. Consider a discrete LTV model
$$
\begin{align*}
x_{k} &=\Phi_{k-1} x_{k-1}+\Gamma_{k-1} u_{k-1}+\Lambda_{k-1} w_{k-1} \\
z_{k} &=H_{k} x_{k}+v_{k}
\end{align*}
$$
Estimating the state $x_k$ is performed by iterating between uncertainty propagation and measurement updates. The mean and covariance after an uncertainty propagation from step $k-1$ to step $k$ are denoted by $(x_{k|k-1}, P_{k|k-1})$. The mean and covariance after a measurement update at step $k$ are denoted by $(x_{k|k}, P_{k|k})$ **Kalman filter**:

Prediction:
$$
\begin{align*}
\hat{x}_{k | k-1} &=\Phi_{k-1} \hat{x}_{k-1 | k-1}+\Gamma_{k-1} u_{k-1} \\
P_{k | k-1} &=\Phi_{k-1} P_{k-1 | k-1} \Phi_{k-1}^{T}+Q_{k-1}
\end{align*}
$$
Correction:
$$
\begin{align*}
\hat{x}_{k | k} &=\hat{x}_{k | k-1}+K_{k}\left(z_{k}-H_{k} \hat{x}_{k | k-1}\right) \\
P_{k | k} &=P_{k | k-1}-P_{k | k-1} H_{k}^{T}\left(H_{k} P_{k | k-1} H_{k}^{T}+R_{k}\right)^{-1} H_{k} P_{k | k-1} \\
K_{k} &=P_{k | k} H_{k}^{T} R_{k}^{-1}
\end{align*}
$$

# Stochastic Control

$$
\begin{align*}
&\text{minimize} & &J(u(\cdot))=\mathbb{E}\left[\phi\left(x(t_{f}), t_{f}\right)+\int_{t_{0}}^{t_{f}} \mathcal{L}(x(\tau), u(\tau), \tau) d \tau\right] \\
&\text{subject to} & &\dot{x}(t) = f(x(t), u(t), w(t), t)
\end{align*}
$$

where $\mathbb{E}[w(t)]=0$ and $\mathbb{E}[w(t)w(\tau)^T] = W(t) \delta(t-\tau)$ for $W(t) \in \mathbb{S}_+$

## Perfect Measurements

Consider
$$
\dot{x}(t)=f(x(t), u(t))+L(t) w(t)
$$
The value function is defined as
$$
\begin{split}
V(x(t), t)&=\min _{u(t), t \in[t, t_{f}]} \mathbb{E}\left[\phi\left(x(t_{f}), t_{f}\right)+\int_{t}^{t_{f}} \mathcal{L}(x(\tau), u(\tau), \tau) d \tau\right] \\
&= \min_{u(t), t \in[t, t+\Delta t]} \mathbb{E}\left[\int_{t}^{t+\Delta t} \mathcal{L}(x(\tau), u(\tau), \tau) d \tau + V(x(t+\Delta t), t+\Delta t)\right]
\end{split}
$$
Expand $V(x,t)$ to first order in $\Delta t$ according to
$$
\begin{split}
V(x+\Delta x, t+\Delta t) &=V(x, t)+\partial_{t} V(x, t) \Delta t+\nabla_{x} V(x, t)^{T} \Delta x+\frac{1}{2} \Delta x^{T} \nabla_{x}^{2} V(x, t) \Delta x+o(\Delta t) \\
&=V+\partial_{t} V \Delta t+\nabla_{x} V^{T}(f+L w) \Delta t+\frac{1}{2}(f+L w)^{T} \nabla_{x}^{2} V(f+L w) \Delta t^{2}+o(\Delta t)
\end{split}
$$
Note that in the above it is necessary to expand $V$ to second order in $\Delta x$ since $w$ is of order $1/\sqrt{\Delta t}$

Substituting back to get the **stochastic HJB equation**
$$
-\partial_t V(x, t) =\min_{u(t)}\left\{\mathcal{L}(x, u, t)+\nabla_{x} V(x, t)^{T} f(x, u)+\frac{1}{2} \operatorname{tr}\left[\nabla_{x}^{2} V(x, t) L(t) W(t) L(t)^{T}\right]\right\}
$$

### Continuous Linear-Quadratic Systems

$$
\begin{gather*}
\dot{x} = Fx + Gu + Lw \\
J = \frac{1}{2} \mathbb{E}\left\{x^T(t_f) S_f x(t_f) + \int_{t_0}^{t_f} \begin{bmatrix} x(t) \\ u(t) \end{bmatrix}^T
\begin{bmatrix}
Q(t) & M(t) \\
M(t)^T & R(t)
\end{bmatrix}
\begin{bmatrix} x(t) \\ u(t) \end{bmatrix}
\right\}
\end{gather*}
$$

with $x(0)=x_0$ and given $t_0$ and $t_f$.

To solve the stochastic HJB, consider the value function of the form
$$
V(t)=\frac{1}{2} x^{T}(t) S(t) x(t)+v(t)
$$
where $v(t)$ is the *stochastic value function increment* defined by
$$
v(t)=\frac{1}{2} \int_{t}^{t_{f}} \operatorname{tr}\left[S(\tau) L W L^{T}\right] d \tau
$$
Plug into the stochastic HJB equation and solve for $u$, and substitute back to get the following equations:
$$
\begin{gather*}
-\dot{S}=\left(F-G R^{-1} M^{T}\right)^{T} S+S\left(F-G R^{-1} M^{T}\right)+Q-S G R^{-1} G^{T} S-M R^{-1} M^{T} \\
-\dot{v} = \frac{1}{2}\operatorname{tr}(SLWL^T)
\end{gather*}
$$

### Discrete Linear-Quadratic Systems

$$
\begin{gather*}
x_{k+1} = F_k x_k + G_k u_k + L_k w_k \\
J = \frac{1}{2} \mathbb{E}\left\{x_N^T S_N x_N + \sum_{k=0}^{N-1} \begin{bmatrix} x_k \\ u_k \end{bmatrix}^T
\begin{bmatrix}
Q & M \\
M^T & R
\end{bmatrix}
\begin{bmatrix} x_k \\ u_k \end{bmatrix}
\right\}
\end{gather*}
$$

with $x(0)=x_0$ and given $t_0$ and $t_f$.

Similarly use value function of the form
$$
\begin{gather*}
V_k = \frac{1}{2}x_k S_k x_k + v_k \\
v_k = \frac{1}{2} \operatorname{tr}[S_{k+1} L W_k L^T] + v_{k+1}
\end{gather*}
$$
Plug into the stochastic HJB equation we can get
$$
S_{k}=\left(F^{T} S_{k+1} F+Q\right)-\left(M^{T}+G^{T} S_{k+1} F\right)^{T}\left(R+G^{T} S_{k+1} G\right)^{-1}\left(M^{T}+G^{T} S_{k+1} F\right)
$$
