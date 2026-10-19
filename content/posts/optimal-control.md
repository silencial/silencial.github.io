---
title: Optimal Control
date: 2020-12-29
updated: 2026-10-13
categories:
- Study
tags:
- JHU
- Robotics
---

EN530.603 Review.

<!--more-->

---

# Unconstrained Optimization

## Optimality Condition

**Necessary Conditions**:

$$
\begin{align*}
&\nabla f(x^*) = 0 && \text{First-order Necessary Conditions} \\
&\nabla^2 f(x^*) \succeq 0 && \text{Second-order Necessary Conditions}
\end{align*}
$$

**Sufficient Conditions**:

$$
\nabla f(x^*) = 0 \quad \text{and} \quad \nabla^2 f(x^*) \succ 0
$$

## Numerical Solution

Iterative update rule:

$$
x^{k+1} = x^k + \alpha^k d^k, \quad k=0,1,\dots
$$

where $d^k$ is the **search direction** and $\alpha^k > 0$ is the **step size**. Gradient-based methods select $\alpha^k$ and $d^k$ according to:

1. **Descent Direction Requirement**: Choose $d^k$ such that whenever $\nabla f(x^k) \neq 0$,

    $$
    \nabla f(x^k)^T d^k < 0
    $$

2. **Sufficient Decrease Requirement**: Choose $\alpha^k > 0$ such that

    $$
    f(x^k + \alpha^k d^k) < f(x^k)
    $$

### Search Direction

General formulation for gradient-based search directions:

$$
x^{k+1} = x^k - \alpha^k D^k \nabla f(x^k)
$$

where $D^k \succ 0$ is apositive definite scaling matrix.

- **Steepest Descent**:

    $$
    D^k = I
    $$

- **Newton's Method**:

    $$
    D^k = \left[ \nabla^2 f(x^k) \right]^{-1}
    $$

- **Gauss-Newton Method**: Applied when the cost function has a least squares structure.

    $$
    f(x) = \frac{1}{2} \norm{g(x)}^2
    $$

    The scaling matrix choice is

    $$
    D^k = \left[ \nabla g(x^k) \nabla g(x^k)^T \right]^{-1}
    $$

- **Conjugate Gradient Method**: Generate linearly independent (conjugate) search directions $d^k$.

    $$
    d^k = -\nabla f(x^k) + \beta^k d^{k-1}
    $$

    Common choice for $\beta^k$:

    $$
    \beta^k = \frac{\nabla f(x^k)^T \left( \nabla f(x^k) - \nabla f(x^{k-1}) \right)}{\nabla f(x^{k-1})^T \nabla f(x^{k-1})}
    $$

### Step Size Selection Rules

- **Minimization Line Search**: Exact step size computation over a bounded interval $[0, s]$ so that $f$ is minimized.

    $$
    \DeclareMathOperator*{\argmin}{arg\,min\,}
    \DeclareMathOperator*{\argmax}{arg\,max\,}
    \alpha^k = \argmin_{\alpha \in [0, s]} f(x^k + \alpha d^k)
    $$

- **Armijo Rule (Backtracking Line Search)**:
    1. Select parameters $s > 0$, $\beta \in (0, 1)$, and $\sigma \in (0, 1)$.
    2. Set $\alpha^k = \beta^m s$, where $m$ is the smallest nonnegative integer satisfying $f(x^k) - f(x^k + \beta^m s d^k) \ge -\sigma \beta^m s \nabla f(x^k)^T d^k$

    Here $\beta$ represents the contraction factor and $\sigma$ specifies the acceptance margin.

### Regularized Newton Method

Drawbacks of standard Newton's method:

- The inverse Hessian matrix $[\nabla^2 f(x)]^{-1}$ may be singular or computationally ill-conditioned.
- When $\nabla^2 f(x) \nsucc 0$, the search direction is not guaranteed to be a descent direction, potentially attracting the trajectory toward local maxima or saddle points.

Resolution: Add a regularization term to the Hessian and solve the system

$$
(\nabla^2 f(x^k) + \Delta^k) d^k = -\nabla f(x^k)
$$

where $\Delta^k$ is chosen such that $\nabla^2 f(x^k) + \Delta^k \succ 0$.

In **Trust-Region** frameworks, setting $\Delta^k = \delta^k I$ yields an equivalence to the constrained subproblem:

$$
d^k = \argmin_{\norm{d} \le \gamma^k} f^k(d)
$$

where $f^k(d) = f(x^k) + \nabla f(x^k)^T d + \frac{1}{2} d^T \nabla^2 f(x^k) d$ is the local quadratic model and $\gamma^k$ is the trust-region radius.

# Constrained Optimization

## Equality Constraint

Minimize $L(x): \mathbb{R}^n \to \mathbb{R}$, subject to $k$ equality constraints $f(x) = 0$, where $f: \mathbb{R}^n \to \mathbb{R}^k$.

### Necessary Optimality Conditions

Let $\mathcal{L}(x, \lambda) = L(x) + \lambda^T f(x)$ be the Lagrangian, where $\lambda \in \mathbb{R}^k$ represents the vector of Lagrange multipliers.

- **First-order Necessary Conditions**:

    $$
    \nabla_x \mathcal{L}(x^*, \lambda^*) = 0 \quad \text{and} \quad f(x^*) = 0
    $$

- **Second-order Necessary Conditions**:

    $$
    dx^T \left[ \nabla_{xx} \mathcal{L}(x^*, \lambda^*) \right] dx \ge 0, \quad \forall dx \in \text{Null}(J_f(x^*))
    $$

    where $dx$ is restricted to the tangent space of the constraint surface, satisfying $\nabla f_i(x^*)^T dx = 0$ for all $i=1,\dots,k$ (or equivalently, $J_f(x^*) dx = 0$).

**Geometric Interpretation**: Any feasible directional variation $dx$ must lie in the tangent space orthogonal to the constraint gradients $\nabla f_i(x^*)$. At an optimum $x^*$, the cost gradient $\nabla L(x^*)$ must be orthogonal to all feasible variations $dx$, implying that $\nabla L(x^*)$ lies in the range space spanned by the constraint gradients.

$$
\nabla L(x^*) = -\sum_{i=1}^k \lambda_i \nabla f_i(x^*)
$$

### Sufficient Optimality Conditions

**Second-order Sufficient Conditions**:

$$
\begin{gather*}
\nabla_x \mathcal{L}(x^*, \lambda^*) = 0, \quad f(x^*) = 0 \\
dx^T \left[\nabla_{xx} \mathcal{L}(x^*, \lambda^*)\right] dx > 0, \quad \forall dx \in \text{Null}(J_f(x^*)) \setminus \{0\}
\end{gather*}
$$

## Inequality Constraint

Minimize $L(x)$ subject to $f(x) \le 0$, where $f: \mathbb{R}^n \to \mathbb{R}^k$.

### KKT Conditions

The Lagrange multipliers $\lambda \in \mathbb{R}^k$ must satisfy the complementarity conditions:

$$
\lambda_i \ge 0, \quad f_i(x) \le 0, \quad \lambda_i f_i(x) = 0, \quad \forall i
$$

which can be stated piecewise as

$$
\lambda_i \begin{cases}
\ge 0, &\text{if } f_i(x)=0 \quad (\text{active constraint}) \\
= 0, &\text{if } f_i(x)<0 \quad (\text{inactive constraint})
\end{cases}
$$

**Geometric Interpretation**: At a local minimum, the negative gradient of the cost function $-\nabla L(x^*)$ must lie inside the cone generated by the gradients of the active constraints ($f_i(x^*)=0$).

$$
-\nabla L(x^*) = \sum_{i \in \mathcal{A}} \lambda_i \nabla f_i(x^*), \quad (\lambda_i \ge 0)
$$

where $\mathcal{A} = \{i \mid f_i(x^*) = 0\}$ is the active constraint set. This ensures that any motion decreasing $L(x)$ necessarily violates at least one active constraint.

# Trajectory Optimization

Formulation of continuous-time optimal control problems:

$$
\begin{align*}
&\text{minimize} & & J(x(t), u(t), t_f) = \int_{t_0}^{t_f} L(x(t), u(t), t) dt \\
&\text{subject to} & & \dot{x}(t) = f(x(t), u(t), t) \\
& & & \text{path and boundary constraints}
\end{align*}
$$

The performance index $J$ is a **functional** (a mapping from a space of functions to the real numbers).

$$
\text{Differential of a function } (\nabla g = 0) \quad \iff \quad \text{Variation of a functional } (\delta J = 0)
$$

## Euler-Lagrange Equation

Consider the unconstrained fundamental functional:

$$
J = \int_{t_0}^{t_f} g(x(t), \dot{x}(t), t) dt
$$

given fixed initial state $x(t_0)$.

The first variation $\delta J$ is derived as

$$
\delta J = \int_{t_0}^{t_f} \left[ g_x(x, \dot{x}, t) - \frac{d}{dt} g_{\dot{x}}(x, \dot{x}, t) \right] \delta x dt + \left[ g_{\dot{x}}(x(t_f), \dot{x}(t_f), t_f) \right] \delta x(t_f)
$$

- **Fixed Terminal Boundary Conditions**: If $x(t_f)$ is specified ($\delta x(t_f) = 0$), the optimality condition $\delta J = 0$ yields the Euler-Lagrange equation

    $$
    g_x(x, \dot{x}, t) - \frac{d}{dt} g_{\dot{x}}(x, \dot{x}, t) = 0
    $$

- **Free Terminal Boundary Conditions**: If $x(t_f)$ is unconstrained ($\delta x(t_f)$ arbitrary), $\delta J = 0$ requires both the Euler-Lagrange equation and the natural boundary condition

    $$
    \begin{gather*}
    g_x(x, \dot{x}, t) - \frac{d}{dt} g_{\dot{x}}(x, \dot{x}, t) = 0 \\
    g_{\dot{x}}(x(t_f), \dot{x}(t_f), t_f) = 0
    \end{gather*}
    $$

## Free Final-Time Problem

When the terminal time $t_f$ is allowed to vary, the total variation at $t_f$ incorporates both spatial variation $\delta x(t_f)$ and temporal variation $\delta t_f$:

$$
\delta x_f = \delta x(t_f) + \dot{x}(t_f) \delta t_f
$$

The overall variation becomes

$$
\delta J = \int_{t_0}^{t_f} \left[ g_x - \frac{d}{dt} g_{\dot{x}} \right] \delta x dt + g_{\dot{x}}(t_f) \delta x_f + \left[ g(t_f) - g_{\dot{x}}(t_f) \dot{x}(t_f) \right] \delta t_f
$$

- **Independent $t_f$ and $x(t_f)$**: When $\delta x_f$ and $\delta t_f$ are independent and unconstrained, the terminal necessary conditions are

    $$
    g_{\dot{x}}(t_f) = 0 \quad \text{and} \quad g(t_f) = 0
    $$

- **Surface-Constrained Terminal State**: When the terminal state is constrained to lie on a curve/surface $x(t_f) = \Theta(t_f)$, the relation $\delta x_f = \frac{d\Theta}{dt} \delta t_f$ holds. The transversality condition simplifies to

    $$
    g_{\dot{x}}(t_f) \left[ \frac{d\Theta}{dt} - \dot{x}^* \right]_{t=t_f} + g(t_f) = 0
    $$

## Differential Constraints

Consider problems with algebraic or kinematic differential constraints:

$$
\begin{align*}
&\text{minimize} &  &J = \int_{t_0}^{t_f} g(x, \dot{x}, t) dt \\
&\text{subject to} & &f(x(t), \dot{x}(t), t) = 0
\end{align*}
$$

where $t_0, t_f, x(t_0)$, and $x(t_f)$ are fixed.

Define dynamic Lagrange multipliers $\lambda(t): [t_0, t_f] \to \mathbb{R}^k$ and the augmented functional $J_a$:

$$
J_a = \int_{t_0}^{t_f} g_a(x, \dot{x}, \lambda, t) dt, \quad \text{where } g_a = g + \lambda(t)^T f(x, \dot{x}, t)
$$

The necessary optimality conditions are the Euler-Lagrange equations evaluated on the augmented integrand $g_a$.

## General Boundary Constraints

Consider problems with terminal state costs and general boundary conditions:

$$
\begin{align*}
&\text{minimize} & &J = \varphi(x(t_f), t_f) + \int_{t_0}^{t_f} g(x, \dot{x}, t) dt \\
&\text{subject to} & &\psi(x(t_f), t_f) = 0
\end{align*}
$$

where $t_f$ is free. Define terminal multipliers $\nu$ and the augmented terminal function $\omega$:

$$
\omega(x(t_f), \nu, t_f) = \varphi(x(t_f), t_f) + \nu^T \psi(x(t_f), t_f)
$$

The complete necessary conditions for optimality comprise:

- **Euler-Lagrange Equation** (In-domain $t \in (t_0, t_f)$):

    $$
    \nabla_x g(x, \dot{x}, t) - \frac{d}{dt} \nabla_{\dot{x}} g(x, \dot{x}, t) = 0
    $$

- **Spatial Transversality Condition** ($\delta x_f$):

    $$
    \nabla_x \omega(x(t_f), \nu, t_f) + \nabla_{\dot{x}} g(x(t_f), \dot{x}(t_f), t_f) = 0
    $$

- **Temporal Transversality Condition** ($\delta t_f$):

    $$
    \frac{\partial \omega}{\partial t_f} + \left[ g(x, \dot{x}, t) - \nabla_{\dot{x}} g(x, \dot{x}, t)^T \dot{x} \right]_{t=t_f} = 0
    $$

- **Terminal Feasibility**:

    $$
    \psi(x(t_f), t_f) = 0
    $$

# Continuous Optimal Control

Consider the general continuous-time optimal control problem:

$$
\begin{align*}
& \text{minimize} & & J = \varphi(x(t_f), t_f) + \int_{t_0}^{t_f} L(x(t), u(t), t) dt \\
& \text{subject to} & & \dot{x}(t) = f(x(t), u(t), t) \\
& & & \psi(x(t_f), t_f) = 0
\end{align*}
$$

Define the **Hamiltonian** $H(x, u, \lambda, t)$ and the augmented functional $J_a$:

$$
\begin{gather*}
H(x, u, \lambda, t) = L(x, u, t) + \lambda(t)^T f(x, u, t) \\
J_a = \varphi(x(t_f), t_f) + \nu^T \psi(x(t_f), t_f) + \int_{t_0}^{t_f} \left[ H(x, u, \lambda, t) - \lambda(t)^T \dot{x}(t) \right] dt
\end{gather*}
$$

where $\lambda(t) \in \mathbb{R}^n$ is the costate (adjoint) vector trajectory and $\nu \in \mathbb{R}^q$ is the vector of terminal constraint multipliers.

The first-order necessary conditions for optimality comprise:

- **State and Adjoint (Costate) Dynamics**:

    $$
    \begin{align*}
    \dot{x}(t) &= \nabla_\lambda H = f(x, u, t) & & (\text{State Dynamics}) \\
    \dot{\lambda}(t) &= -\nabla_x H(x, u, \lambda, t) & & (\text{Adjoint/Costate Dynamics})
    \end{align*}
    $$

- **Control Optimality Condition** (Unconstrained $u$):

    $$
    \nabla_u H(x, u, \lambda, t) = 0
    $$

- **Transversality Conditions**:

    $$
    \begin{align*}
    &\psi(x(t_f), t_f) = 0 & &(\text{Terminal Boundary Feasibility}) \\
    &\lambda(t_f) = \nabla_x \varphi(x(t_f), t_f) + \nabla_x \psi(x(t_f), t_f) \cdot \nu & &(\text{Spatial Variation } \delta x_f) \\
    &\left[ \frac{ \partial \varphi }{ \partial t_f } + \nu^T \frac{ \partial \psi }{ \partial t_f } + H \right]_{t=t_f} = 0 & &(\text{Temporal Variation } \delta t_f)
    \end{align*}
    $$

## Hamiltonian Conservation

When the running cost $L$ and system dynamics $f$ have no explicit dependence on time $t$ ($\partial_t L = 0$ and $\partial_t f = 0$), the Hamiltonian is autonomous ($\partial_t H = 0$).

Under such conditions, $H$ is a conserved quantity along any optimal state-costate-control trajectory $(x^*, \lambda^*, u^*)$:

$$
\begin{split}
\frac{d}{dt} H(x, u, \lambda, t) &= \nabla_x H^T \dot{x} + \nabla_u H^T \dot{u} + \nabla_\lambda H^T \dot{\lambda} + \frac{ \partial H }{ \partial t } \\
&= -\dot{\lambda}^T f(x, u, t) + 0 \cdot \dot{u} + f(x, u, t)^T \dot{\lambda} + 0 \\
&= 0
\end{split}
$$

Thus, $H(x^*(t), u^*(t), \lambda^*(t)) = C$ (a constant) for all $t \in [t_0, t_f]$.

- **Time-Invariant Terminal Costs/Constraints**: If the terminal cost $\varphi(x(t_f))$ and boundary constraint $\psi(x(t_f))$ do not explicitly depend on $t_f$ ($\frac{\partial \varphi}{\partial t_f} = 0$ and $\frac{\partial \psi}{\partial t_f} = 0$), the temporal transversality condition simplifies directly to $H(t_f) = 0$. Consequently, $H(t) \equiv 0$ across the entire time horizon.
- **Time-Explicit Terminal Costs/Constraints**: If $\varphi$ or $\psi$ explicitly depends on $t_f$, the terminal condition gives $H(t_f) = -\left[ \frac{\partial \varphi}{\partial t_f} + \nu^T \frac{\partial \psi}{\partial t_f} \right]_{t=t_f}$. In this case, $H(t)$ remains constant at this nonzero boundary value throughout $t \in [t_0, t_f]$.

# Linear-Quadratic Regulator

Linear dynamic system with quadratic performance index:

$$
\begin{align*}
& \text{minimize} & & J = \frac{1}{2} x(t_f)^T P_f x(t_f) + \int_{t_0}^{t_f} \frac{1}{2} \left[ x(t)^T Q(t) x(t) + u(t)^T R(t) u(t) \right] dt \\
& \text{subject to} & & \dot{x}(t) = A(t) x(t) + B(t) u(t)
\end{align*}
$$

where $P_f \succeq 0$, $Q(t) \succeq 0$, and $R(t) \succ 0$.

## Riccati Differential Equation

Formulate the Hamiltonian function and apply the first-order optimality conditions:

$$
\begin{gather*}
\begin{pmatrix}
\dot{x} \\ \dot{\lambda}
\end{pmatrix} = \begin{pmatrix}
A & -B R^{-1} B^T \\
-Q & -A^T
\end{pmatrix} \begin{pmatrix}
x \\ \lambda
\end{pmatrix} \\
u(t) = -R^{-1} B^T \lambda(t) \\
\lambda(t_f) = P_f x(t_f)
\end{gather*}
$$

It can be shown that $\lambda(t)$ is linear in $x(t)$: $\lambda(t) = P(t) x(t)$. Differentiating with respect to time $t$ and substituting the dynamics yields:

$$
\left[ \dot{P}(t) + A^T P(t) + P(t) A - P(t) B R^{-1} B^T P(t) + Q(t) \right] x(t) = 0
$$

Since this must hold for arbitrary nonzero states $x(t)$, $P(t)$ satisfies the **Riccati Equation**:

$$
-\dot{P} = A^T P + P A - P B R^{-1} B^T P + Q, \quad P(t_f) = P_f
$$

The optimal feedback control law is given by

$$
u(t) = -R^{-1} B^T P(t) x(t) = -K(t) x(t)
$$

where $K(t) = R^{-1} B^T P(t)$ is the time-variant state feedback gain.

## Optimal Cost

The minimum cost-to-go evaluated from time $t$ to $t_f$ along the optimal trajectory is quadratic in $x(t)$:

$$
\begin{split}
J^*(t) &= \frac{1}{2} x(t_f)^T P_f x(t_f) + \int_{t}^{t_f} \frac{1}{2} \left[ x(\tau)^T Q x(\tau) + u(\tau)^T R u(\tau) \right] d\tau \\
&= \frac{1}{2} x(t_f)^T P_f x(t_f) + \int_{t}^{t_f} \frac{1}{2} x(\tau)^T \left( Q + K^T R K \right) x(\tau) d\tau \\
&= \frac{1}{2} x(t_f)^T P_f x(t_f) - \int_{t}^{t_f} \frac{d}{d\tau} \left( \frac{1}{2} x(\tau)^T P(\tau) x(\tau) \right) d\tau \\
&= \frac{1}{2} x(t)^T P(t) x(t)
\end{split}
$$

## Trajectory Tracking

Consider tracking a smooth reference trajectory $x_d(t)$ with reference input $u_d(t)$ satisfying $\dot{x}_d = f(x_d, u_d)$.

Define tracking error state $e(t)$ and control offset $v(t)$:

$$
\begin{gather*}
e(t) = x(t) - x_d(t) \\
v(t) = u(t) - u_d(t)
\end{gather*}
$$

First-order Taylor expansion around $(x_d, u_d)$ yields linear error dynamics:

$$
\begin{split}
\dot{e}(t) &= \dot{x}(t) - \dot{x}_d(t) \\
&= f(x_d + e, u_d + v) - f(x_d, u_d) \\
&\approx \left.\frac{\partial f}{\partial x}\right|_{(x_d, u_d)} e(t) + \left.\frac{\partial f}{\partial u}\right|_{(x_d, u_d)} v(t) \\
&= A(t) e(t) + B(t) v(t)
\end{split}
$$

Solving the error LQR subproblem yields optimal feedback offset $v(t) = -K(t) e(t)$.

The total control action combines feedforward and feedback components:

$$
u(t) = u_d(t) - K(t) \left( x(t) - x_d(t) \right)
$$

# Constrained Optimal Control

Consider optimal control problems subject to control domain constraints $u(t) \in \mathcal{U}$ (e.g., $|u(t)| \le 1$) and state path constraints.

## Pontryagin's Minimum Principle

For an optimal trajectory $(x^*(t), u^*(t), \lambda^*(t))$, the optimal control input $u^*(t)$ must globally minimize the Hamiltonian $H$ over the set of admissible controls $\mathcal{U}$:

$$
u^*(t) = \argmin_{u(t) \in \mathcal{U}} H(x^*(t), u(t), \lambda^*(t), t)
$$

## Minimum-Time Problem

Consider a linear continuous-time system:

$$
\dot{x}(t) = A x(t) + B u(t), \quad x(0) = x_0
$$

subject to scalar control bounds $|u(t)| \le 1$ and target boundary condition $x(t_f) = 0$.

Performance functional (minimizing total duration $t_f$):

$$
J = \int_{t_0}^{t_f} (1) dt
$$

The Hamiltonian function is $H(x, u, \lambda, t) = 1 + \lambda^T (A x + B u)$. By Pontryagin's Minimum Principle, the optimal control is dictated by the switching function $\lambda(t)^T B$:

$$
u^*(t) = -\operatorname{sgn}(\lambda(t)^T B) = \begin{cases}
+1, & \text{if } \lambda(t)^T B < 0 \\
-1, & \text{if } \lambda(t)^T B > 0
\end{cases}
$$

This structure represents **bang-bang control**, where the control rapidly switches between its upper and lower saturation bounds.

Across control switching instances $t_s$, the trajectory satisfies the **Weierstrass-Erdmann condition**:

$$
\begin{gather*}
\lambda(t_s^-) = \lambda(t_s^+) \\
H(t_s^-) = H(t_s^+)
\end{gather*}
$$

## Minimum Control Effort Problem

Consider a control-affine nonlinear system:

$$
\dot{x}(t) = a(x(t), t) + B(x(t), t) u(t)
$$

where $B(x, t) = \begin{bmatrix} b_1(x, t) & b_2(x, t) & \dots & b_m(x, t) \end{bmatrix} \in \mathbb{R}^{n \times m}$ and $|u_i(t)| \le 1$ for $i=1, \dots, m$.

Performance index measuring control effort ($L_1$-norm penalty):

$$
J = \int_{t_0}^{t_f} \left( \sum_{i=1}^m |u_i(t)| \right) dt
$$

Minimizing the Hamiltonian component $\abs{u_i} + (\lambda^T b_i) u_i$ over $u_i \in [-1, 1]$ yields **bang-off-bang control**:

$$
u_i^*(t) = \begin{cases}
+1, & \text{if } \lambda^T b_i < -1 \\
0, & \text{if } -1 < \lambda^T b_i < 1 \\
-1, & \text{if } \lambda^T b_i > 1 \\
\in [0, 1], & \text{if } \lambda^T b_i = -1 \\
\in [-1, 0], & \text{if } \lambda^T b_i = 1
\end{cases}
$$

## Singular Control

A singular control arc occurs when the first-order condition $\nabla_u H = 0$ provides no algebraic relation to solve for $u(t)$ over a nonzero time interval $[t_1, t_2]$. This happens when $\nabla_u^2 H = 0$ (e.g., systems affine in control).

Consider an unweighted control LQR scenario ($R = 0$):

$$
\nabla_u H = B^T \lambda(t) = 0
$$

To derive $u(t)$, repeatedly differentiate the switching condition $\nabla_u H = 0$ with respect to time $t$ until $u(t)$ appears explicitly:

$$
\begin{gather*}
\frac{d}{dt} \nabla_u H = B^T \dot{\lambda} = -B^T \left( Q x + A^T \lambda \right) = 0 \\
\frac{d^2}{dt^2} \nabla_u H = -B^T Q (A x + B u) + B^T A^T \left( Q x + A^T \lambda \right) = 0
\end{gather*}
$$

Solving the second-derivative equation directly yields the singular control action $u_s(t)$.

## General Constraints

### Combined State-Control Path Constraints

For general inequality constraints $c(x, u, t) \le 0$, define the augmented Hamiltonian:

$$
H = L(x, u, t) + \lambda^T f(x, u, t) + \mu^T c(x, u, t)
$$

where Lagrange multipliers $\mu(t)$ satisfy KKT conditions:

$$
\mu(t) \ge 0, \quad c(x, u, t) \le 0, \quad \mu(t)^T c(x, u, t) = 0
$$

Adjoint dynamics and stationarity conditions:

$$
\begin{align*}
\dot{\lambda} &= -\nabla_x L - \nabla_x f^T \lambda - \nabla_x c^T \mu \\
0 &= \nabla_u L + \nabla_u f^T \lambda + \nabla_u c^T \mu
\end{align*}
$$

### Pure State Inequality Constraints

For pure state constraints $c(x(t), t) \le 0$, the control $u(t)$ does not appear explicitly in $c$.

Differentiate $c(x, t)$ w.r.t. time $t$ repeatedly until $u(t)$ explicitly appears at the $q$-th derivative (defining relative degree $q$):

$$
c^{(q)}(x, u, t) \le 0
$$

The augmented Hamiltonian incorporates the $q$-th derivative:

$$
H = L + \lambda^T f + \mu^T c^{(q)}
$$

At the entry time $t$ when the trajectory hits the constraint boundary ($c(x, t) = 0$), the **tangency (interior boundary) conditions** must be satisfied:

$$
\begin{bmatrix}
c(x, t) \\
\dot{c}(x, t) \\
\vdots \\
c^{(q-1)}(x, t)
\end{bmatrix} = 0
$$

# Dynamic Programming

## Discrete-Time DP

Consider a discrete-time finite-horizon optimal control problem:

$$
\begin{align*}
& \text{minimize} & & J = \phi(x_N, t_N) + \sum_{i=0}^{N-1} L_i(x_i, u_i) \\
& \text{subject to} & & x_{i+1} = f_i(x_i, u_i)
\end{align*}
$$

with given initial state $x_0$ and fixed discrete horizon $N$.

Define the cost-to-go functional $J_i$ from discrete stage $i$ to terminal stage $N$:

$$
J_i(x_i, u_{i:N-1}) \triangleq \phi(x_N, t_N) + \sum_{k=i}^{N-1} L_k(x_k, u_k)
$$

The **optimal value function** (or optimal cost-to-go function) $V_i(x)$ at stage $i$ is defined as

$$
V_i(x) \triangleq \min_{u_{i:N-1}} J_i(x, u_{i:N-1})
$$

### Bellman's Principle of Optimality

The discrete-time Bellman equation recurses backward in time:

$$
V_i(x) = \min_{u_i} \left[ L_i(x, u_i) + V_{i+1}(f_i(x, u_i)) \right]
$$

with terminal boundary condition:

$$
V_N(x) = \phi(x, t_N)
$$

### Discrete-Time LQR

Consider linear system dynamics with quadratic stage costs:

$$
\begin{gather*}
x_{i+1} = A_i x_i + B_i u_i \\
\phi(x) = \frac{1}{2} x^T P_f x, \qquad L_i(x, u) = \frac{1}{2} \left( x^T Q_i x + u^T R_i u \right)
\end{gather*}
$$

where $P_f, Q_i \succeq 0$ and $R_i \succ 0$.

Assume a quadratic value function $V_i(x) = \frac{1}{2} x^T P_i x$ with $P_N = P_f$:

$$
V_i(x) = \min_{u_i} \left[ \frac{1}{2} x^T Q_i x + \frac{1}{2} u_i^T R_i u_i + \frac{1}{2} (A_i x + B_i u_i)^T P_{i+1} (A_i x + B_i u_i) \right]
$$

Minimizing with respect to $u_i$ by setting the gradient to zero yields the optimal state-feedback control law:

$$
u_i^* = -\left( R_i + B_i^T P_{i+1} B_i \right)^{-1} B_i^T P_{i+1} A_i x \equiv -K_i x
$$

Substituting $u_i^*$ back into the Bellman equation gives the recursion for $P_i$:

$$
P_i = Q_i + K_i^T R_i K_i + (A_i - B_i K_i)^T P_{i+1} (A_i - B_i K_i)
$$

Eliminating explicit feedback gains $K_i$ yields the **Discrete-Time Riccati Difference Equation**:

$$
P_i = Q_i + A_i^T \left[ P_{i+1} - P_{i+1} B_i \left( R_i + B_i^T P_{i+1} B_i \right)^{-1} B_i^T P_{i+1} \right] A_i, \quad P_N = P_f
$$

## Continuous-Time DP

Define the continuous optimal value function $V(x(t), t)$ as the minimum cost-to-go evaluated from state $x(t)$ at time $t$ to terminal time $t_f$:

$$
V(x(t), t) = \min_{u(\tau), \tau \in [t, t_f]} \left[ \phi(x(t_f), t_f) + \int_{t}^{t_f} L(x(\tau), u(\tau), \tau) d \tau \right]
$$

Applying Bellman's Principle of Optimality over an infinitesimal interval $[t, t + \Delta t]$:

$$
V(x(t), t) = \min_{u(\tau), \tau \in [t, t+\Delta t]} \left[ \int_{t}^{t+\Delta t} L(x(\tau), u(\tau), \tau) d \tau + V(x(t+\Delta t), t+\Delta t) \right]
$$

### Hamilton-Jacobi-Bellman (HJB) Equation

Expanding $V(x(t+\Delta t), t+\Delta t)$ around $(x(t), t)$ via first-order Taylor series:

$$
V(x(t+\Delta t), t+\Delta t) = V(x(t), t) + \left[ \frac{\partial V}{\partial t}(x(t), t) + \nabla_x V(x(t), t)^T f(x(t), u(t), t) \right] \Delta t + o(\Delta t)
$$

Substituting back into the Bellman equation, dividing by $\Delta t$, and taking the limit $\Delta t \to 0$ yields the **Hamilton-Jacobi-Bellman (HJB) Equation**:

$$
-\frac{\partial V}{\partial t}(x, t) = \min_{u(t)} \left[ L(x, u, t) + \nabla_x V(x, t)^T f(x, u, t) \right]
$$

subject to the terminal boundary condition:

$$
V(x(t_f), t_f) = \phi(x(t_f), t_f)
$$

# Numerical Methods for Optimal Control

Formulation of the continuous-time optimal control problem:

$$
\begin{align*}
& \text{minimize} & & J = \phi(x(t_f), t_f) + \int_{t_0}^{t_f} L(x(t), u(t), t) dt \\
& \text{subject to} & & x(t_0) = x_0, \quad x(t_f) \text{ and } t_f \text{ free} \\
& & & \dot{x}(t) = f(x(t), u(t), t) \\
& & & c(x(t), u(t), t) \le 0, \quad \forall t \in [t_0, t_f] \\
& & & \psi(x(t_f), t_f) \le 0
\end{align*}
$$

Three primary paradigms for numerical optimal control:

- **Dynamic Programming**: Solves the HJB PDE (suffers from the curse of dimensionality).
- **Indirect Methods** (Optimize then Discretize): Formulates continuous first-order necessary conditions via calculus of variations and PMP, transforming the problem into a Two-Point Boundary Value Problem (TPBVP).
- **Direct Methods** (Discretize then Optimize): Discretizes the continuous-time infinite-dimensional decision space into a finite-dimensional Nonlinear Programming (NLP) problem.

## Indirect Methods

Formulate the first-order necessary conditions (ignoring inequality path constraints for basic formulation):

$$
\begin{align*}
\dot{x} &= f(x, u, t) \\
\dot{\lambda} &= -\nabla_x H(x, u, \lambda, t) \\
u^* &= \argmin_u H(x, u, \lambda, t) \\
\lambda(t_f) &= \nabla_x \phi(x(t_f), t_f) + \nabla_x \psi(x(t_f), t_f)^T \nu \\
0 &= \left[ \frac{\partial \phi}{\partial t_f} + \nu^T \frac{\partial \psi}{\partial t_f} + H \right]_{t=t_f}
\end{align*}
$$

### Indirect Single Shooting

Forward integrates the coupled Euler-Lagrange (state-costate) ODEs from $t_0$ to $t_f$ using an initial guess for unknown initial costates $\lambda(t_0)$, iteratively satisfying terminal transversality conditions via Newton-type root-finding algorithms.

- Optimization / Decision Variables:

    $$
    \begin{bmatrix}
    \lambda(t_0) & \nu & t_f
    \end{bmatrix}
    $$

- Residual Root-Finding Equations:

    $$
    \begin{bmatrix}
    \psi(x(t_f), t_f) \\
    \lambda(t_f) - \nabla_x \phi(x(t_f), t_f) - \nabla_x \psi(x(t_f), t_f)^T \nu \\
    \left[ \frac{\partial \phi}{\partial t_f} + \nu^T \frac{\partial \psi}{\partial t_f} + H \right]_{t=t_f}
    \end{bmatrix} = 0
    $$

### Indirect Multiple Shooting

Partitions the time horizon $[t_0, t_f]$ into $N$ subintervals $t_0 < t_1 < \dots < t_N = t_f$ to mitigate extreme numerical sensitivity (ill-conditioning) associated with long-horizon ODE forward integration.

Let $v_i = (x(t_i), \lambda(t_i))$ be node decision variables, and let $\bar{v}_i$ denote the state-costate trajectory obtained by forward integrating the ODEs on $[t_i, t_{i+1}]$ starting from $v_i$.

- Optimization / Decision Variables:

    $$
    \begin{bmatrix}
    \lambda(t_0) & v_1 & v_2 & \dots & v_N & \nu & t_f
    \end{bmatrix}
    $$

- Matching and Boundary Equations:

    $$
    \begin{bmatrix}
    \bar{v}_0 - v_1 \\
    \bar{v}_1 - v_2 \\
    \vdots \\
    \bar{v}_{N-1} - v_N \\
    \psi(x(t_f), t_f) \\
    \lambda(t_f) - \nabla_x \phi(x(t_f), t_f) - \nabla_x \psi(x(t_f), t_f)^T \nu \\
    \left[ \frac{\partial \phi}{\partial t_f} + \nu^T \frac{\partial \psi}{\partial t_f} + H \right]_{t=t_f}
    \end{bmatrix} = 0
    $$

## Direct Methods

Parametrizes continuous controls and/or states into a finite set of numerical parameters, reformulating optimal control as a standard Nonlinear Program (NLP).

### Direct Single Shooting

Parametrizes control trajectory $u(t)$ using finite parameters $p_k$:

$$
u(t) = \sum_{k=1}^M p_k B_k(t)
$$

where $B_k(t)$ are basis functions (e.g., piecewise constant or linear splines).

The state trajectory $x(t)$ is implicitly evaluated by forward numerical integration of the dynamics $\dot{x} = f(x, u(p), t)$ across $[t_0, t_f]$.

- NLP Decision Variables:

    $$
    \begin{bmatrix}
    p_1 & p_2 & \dots & p_M & t_f
    \end{bmatrix}
    $$

- NLP Constraints: Discretized path constraints $c(x(t_i), u(t_i), t_i) \le 0$ and terminal constraints $\psi(x(t_f), t_f) \le 0$.

### Direct Multiple Shooting

Discretizes time grid $t_0 < t_1 < \dots < t_N = t_f$. Introduces explicit discrete state nodes $x_i \approx x(t_i)$ and local control parametrization $p_i$ on each interval $[t_i, t_{i+1}]$.

Integrates system dynamics on $[t_i, t_{i+1}]$ starting from initial state node $x_i$ under control $u(p_i)$ to compute interval terminal state $\bar{x}_i$.

- NLP Decision Variables:

    $$
    \begin{bmatrix}
    p_0 & p_1 & \dots & p_{N-1} & x_1 & x_2 & \dots & x_N & t_f
    \end{bmatrix}
    $$

- NLP Continuity and Boundary Constraints:

    $$
    \begin{bmatrix}
    \bar{x}_0 - x_1 \\
    \bar{x}_1 - x_2 \\
    \vdots \\
    \bar{x}_{N-1} - x_N \\
    \psi(x_N, t_N)
    \end{bmatrix} = 0
    $$

# Optimal State Estimation

## Classification of State Estimation Methods

- Linear Models with Gaussian Noise:
    - Kalman Filter (KF): Discrete-time linear dynamics, linear observation model, additive zero-mean Gaussian noise.
    - Kalman-Bucy Filter: Continuous-time formulation of the optimal linear Gaussian estimator.
- Nonlinear Models with Gaussian Noise:
    - Extended Kalman Filter (EKF): Performs first-order Taylor series linearization of nonlinear dynamics and observation models around current state estimates.
    - Unscented Kalman Filter (UKF): Employs the Unscented Transform (UT) to deterministically propagate sigma points through the true nonlinear dynamics and measurement functions, capturing posterior mean and covariance up to third order without analytical linearization.
- Nonlinear Models with Arbitrary Noise:
    - Particle Filter: Nonparametric Sequential Monte Carlo method representing posterior probability distributions via a set of weighted random samples (particles).

## Least Squares for Static Estimation

### Unweighted Linear Least Squares

Consider the linear measurement model for an unknown parameter vector $x \in \mathbb{R}^n$ with observations $z \in \mathbb{R}^k$:

$$
z = H x + v
$$

where $v$ denotes zero-mean measurement noise.

Define measurement residual $e_z = z - H \hat{x}$ and quadratic cost functional:

$$
J(\hat{x}) = \frac{1}{2} \norm{e_z}^2
$$

- First-order necessary condition: $\hat{x} = \left( H^T H \right)^{-1} H^T z$
- Second-order sufficient condition: $\nabla_{\hat{x}}^2 J = H^T H \succ 0$. This is satisfied when $\operatorname{rank}(H) = n$.

### Weighted Least Squares with Prior Information (Bayesian MAP Form)

Incorporate prior distribution on $x$ and known measurement noise statistics:

$$
\begin{gather*}
\mathbb{E}[x] = \hat{x}_0, \qquad \mathbb{E}[(x - \hat{x}_0)(x - \hat{x}_0)^T] = P_0 \\
\mathbb{E}[v] = 0, \qquad \mathbb{E}[v v^T] = R
\end{gather*}
$$

Augmented objective functional:

$$
J(\hat{x}) = \frac{1}{2} \left( \hat{x} - \hat{x}_0 \right)^T P_0^{-1} \left( \hat{x} - \hat{x}_0 \right) + \frac{1}{2} \left( z - H \hat{x} \right)^T R^{-1} \left( z - H \hat{x} \right)
$$

- Necessary condition: $\hat{x} = \left( P_0^{-1} + H^T R^{-1} H \right)^{-1} \left( P_0^{-1} \hat{x}_0 + H^T R^{-1} z \right)$
- Sufficient condition: $\nabla_{\hat{x}}^2 J = P_0^{-1} + H^T R^{-1} H \succ 0$

Equivalently, the necessary condition takes the correction form:

$$
\begin{split}
\hat{x} &= \hat{x}_0 + (P_0^{-1} + H^T R^{-1} H)^{-1} H^T R^{-1} (z - H \hat{x}_0) \\
&= \hat{x}_0 + P H^T R^{-1} (z - H \hat{x}_0) \\
&= \hat{x}_0 + K (z - H \hat{x}_0)
\end{split}
$$

where $P = \operatorname{Cov}(\hat{x} - x) = (P_0^{-1} + H^T R^{-1} H)^{-1}$ is the posterior error covariance, and $K = P H^T R^{-1}$ is the estimation gain.

### Recursive Least Squares

Given prior estimate $(\hat{x}_0, P_0)$, for each sequential measurement $z_i = H_i x + v_i$ with $v_i \sim \mathcal{N}(0, R_i)$:

1. Kalman Gain Computation:

    $$
    K_i = P_{i-1} H_i^T \left( H_i P_{i-1} H_i^T + R_i \right)^{-1} = P_i H_i^T R_i^{-1}
    $$

2. State Estimate Update:

    $$
    \hat{x}_i = \hat{x}_{i-1} + K_i \left( z_i - H_i \hat{x}_{i-1} \right)
    $$

3. Error Covariance Update (via Woodbury Matrix Identity):

    $$
    \begin{split}
    P_i &= \left( P_{i-1}^{-1} + H_i^T R_i^{-1} H_i \right)^{-1} \\
    &= (I - K_i H_i) P_{i-1} \\
    &= P_{i-1} - P_{i-1} H_i^T \left( H_i P_{i-1} H_i^T + R_i \right)^{-1} H_i P_{i-1}
    \end{split}
    $$

## Propagation of Uncertainty

Consider continuous-time stochastic differential systems:

$$
\dot{x}(t) = f(x(t), u(t)) + L(t) w(t)
$$

where $w(t)$ is continuous-time zero-mean Gaussian white noise with autocovariance:

$$
\mathbb{E}[w(t)] = 0, \qquad \mathbb{E}[w(t) w(\tau)^T] = Q_c'(t) \delta(t - \tau)
$$

### Discrete-Time Linear System (Gauss-Markov Process)

Discrete-time linear system model:

$$
x_k = \Phi_{k-1} x_{k-1} + \Gamma_{k-1} u_{k-1} + \Lambda_{k-1} w_{k-1}
$$

where $w_{k-1} \sim \mathcal{N}(0, Q_{k-1}')$, $\mathbb{E}[w_k w_l^T] = Q_k' \delta_{kl}$, and initial state $x_0 \sim \mathcal{N}(\hat{x}_0, P_0)$.

Propagating state mean and covariance across one sampling step:

$$
\begin{align*}
\hat{x}_k &= \mathbb{E}[x_k] = \Phi_{k-1} \hat{x}_{k-1} + \Gamma_{k-1} u_{k-1} \\
P_k &= \mathbb{E}[(x_k - \hat{x}_k)(x_k - \hat{x}_k)^T] = \Phi_{k-1} P_{k-1} \Phi_{k-1}^T + Q_{k-1}
\end{align*}
$$

where process noise covariance is mapped by $Q_{k-1} = \Lambda_{k-1} Q_{k-1}' \Lambda_{k-1}^T$.

### Continuous-to-Discrete Discretization

Consider continuous-time linear time-variant (LTV) dynamics:

$$
\dot{x}(t) = F(t) x(t) + G(t) u(t) + L(t) w(t)
$$

Integration using the state-transition matrix $\Phi(t, \tau)$ yields:

$$
x(t_k) = \Phi(t_k, t_{k-1}) x(t_{k-1}) + \int_{t_{k-1}}^{t_k} \Phi(t_k, \tau) [G(\tau) u(\tau) + L(\tau) w(\tau)] d\tau
$$

Assuming zero-order hold control ($u(\tau) \equiv u_{k-1}$ on $[t_{k-1}, t_k]$):

- State Transition and Input Matrices:

    $$
    \Phi_{k-1} = \Phi(t_k, t_{k-1}), \qquad \Gamma_{k-1} = \int_{t_{k-1}}^{t_k} \Phi(t_k, \tau) G(\tau) d \tau
    $$

- Discrete Process Noise Covariance:

    $$
    \begin{split}
    Q_{k-1} &= \int_{t_{k-1}}^{t_k} \Phi(t_k, \tau) L(\tau) Q_c'(\tau) L(\tau)^T \Phi(t_k, \tau)^T d \tau \\
    &\approx L(t_{k-1}) Q_c'(t_{k-1}) L(t_{k-1})^T \Delta t
    \end{split}
    $$

## Linear-Optimal Estimation: Kalman Filter

Discrete-time LTV stochastic system with measurement model:

$$
\begin{align*}
x_k &= \Phi_{k-1} x_{k-1} + \Gamma_{k-1} u_{k-1} + \Lambda_{k-1} w_{k-1}, && w_{k-1} \sim \mathcal{N}(0, Q_{k-1}') \\
z_k &= H_k x_k + v_k, && v_k \sim \mathcal{N}(0, R_k)
\end{align*}
$$

The discrete Kalman filter recurses between prediction (time update) and correction (measurement update).

### Prediction (Time Update)

Propagate prior state estimate and error covariance:

$$
\begin{align*}
\hat{x}_{k|k-1} &= \Phi_{k-1} \hat{x}_{k-1|k-1} + \Gamma_{k-1} u_{k-1} \\
P_{k|k-1} &= \Phi_{k-1} P_{k-1|k-1} \Phi_{k-1}^T + Q_{k-1}
\end{align*}
$$

where $Q_{k-1} = \Lambda_{k-1} Q_{k-1}' \Lambda_{k-1}^T$.

### Correction (Measurement Update)

Incorporate measurement $z_k$ to obtain posterior estimates:

$$
\begin{align*}
K_k &= P_{k|k-1} H_k^T \left( H_k P_{k|k-1} H_k^T + R_k \right)^{-1} && (\text{Optimal Kalman Gain}) \\
\hat{x}_{k|k} &= \hat{x}_{k|k-1} + K_k \left( z_k - H_k \hat{x}_{k|k-1} \right) && (\text{State Estimate Update}) \\
P_{k|k} &= (I - K_k H_k) P_{k|k-1} && (\text{Posterior Covariance Update})
\end{align*}
$$

# Stochastic Control

Continuous-time stochastic optimal control under additive Gaussian white noise excitation:

$$
\begin{align*}
& \text{minimize} & & J(u(\cdot)) = \mathbb{E}\left[ \phi(x(t_f), t_f) + \int_{t_0}^{t_f} \mathcal{L}(x(\tau), u(\tau), \tau) d\tau \right] \\
& \text{subject to} & & \dot{x}(t) = f(x(t), u(t), t) + L(t) w(t)
\end{align*}
$$

where $w(t)$ is continuous-time zero-mean Gaussian white noise with autocovariance:

$$
\mathbb{E}[w(t)] = 0, \qquad \mathbb{E}[w(t) w(\tau)^T] = W(t) \delta(t - \tau), \quad W(t) \succeq 0
$$

## Perfect State Measurements

### Continuous-Time Stochastic Dynamic Programming

Define the optimal value function $V(x(t), t)$:

$$
\begin{split}
V(x(t), t) &= \min_{u(\tau), \tau \in [t, t_f]} \mathbb{E}\left[ \phi(x(t_f), t_f) + \int_t^{t_f} \mathcal{L}(x(\tau), u(\tau), \tau) d\tau \ \middle| \ x(t) \right] \\
&= \min_{u(\tau), \tau \in [t, t+\Delta t]} \mathbb{E}\left[ \int_t^{t+\Delta t} \mathcal{L}(x(\tau), u(\tau), \tau) d\tau + V(x(t+\Delta t), t+\Delta t) \ \middle| \ x(t) \right]
\end{split}
$$

Applying Itô's Lemma / second-order Taylor expansion over interval $\Delta t$ with state displacement $\Delta x \approx f \Delta t + L \Delta w$ (where $\Delta w \sim \mathcal{N}(0, W \Delta t)$):

$$
\begin{split}
V(x + \Delta x, t + \Delta t) &= V(x, t) + \frac{\partial V}{\partial t} \Delta t + \nabla_x V^T \Delta x + \frac{1}{2} \Delta x^T \nabla_x^2 V \Delta x + o(\Delta t) \\
&= V + \frac{\partial V}{\partial t} \Delta t + \nabla_x V^T (f \Delta t + L \Delta w) + \frac{1}{2} (f \Delta t + L \Delta w)^T \nabla_x^2 V (f \Delta t + L \Delta w) + o(\Delta t)
\end{split}
$$

Taking the conditional expectation $\mathbb{E}[\cdot \mid x(t)]$, noting that $\mathbb{E}[\Delta w] = 0$ and $\mathbb{E}\left[ \Delta w^T L^T \nabla_x^2 V L \Delta w \right] = \operatorname{tr}\left( \nabla_x^2 V L W L^T \right) \Delta t$:

$$
\mathbb{E}[V(x + \Delta x, t + \Delta t) \mid x] = V(x, t) + \left[ \frac{\partial V}{\partial t} + \nabla_x V^T f + \frac{1}{2} \operatorname{tr}\left( \nabla_x^2 V L W L^T \right) \right] \Delta t + o(\Delta t)
$$

Substituting back into the Bellman equation, dividing by $\Delta t$, and taking the limit $\Delta t \to 0$ yields the **Stochastic HJB Equation**:

$$
-\frac{\partial V}{\partial t}(x, t) = \min_{u(t)} \left[ \mathcal{L}(x, u, t) + \nabla_x V(x, t)^T f(x, u, t) + \frac{1}{2} \operatorname{tr}\left( \nabla_x^2 V(x, t) L(t) W(t) L(t)^T \right) \right]
$$

with terminal boundary condition $V(x(t_f), t_f) = \phi(x(t_f), t_f)$.

## Stochastic Linear-Quadratic Control

### Continuous-Time Stochastic LQR

Linear dynamics and cross-coupled quadratic performance index:

$$
\begin{gather*}
\dot{x}(t) = F(t) x(t) + G(t) u(t) + L(t) w(t) \\
J = \frac{1}{2} \mathbb{E}\left[ x(t_f)^T S_f x(t_f) + \int_{t_0}^{t_f} \begin{bmatrix} x(t) \\ u(t) \end{bmatrix}^T
\begin{bmatrix}
Q(t) & M(t) \\
M(t)^T & R(t)
\end{bmatrix}
\begin{bmatrix} x(t) \\ u(t) \end{bmatrix}
dt \right]
\end{gather*}
$$

where $S_f, Q(t) \succeq 0$, $R(t) \succ 0$, and $\begin{bmatrix} Q & M \\ M^T & R \end{bmatrix} \succeq 0$.

Assume the value function structure with quadratic state dependency and scalar variance increment:

$$
V(x, t) = \frac{1}{2} x^T S(t) x + v(t)
$$

with boundary conditions $S(t_f) = S_f$ and $v(t_f) = 0$.

Minimizing the Hamiltonian-like term inside the stochastic HJB w.r.t. $u$ yields:

$$
u^*(t) = -R^{-1}(t) \left( G(t)^T S(t) + M(t)^T \right) x(t) \equiv -K(t) x(t)
$$

Substituting $u^*(t)$ back into the stochastic HJB decouples into matrix and scalar differential equations:

$$
\begin{gather*}
-\dot{S}(t) = \left( F - G R^{-1} M^T \right)^T S + S \left( F - G R^{-1} M^T \right) + Q - S G R^{-1} G^T S - M R^{-1} M^T \\
-\dot{v}(t) = \frac{1}{2} \operatorname{tr}\left( S(t) L(t) W(t) L(t)^T \right)
\end{gather*}
$$

where the stochastic value increment is explicitly integrated as:

$$
v(t) = \frac{1}{2} \int_t^{t_f} \operatorname{tr}\left( S(\tau) L(\tau) W(\tau) L(\tau)^T \right) d\tau
$$

### Discrete-Time Stochastic LQR

Discrete linear system with additive Gaussian process noise $w_k \sim \mathcal{N}(0, W_k)$:

$$
\begin{gather*}
x_{k+1} = F_k x_k + G_k u_k + L_k w_k \\
J = \frac{1}{2} \mathbb{E}\left[ x_N^T S_N x_N + \sum_{k=0}^{N-1} \begin{bmatrix} x_k \\ u_k \end{bmatrix}^T
\begin{bmatrix}
Q_k & M_k \\
M_k^T & R_k
\end{bmatrix}
\begin{bmatrix} x_k \\ u_k \end{bmatrix}
\right]
\end{gather*}
$$

Value function representation:

$$
V_k(x_k) = \frac{1}{2} x_k^T S_k x_k + v_k
$$

Optimal state-feedback control law:

$$
u_k^* = -\left( R_k + G_k^T S_{k+1} G_k \right)^{-1} \left( M_k^T + G_k^T S_{k+1} F_k \right) x_k \equiv -K_k x_k
$$

Backward recursion for Riccati matrix $S_k$ and scalar cost increment $v_k$:

$$
\begin{gather*}
S_k = F_k^T S_{k+1} F_k + Q_k - \left( M_k^T + G_k^T S_{k+1} F_k \right)^T \left( R_k + G_k^T S_{k+1} G_k \right)^{-1} \left( M_k^T + G_k^T S_{k+1} F_k \right) \\
v_k = v_{k+1} + \frac{1}{2} \operatorname{tr}\left( S_{k+1} L_k W_k L_k^T \right)
\end{gather*}
$$

with terminal conditions $S_N = S_f$ and $v_N = 0$.
