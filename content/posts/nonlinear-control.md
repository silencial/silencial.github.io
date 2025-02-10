---
title: Nonlinear Control and Planning in Robotics
date: 2021-05-13
categories:
- Study
tags:
- JHU
- Robotics
image:
---


EN530.678 Review.

<!--more-->

---

# System Models

## Constraints

The configurations space a mechanical system is denoted by $Q$ and is assumed to be an $n$-dimensional **manifold**, locally **isomorphic** to $\mathbb{R}^n$. A configuration is denoted by $q\in Q$

**Holonomic** (or geometric) constraints: restrict possible motions to a $n-k$ dimensional sub-manifold
$$
h_i(q) = 0 \quad (i = 1,\dots,k)
$$
**Linear (Pfaffian) nonholonomic** (or kinematic): restrict the direction of velocities but it is still possible to reach any configuration in $Q$
$$
A^T(q) \dot{q} = 0
$$
Nonholonomic constraints are not integrable, i.e. it is not possible to find $k$ functions $h_i$ s.t.
$$
\nabla_{q} h_{i}(q)=a_{i}(q) \quad (i=1, \dots, k)
$$
## Dynamics

### holonomic systems

Assume the system has a Lagrangian
$$
L(q, \dot{q})=\frac{1}{2} \dot{q}^{T} M(q) \dot{q}-V(q)
$$
with inertial matrix $M(q) \succ 0$ and potential energy $V(q)$. The system is subject to external forces $f_\text{ext}(q,\dot{q})\in \mathbb{R}^n$ and control inputs $u\in \mathbb{R}^m$. The equations of motions are given by
$$
\frac{d}{d t} \nabla_{\dot{q}} L-\nabla_{q} L=f_{\text{ext}}(q, \dot{q})+B(q) u
$$
where $B(q)\in\mathbb{R}^{n\times m}$ is a matrix mapping from $m$ control inputs to the forces/torques acting on the generalized coordinates $q$.

The actual equations take the form
$$
M(q) \ddot{q}+b(q, \dot{q})=B(q) u
$$
where
$$
\begin{equation}\label{bq}
b(q, \dot{q})=\dot{M}(q) \dot{q}-\frac{1}{2} \nabla_{q}\left(\dot{q}^{T} M(q) \dot{q}\right)+\nabla_{q} V(q)-f_{\text{ext}}(q, \dot{q})
\end{equation}
$$

### nonholonomic systems

Similar to holonomic systems but the Euler-Lagrange equations become
$$
\frac{d}{d t} \nabla_{\dot{q}} L-\nabla_{q} L=A(q) \lambda+f_{\text{ext}}(q, \dot{q})+B(q) u
$$
where $\lambda\in\mathbb{R}^k$ is a vector of the Lagrange multipliers and the extra term $A(q)\lambda$ denotes the force that counters any motion in directions spanned by $A(q)$.

The actual equations take the form
$$
\begin{gather*}
M(q) \ddot{q}+b(q, \dot{q})=A(q)\lambda + B(q) u \\
A^T(q)\dot{q} = 0
\end{gather*}
$$
where $b(q)$ has the same form as $\eqref{bq}$.

The Lagrange multipliers can be eliminated by noting $A^T(q)G(q) = 0$ and multiplying the dynamics by $G^T(q)$ to obtain a reduced set of $m=n-k$ differential equations:
$$
G^{T}(q)(K(q) \ddot{q}+n(q, \dot{q}))=G^{T}(q) S(q) u
$$
A standard assumption will be that $G^T(q)S(q)\ne 0$ or that all feasible directions are controllable. The final equations are
$$
\begin{gather*}
\dot{q}=G(q) v \\
K(q) \dot{v}+n(q, v)=S(q) u
\end{gather*}
$$
where
$$
\begin{gather*}
K(q)=G^{T}(q) M(q) G(q)>0 \\
n(q, v)=G^{T} M(q) \dot{G}(q) v+G^{T}(q) b(q, G(q) v) \\
S(q)=G^{T}(q) B(q)
\end{gather*}
$$

# Stability

See [Nonlinear System]({{< ref "nonlinear-system" >}})

# Manifolds and Vector Fields

## Manifolds

A **manifold** is a set $M$ that locally "looks like" linear space, e.g. $\mathbb{R}^n$. A **chart** on $M$ is a subset $U$ of $M$ together with a bijective map $\varphi:U\to \varphi(U) \subset \mathbb{R}^n$. Two charts $U, \varphi$ and $U', \varphi'$ s.t. $U\cap U'\ne \varnothing$ are called **compatible** if $\varphi(U \cap U')$ and $\varphi'(U\cap U')$ are open subsets of $\mathbb{R}^n$ and the maps
$$
\begin{gather*}
\varphi^{\prime} \circ \varphi^{-1}\Big\vert_{\varphi\left(U \cap U^{\prime}\right)}: \varphi\left(U \cap U' \right) \to \varphi^{\prime}\left(U \cap U'\right) \\
\varphi \circ\left(\varphi^{\prime}\right)^{-1}\Big\vert_{\varphi^{\prime}\left(U \cap U^{\prime}\right)}: \varphi^{\prime}\left(U \cap U' \right) \to \varphi\left(U \cap U'\right)
\end{gather*}
$$
are $C^\infty$

![Manifold](https://i.imgur.com/d8N5xMv.png)

We call $M$ a **differentiable $n$-manifold** when:

1. The set $M$ is covered by a collections of charts, that is, every point is represented in at least one chart
2. $M$ has an **atlas**; that is, $M$ can be written as a union of compatible charts

## Tangents

Two curves $t\to c_1(t)$ and $t\to c_2(t)$ in an $n$-manifold $M$ are called **equivalent** at the point $m$ if
$$
c_1(0) = c_2(0) = m
$$
and
$$
\frac{d}{d t}\left(\varphi \circ c_{1}\right)\Big\vert_{t=0}=\frac{d}{d t}\left(\varphi \circ c_{2}\right)\Big\vert_{t=0}
$$
in some chart $\varphi$

A **tangent vector** $v$ to a manifold $M$ at point $m$ is an equivalent class of curves at $m$. The set of tangent vectors to $M$ at $m$ is a vector space, denoted by $T_m M$

The **tangent bundle** of $M$ denoted by $TM$ is the disjoint union of the tangent spaces to $M$ at the points $m\in M$, i.e.
$$
T M=\bigcup_{m \in M} T_{m} M
$$

## Vector fileds

A **vector field** $X$ on $M$ is a map $X: M \to TM$ that assigns a vector $X(m)$ at the point $m\in M$.

An **integral curve** of $X$ with initial condition $m_0$ at $t=0$ is a map $c: (a,b) \to M$ s.t. $(a,b)$ is an open interval containing $0$, $c(0) = m_0$ and $c'(t) = X(c(t))$ for all $t\in (a,b)$.

The **flow** of $X$ is a collection of maps $\Phi_t: M\to M$ s.t. $t\to \Phi_t(m)$ is the integral curve of $X$ with initial condition $m$.

## Lie bracket

Given two vector fields $g_1(x)$ and $g_2(x)$, do their flows commute? e.g. $\Phi_{t}^{g_{2}} \circ \Phi_{t}^{g_{1}}=\Phi_{t}^{g_{1}} \circ \Phi_{t}^{g_{2}}$

The difference is quantified by
$$
\Phi_{t}^{-g_{2}} \circ \Phi_{t}^{-g_{1}} \circ \Phi_{t}^{g_{2}} \circ \Phi_{t}^{g_{1}}\left(x_{0}\right)=x_{0}+t^{2}\left[g_{1}, g_{2}\right]+O\left(t^{3}\right)
$$
where $[g_1, g_2]$ is the **Lie bracket** defined by
$$
\left[g_{1}, g_{2}\right]=\frac{\partial g_{2}}{\partial x} g_{1}-\frac{\partial g_{1}}{\partial x} g_{2}
$$
or as applied to a function $\alpha$ by
$$
\left[g_{1}, g_{2}\right] \alpha=g_{1}\left(g_{2} \alpha\right)-g_{2}\left(g_{1} \alpha\right)
$$

A vector space $V$ with a bilinear operator $[\cdot, \cdot]: V\times V\to V$ is called a **Lie algebra** when satisfying:

1. Skew-symmetry: $[v, w] = -[w, v]$ for all $v, w \in V$
2. Jacobi identity: $[[v, w], z]+[[z, v], w]+[[w, z], v]=0$

# Distributions and Controllability

## Distributions

- Distributions determine possible directions of motion
- Controllability determines which states can be reached

Let $g_1(x), \dots, g_m(x)$ be linearly independent vector fields on $M$

A **distribution** $\Delta$ assigns a subspace of the tangent space to each point defined by
$$
\Delta=\operatorname{span}\{g_{1}, \dots, g_{m}\}
$$
A distribution $\Delta$ is **involutive** if it is closed under the Lie bracket, i.e.
$$
\forall f(x), g(x) \in \Delta(x), \quad[f(x), g(x)] \in \Delta(x)
$$
A distribution $\Delta$ is **regular** if the dimension of $\Delta(x)$ does not vary with $x$

A distribution $\Delta$ of constant dimension $k$ is **integrable** if $\forall x\in\mathbb{R}^n$ there are smooth functions $h_i: \mathbb{R}^n \to \mathbb{R}$ s.t. $\frac{\partial h_i}{\partial x}$ are linearly independent at $x$ and $\forall f\in\Delta$ we have
$$
L_{f} h_{i}=\frac{\partial h_{i}}{\partial x} f(x)=0, \quad i=1, \dots, n-k
$$
The **integral manifolds** of the distribution is defined as the level sets
$$
\left\{q: h_{1}(x)=c_{1}, \dots, h_{n-k}(x)=c_{n-k}\right\}
$$
**Frobenius Theorem**: A regular distribution is integrable iff it is involutive

## Controllability

### reachable sets

Consider the nonlinear control system (NCS) with $x\in\mathbb{R}^n$ and $u\in U \subset \mathbb{R}^m$:
$$
\begin{equation}\label{ncs}
\quad \dot{x}=g_{0}(x)+\sum_{i=1}^{m} g_{i}(x) u_{i}
\end{equation}
$$
A system is **controllable** if $\forall x_0, x_f \in \mathbb{R}^n$ there exists a time $T$ and $u:[0, T] \to U$ s.t $\eqref{ncs}$ satisfies $x(0)=x_0$ and $x(T)=x_f$

A system is **small-time locally controllable** (STLC) at $x_0$ if it can reach nearby points in arbitrary small times and stay near $x_0$

The **reachable set** $\mathcal{R}^V(x_0, T)$ is the set of states $x(T)$ for which there is a control $u:[0, T]\to U$ that steers the system from $x(0)$ to $x(T)$ without leaving an open set $V$ around $x_0$

The set of states reachable up to time $T$ is defined by
$$
\mathcal{R}^{V}(x_{0}, \le T)=\bigcup_{0<\tau \le T} \mathcal{R}^{V}(x_{0}, \tau)
$$

### controllability conditions

NCS is **locally accessible** (LA) from $x_0$ if for all neighborhood $V$ of $x_0$ and $T > 0$ there exists an open set $\Omega$ s.t.
$$
\Omega \subset \mathcal{R}^{V}(x_{0}, \le T)
$$
NCS is STLC if for all neighborhood $V$ of $x_0$ and $T >0$, $\mathcal{R}^{V}(x_{0}, T)$ contains a neighborhood of $x_0$

STLC $\implies$ controllable $\implies$ LA

NCS is LA from $x_0$ iff $\dim\bar{\Delta}(x_0)=n$ (LA rank condition (LARC)) where
$$
\bar{\Delta}=\operatorname{span}\left\{v \in \bigcup_{k \ge 0} \Delta^{k}\right\} \text { with }\begin{cases}
\Delta^{0}=\operatorname{span}\{g_{0}, g_{1}, \dots, g_{m}\} \\
\Delta^{k}=\Delta^{k-1}+\operatorname{span}\left\{[g_{j}, v],\quad j=0, \dots, m;\ \ v \in \Delta^{k-1}\right\}
\end{cases}
$$
For **driftless** control systems ($g_0 = 0$), STLC $\iff$ controllable $\iff$ LA. The equivalence also holds when $g_0(x) \in \operatorname{span}\{g_{1}, \ldots, g_{m}\}$ (trivial drift)

If the driftless control system
$$
\dot{q} = \sum_{i=1}^m g_i(q) v_i
$$
with state $q$ and inputs $v$ is controllable, then its **dynamic extension**
$$
\begin{align*}
&\dot{q}=\sum_{i=1}^{m} g_{i}(q) v_{i} \\
&\dot{v}_{i}=u_{i}, \quad i=1, \dots, m
\end{align*}
$$
with state $x=(q,v)$ and controls $u$ is also controllable (vice versa)

The **degree of nonholonomy**is defined as the smallest $k$ s.t.
$$
\dim \Delta^{k+1}=\dim \Delta^{k}
$$

- Completely nonholonomic: $\dim \Delta^k = n$
- Partially nonholonomic: $m < \dim \Delta^k < n$
- Holonomic: $\dim \Delta^k = m = n-k$

### good and bad brackets

A **bad bracket** is a Lie bracket generated using an odd number of $g_0$ and even number of $g_i$ vectors. A **good bracket** is one that is not bag.

NCS $\eqref{ncs}$ is STLC at $x^*$ if

1. $g_0(x^*) = 0$
2. $U$ is open and its convex hull contains $0$
3. LARC is satisfied using brackets of degree $k$
4. Any bad bracket of degree $j \le k$ can be expressed as linear combinations of good brackets of degree $< j$

# Stabilizability and Chained Forms

Given a NCS
$$
\begin{equation}\label{ncs_simple}
\dot{x} = f(x,u)
\end{equation}
$$
the goal is to construct a control law $u=K(x)$ s.t.

- Stabilization: an equilibrium point $x_e$ is made asymptotically stable, or
- Tracking: a desired feasible trajectory $x_d(t)$ is asymptotically stable

The linear approximation of the system at $x_e$ is
$$
\begin{gather*}
\delta\dot{x}=A \delta x+B \delta u \quad (\delta x=x-x_{e},\ \delta u=u-u_{e}) \\
A \triangleq \partial_x f(x_e, u_e), \quad B \triangleq \partial_u f(x_e, u_e)
\end{gather*}
$$
The NCS can be locally smoothly stabilized at $x_e$ using $\delta u = K\delta x$ if the linearized system is controllable

Necessary conditions by **Brockett's theorem**: If $\eqref{ncs_simple}$ is locally asymptotically $C^1$-stabilizable ($u$ is $C^1$-smooth) at $x_e = 0$ then the image of the map
$$
f: \mathbb{R}^{n} \times U \to \mathbb{R}^{n}
$$
contains some neighborhood of $x_e$

Nonholonomic mechanical systems cannot be stabilized at a point by smooth feedback, alternatives are

1. Time-varying feedback $u = K(t,x)$
2. Non-smooth feedback

# Trajectory generation

## Differential flatness

A system is differentially flat if one can find a set of outputs (equal to the number of inputs) which completely determine the whole state and the inputs without the need to integrate the system. More formally, a system with state $x\in\mathbb{R}^n$ and inputs $u\in\mathbb{R}^m$ is differentially flat if one can find outputs $y\in\mathbb{R}^m$ of the form
$$
y = h(x, u, \dot{u}, \dots, u^{(a)})
$$
s.t.
$$
\begin{gather*}
x=\varphi(y, \dot{y}, \dots, y^{(b)}) \\
u=\alpha(y, \dot{y}, \dots, y^{(c)})
\end{gather*}
$$
The coordinates $y$ are called **flat outputs**

## Trajectory generation

Consider the problem of generating a trajectory between two given states
$$
\begin{gather*}
\dot{x} = f(x,u) \\
x(0) = x_0,\quad x(T) = x_f
\end{gather*}
$$
The boundary conditions of a differentially flat systems are expressed as
$$
\begin{align*}
x(0) &=\varphi\big(y(0), \dot{y}(0), \dots, y^{(b)}(0)\big) \\
x(T) &=\varphi\big(y(T), \dot{y}(T), \dots, y^{(b)}(T)\big)
\end{align*}
$$
The general strategy is to assume a parametric form
$$
y(t) = A\lambda(t)
$$
where $\lambda(t)\in\mathbb{R}^N$ are basis functions and $A\in \mathbb{R}^{m\times N}$ is a constant matrix that satisfies the boundary conditions:
$$
Y = A \Lambda
$$
where $Y\in\mathbb{R}^{m\times 2(b+1)}$ and $\Lambda\in\mathbb{R}^{N\times 2(b+1)}$ are
$$
\begin{gather*}
Y = \left[y(0), \dots, y^{(b)}(0), y(T), \dots, y^{(b)}(T)\right] \\
\Lambda = \left[\lambda(0), \dots, \lambda^{(b)}(0), \lambda(T), \dots, \lambda^{(b)}(T)\right]
\end{gather*}
$$
It is necessary that $N\ge 2(b+1)$

# Feedback Linearization

## Input-output linearization

Consider the nonlinear control system
$$
\begin{align*}
\dot{x} &=f(x)+G(x) u \\
y &=h(x)
\end{align*}
$$
Input-output linearization is to use transformation of $u$ so that the input-output response between $v$ and $y$ is linear.

**Static feedback**:
$$
u=a(x) + B(x)v
$$
 where $B(x)$ is a nonsingular matrix, and $v$ is called **virtual input**

**Dynamic feedback**:
$$
\begin{gather*}
u=a(x, \xi)+B(x, \xi) v \\
\dot{\xi}=c(x, \xi)+D(x, \xi) v
\end{gather*}
$$
where $\xi$ is the **compensator state**

## Static feedback

### fully-actuated manipulator control

$$
\begin{equation}\label{fully-actuated}
M(q) \ddot{q}+b(q, \dot{q})=u
\end{equation}
$$

Assume that one is interested in tracking a desired path $q_d(t)$. The task is specified by the output
$$
y=q
$$
Choose the virtual input $v = \ddot{q}$ and control law
$$
v=\ddot{q}_{d}-k_{d}\left(\dot{q}-\dot{q}_{d}\right)-k_{p}\left(q-q_{d}\right)
$$
which result in a closed-loop linear dynamics
$$
\begin{gather*}
\dot{z} = Az \\
z=\begin{pmatrix}
q-q_{d} \\
\dot{q}-\dot{q}_{d}
\end{pmatrix} \\
A=\begin{pmatrix}
0 & I \\
-k_p I & -k_d I
\end{pmatrix}
\end{gather*}
$$
where $A$ is Hurwitz. The virtual controls are mapped back to the original input $u$ using $\eqref{fully-actuated}$.

In general, whenever $\dim(Q) = \dim(U)$ one can always use nonlinear static feedback to achieve linearization.

### partial feedback linearization

Consider an underactuated system
$$
M(q) \ddot{q}+b(q, \dot{q})=\begin{pmatrix}
0 \\ u
\end{pmatrix}
$$

which can be equivalently expressed as
$$
\begin{bmatrix}
M_{11} & M_{12} \\
M_{21} & M_{22}
\end{bmatrix}
\begin{bmatrix}
\ddot{q_1} \\
\ddot{q_2}
\end{bmatrix} +
\begin{bmatrix}
b_1 \\ b_2
\end{bmatrix} =
\begin{bmatrix}
0 \\ u
\end{bmatrix}
$$
**Collocated input/output linearization**: If the output is $y=q_2\in\mathbb{R}^m$, we can get
$$
\bar{M}_{22}\ddot{q}_2 + \bar{b}_2 = u
$$
where
$$
\begin{gather*}
\bar{M}_{22} = M_{22} - M_{21}M_{11}^{-1}M_{12} \\
\bar{b}_2 = b_2 - M_{21}M_{11}^{-1}b_1
\end{gather*}
$$
The rest is similar as the fully actuated control case.

The $q_1$ is the remaining "non-linearized" coordinates. We can define
$$
\eta = \begin{bmatrix}
\eta_1 \\ \eta_2
\end{bmatrix} =
\begin{bmatrix}
q_1 \\ \dot{q}_1
\end{bmatrix}
$$
and we have the dynamics
$$
\begin{gather*}
\dot{\eta}_1 = \eta_2 \\
\dot{\eta}_2 = -M_{11}^{-1}(M_{12}(\ddot{q}_d - k_p z_1 - k_d z_2) + b_1)
\end{gather*}
$$
and the complete system can be written as
$$
\begin{equation}\label{collocated}
\begin{aligned}
&\dot{z} = Az & &\text{linearized} \\
&\dot{\eta} = w(t, z,\eta) & &\text{non-linearized}
\end{aligned}
\end{equation}
$$
The **zero dynamics** of the system is defined as the evolution of the non-linearized part after the linear part has stabilized, i.e.
$$
\dot{\eta} = w(t, 0, \eta)
$$
Suppose $w(t, 0,\eta_0) = 0$ for $t\ge 0$, i.e. $(0, \eta_0)$ is the equilibrium of the full system $\eqref{collocated}$, and $A$ is Hurwitz. Then $(0, \eta_0)$ is locally stable if $\eta_0$ is locally stable for the zero dynamics. (respectively, locally asymptotically stable, unstable)

**Non-collocated input/output linearization**: If the output is $y = q_1\in \mathbb{R}^l$, then it can only be linearized when $l\le m$ and $\operatorname{rank}(M_{12}) = l$. We can get
$$
\tilde{M}_{21} \ddot{q}_1 + \tilde{b}_2 = u
$$
where
$$
\begin{gather*}
\tilde{M}_{21} = M_{21} = M_{22}M_{12}^\dagger M_{11} \\
\tilde{b}_2 = b_2 - M_{22}M_{12}^\dagger b_1
\end{gather*}
$$
$M_{12}^\dagger = M_{12}^T(M_{12} M_{12}^T)^{-1}$ is the right pseudo-inverse of $M_{12}$

The rest is similar.

## Dynamic feedback

If a control system is differentially flat then it is dynamic feedback linearizable on an open dense set, with the dynamic feedback possibly depending explicitly on time

## General case

### SISO system

Consider
$$
\begin{gather*}
\dot{x} = f(x) + g(x) u \\
y = h(x)
\end{gather*}
$$
where $u\in\mathbb{R}$. Let $x^*$ be the equilibrium, then we have
$$
\dot{y} = \nabla h^T [f(x) + g(x) u] = L_f h + u L_g h
$$

If $|L_g h| \ne 0$
$$
u = \frac{1}{L_g h}(-L_f h + v) \\
\dot{y} = v
$$

More generally, we can keep differentiating $\dot{y}, \ddot{y}, \dots$ and denote $\gamma$ to be the smallest integer s.t.
$$
\begin{gather*}
L_gL_f^i h = 0 \quad (i = 0, \dots, \gamma - 2) \\
L_gL_f^{\gamma -1} h \ne 0
\end{gather*}
$$

Then we have
$$
\begin{gather*}
u = \frac{1}{L_gL_f^{\gamma-1}h}(-L_f^\gamma h + v) \\
y^{(\gamma)} = v
\end{gather*}
$$
i.e. the output becomes a $\gamma$-order linear system. $\gamma$ is called the **strict relate degree**

### MIMO system

Consider a two-input two-output system
$$
\begin{gather*}
\dot{x}=f(x)+g_{1}(x) u_{1}+g_{2}(x) u_{2} \\
y=\begin{bmatrix}
y_{1} \\
y_{2}
\end{bmatrix}=\begin{bmatrix}
h_{1}(x) \\
h_{2}(x)
\end{bmatrix}
\end{gather*}
$$
Differentiate both outputs until inputs appeared:
$$
\begin{split}
\begin{bmatrix}
y_1^{(\gamma_1)} \\ y_2^{(\gamma_2)}
\end{bmatrix} & =
\begin{bmatrix}
L_{g_{1}} L_{f}^{\gamma_{1}-1} h_{1} & L_{g_{2}} L_{f}^{\gamma_{1}-1} h_{1} \\
L_{g_{1}} L_{f}^{\gamma_{2}-1} h_{2} & L_{g_{2}} L_{f}^{\gamma_{2}-1} h_{2}
\end{bmatrix}
\begin{bmatrix}
u_1 \\ u_2
\end{bmatrix} +
\begin{bmatrix}
L_f^{\gamma_1}h_1 \\ L_f^{\gamma_2}h
\end{bmatrix} \\
&\triangleq G(x) u + H(x)
\end{split}
$$
The system has a relative degree $(\gamma_1, \gamma_2)$ at $x^*$ if
$$
L_{g_{j}} L_{f}^{k} h_{i}(x)=0, \quad (j=1,2,\ \ 0 \le k \le \gamma_{i}-2, \ \ i=1,2)
$$
and $G$ is non-singular. Then
$$
\begin{gather*}
u = G^{-1}(x) [H(x) + v] \\
\begin{bmatrix}
y_1^{(\gamma_1)} \\ y_2^{(\gamma_2)}
\end{bmatrix} =
\begin{bmatrix}
v_1 \\ v_2
\end{bmatrix}
\end{gather*}
$$

### normal forms

If a SISO has a relative degree $\gamma \le n$ at some point $x^*$ then it can be transformed into a normal form, i.e. one can find a change of coordinates $x\to\Phi(x)$ s.t.
$$
\Phi(x) = \begin{bmatrix}
z \\ \eta
\end{bmatrix} \triangleq
\begin{bmatrix}
h(x) \\ L_f h \\ \vdots \\ L_f^{\gamma-1}h \\
\eta_1(x) \\ \vdots \\ \eta_{n-\gamma}(x)
\end{bmatrix}
$$
The last $n-\gamma$ coordinates $\eta$ are chosen so that the following conditions holds:

1. $\Phi(x)$ is a **diffeomorphism**, i.e. a smooth map with smooth inverse. Equivalent to $\partial \Phi$ has full rank
2. The dynamics of $\dot{\eta}$ is not directly affected by $u$, i.e. $L_g\eta_i = 0$. It means that $\eta$ are the internal dynamics $\dot{\eta} = w(t,z,\eta)$

So we have two parts of the dynamics: $z$-dynamics and internal dynamics
$$
\begin{gather*}
\dot{z}=A z+B v \\
\dot{\eta}=w(t, z, \eta)
\end{gather*}
$$
where
$$
A = \begin{bmatrix}
0 \\
\vdots & I_{\gamma-1} \\
0 & \cdots & 0
\end{bmatrix} \qquad
B = \begin{bmatrix}
0 \\ \vdots \\ 0 \\ 1
\end{bmatrix}
$$
$z$ is controllable. The virtual input $u$ can be chosen as $v=Kz$ s.t. $A+BK$ is Hurwitz. And the control is
$$
u = \frac{1}{L_gL_f^{\gamma-1}h}(-L_f^\gamma h + v)
$$
If the zero dynamics
$$
\dot{\eta} = w(t, 0, \eta)
$$
is A.S. then the system is **minimum phase**, otherwise it is non-minimum phase

# Backstepping

Backstepping is a nonlinear control design tool for underactuated systems.

## Integrator Backstepping

Consider
$$
\begin{align}
\dot{\eta} &= f(\eta) + g(\eta)\xi \label{integrator} \\
\dot{\xi} &= u \nonumber
\end{align}
$$
where $[\eta^T, \xi]\in\mathbb{R}^{n+1}$ and $u\in\mathbb{R}$ is the control input. The functions $f,g$ are smooth in  the domain that contains $0$ and $f(0)=0$. The goal is to design a controller which stabilizes the origin $(\eta, \xi)=(0,0)$

Assume there is a control law $\xi=\phi(\eta)$ which makes the subsystem $\eqref{integrator}$ A.S. with $\phi(0)=0$ and the Lyapunov function $V_0(\eta)$ s.t.
$$
\frac{\partial V_{0}}{\partial \eta}[f(\eta)+g(\eta) \phi(\eta)] \le-W(\eta) \quad (\forall \eta \in D)
$$
where $W(\eta)$ is P.D. Now using

$$
\require{mathtools}
\DeclarePairedDelimiters\norm{\lVert}{\rVert}
V(\eta, \xi) = V_0(\eta) + \frac{1}{2} \norm{\xi-\phi(\eta)}^2
$$
and we can obtain
$$
\begin{split}
\dot{V} &=\frac{\partial V_{0}}{\partial \eta}(f+g\xi) + (\xi-\phi) (u - \frac{\partial \phi}{\partial \eta} \dot{\eta}) \\
& \le -W(\eta) + (\xi-\phi) (u - \frac{\partial \phi}{\partial \eta} \dot{\eta} + \frac{\partial V_{0}}{\partial \eta} g)
\end{split}
$$

If we set
$$
u=\frac{\partial \phi}{\partial \eta}(f+g\xi)-\frac{\partial V_{0}}{\partial \eta} g-k(\xi-\phi) \quad (k> 0)
$$
then $\dot{V} < 0$. So that the origin $(\eta, z)=(0,0)$ is A.S. From $\phi(0)=0$ we can get the $(\eta,\xi)=(0,0)$ is A.S.

## Block backstepping

Consider
$$
\begin{align}
\dot{\eta}&=f(\eta)+G(\eta) \xi \label{block} \\
\dot{\xi}&=f_{a}(\eta, \xi)+G_{a}(\eta, \xi) u \nonumber
\end{align}
$$
where $\eta\in\mathbb{R}^n$, $\xi\in\mathbb{R}^m$ and $u\in\mathbb{R}^m$ is the control input. The functions $f, f_a, G, G_a$ are smooth in the interested domain, $f(0) = f_a(0) = 0$ and $G_a$ is a non-singular $m\times m$ matrix.

Assume there is a control law $\xi=\phi(\eta)$ which makes the subsystem $\eqref{block}$ A.S. with $\phi(0)=0$ and the Lyapunov function $V_0$ s.t.
$$
\frac{\partial V_{0}}{\partial \eta}[f(\eta)+G(\eta) \phi(\eta)] \le -W(\eta) \quad (\forall \eta \in D)
$$
where $W(\eta)$ is P.D. Now using
$$
V(\eta, \xi) = V_0(\eta) + \frac{1}{2} \norm{\xi-\phi(\eta)}^2
$$
and we can obtain
$$
\begin{split}
\dot{V}&=\frac{\partial V_{0}}{\partial \eta}(f+G \xi)+[\xi-\phi]^{T}\left[f_{a}+G_{a} u-\frac{\partial \phi}{\partial \eta}\dot{\eta}\right] \\
&\le -W(\eta) + [\xi-\phi]^{T}\left[f_{a}+G_{a} u-\frac{\partial \phi}{\partial \eta}\dot{\eta} + \left(\frac{\partial V_{0}}{\partial \eta} G\right)^T \right]
\end{split}
$$
If we set
$$
u=G_{a}^{-1}\left[\frac{\partial \phi}{\partial \eta}(f+G \xi) - \left(\frac{\partial V_{0}}{\partial \eta} G\right)^{T} - f_a - k(\xi-\phi)\right] \quad (k>0)
$$
then $\dot{V} < 0$. So that the origin $(\eta, \xi)=(0,0)$ is A.S.

# Lyapunov Redesign and Robust Backstepping

## Uncertainty and Lyapunov redesign

Consider
$$
\begin{equation}\label{uncertain}
\dot{x} = f(t,x) + G(t,x) [u + \delta(t,x,u)]
\end{equation}
$$
where $x\in\mathbb{R}^n$ is the state and $u\in\mathbb{R}^p$ is the control. The functions $f,G,\delta$ are defined for $(x,u)\in D\times \mathbb{R}^p$ ($D$ contains the origin), piecewise continuous and Lipschitz in $x$ and $u$. Assume $f,G$ is know while $\delta$ is unknown.

When the uncertainty acts only along the control vector fields (the column of the matrix $G$) it is said to satisfy the **matching condition**, i.e. it matches the controls. $\eqref{uncertain}$ is in such form. Stabilizing controls for this case can be done by **Lyapunov redesign**. In the non-matching case, it is necessary to assume more restrictive assumptions about the bounds of $\delta$ and employ recursive techniques such as **robust backstepping**.

Assume that a feedback controller $u=\psi(t,x)$ was designed so that the nominal system
$$
\dot{x} = f(t,x) + G(t,x)u
$$
is A.S. And the Lyapunov function $V(t,x)$ satisfies
$$
\begin{gather*}
\alpha_{1}(\norm{x}) \le V(t, x) \le \alpha_{2}(\norm{x}) \\
\partial_{t} V+\partial_{x} V \cdot[f(t, x)+G(t, x) \psi(t, x)] \le -\alpha_{3}(\norm{x})
\end{gather*}
$$
for all $x\in D$,$\alpha_i$ are strictly increasing and $\alpha_i(0)=0$

Assume the uncertainty satisfies the bound
$$
\begin{equation}\label{bound}
\norm{\delta(t, x, \psi(t, x)+v)} \le \rho(t, x)+k_{0}\norm{v} \qquad (0 \le k_{0}<1)
\end{equation}
$$
where $\rho:[0,t_f]\times D\to \mathbb{R}$ is a non-negative continuous function and specifies the magnitude of the uncertainty. The idea behind Lyapunov redesign is to augment the nominal control law $\psi(t,x)$ with an extra term $v\in\mathbb{R}^p$ which suppresses the uncertainty so that the combined control $u=\psi(t,x)+v$ stabilizes the real system.

Now $\dot{V}$ becomes
$$
\begin{split}
\dot{V}&=\partial_{t} V+\partial_{x} V \cdot[f+G \psi]+\partial_{x} V \cdot G[v+\delta] \\
&\le -\alpha_{3}(\norm{x})+\partial_{x} V \cdot G[v+\delta]
\end{split}
$$
Setting $w^T = \partial_x V\cdot G$ and it becomes
$$
\dot{V}\le -\alpha_3 \norm{x} + w^T v + w^T \delta
$$
Using the bound $\eqref{bound}$ we have
$$
w^{T} v+w^{T} \delta \le w^{T} v+\norm{w} (\rho+k_{0}\norm{v})
$$
Setting
$$
\begin{gather*}
v = -\eta(t,x)\frac{w}{\norm{w}} \\
\eta(t,x) \ge \frac{\rho(t,x)}{1-k_0}
\end{gather*}
$$
we have
$$
\begin{split}
w^{T} v+w^{T} \delta &\le-\eta(x)\norm{w}+\norm{w}\left(\rho+k_{0} \eta(x)\right) \\
&=\norm{w}\left(\rho-\eta\left(1-k_{0}\right)\right) \\
&\le 0
\end{split}
$$
So that $\dot{V} \le 0$ for the whole system.

The controller is discontinuous at $w=0$, e.g. typically at the origin. In addition to this theoretical limitation, practical issues also occur due to digital switching, delays, and other physical imperfections. This results in oscillatory behavior near the equilibrium called **chattering**. The solution is to smooth the control law near the origin:
$$
\begin{align*}
v &=-\eta(t, x) \frac{w}{\norm{w}} & &\text{if } \eta(t, x)\norm{w} \ge \epsilon \\
v &=-\eta(t, x)^{2} \frac{w}{\epsilon} & &\text{if } \eta(t, x)\norm{w}<\epsilon
\end{align*}
$$

## Robust backstepping

Consider the single-input system
$$
\begin{align}
\dot{\eta}&=f(\eta)+g(\eta) \xi+\delta_{\eta}(\eta, \xi) \label{robust-bs} \\
\dot{\xi}&=f_{a}(\eta, \xi)+g_{a}(\eta, \xi) u+\delta_{\xi}(\eta, \xi) \nonumber
\end{align}
$$
where $\eta\in\mathbb{R}^n$, $\xi\in\mathbb{R}$ are defined over a domain $D$ that contains the origin. Assume $f,g,f_a,g_a$ are smooth and known, $f(0)=f_a(0)=0$. And $\delta_n, \delta_\xi$ are uncertain terms that satisfy
$$
\begin{equation}\label{assume-1}
\begin{aligned}
\norm{\delta_{\eta}(\eta, \xi)}_{2} &\le a_{1}\norm{\eta}_{2} \\
|\delta_{\xi}(\eta, \xi)| &\le a_{2}\norm{\eta}_{2}+a_{3}|\xi|
\end{aligned}
\end{equation}
$$
for all $(\eta, \xi)$

Assume we have a stabilizing controller $\xi=\phi(\eta)$ for the subsystem $\eqref{robust-bs}$ and a Lyapunov function $V_0(\eta)$ s.t.
$$
\frac{\partial V_{0}}{\partial \eta}\left[f(\eta)+g(\eta) \phi(\eta)+\delta_{\eta}(\eta, \xi)\right] \le -b\norm{\eta}^{2}
$$
for some $b>0$. Suppose further that $\phi(\eta)$ satisfies
$$
\begin{equation}\label{assume-2}
|\phi(\eta)| \le a_{4}\norm{\eta} \qquad\norm*{\frac{\partial \phi}{\partial \eta}} \le a_{5}
\end{equation}
$$
Consider a Lyapunov function
$$
V(\eta, \xi) = V_0(\eta) +\frac{1}{2}[\xi - \phi(\eta)]^2
$$
we have
$$
\dot{V}=\frac{\partial V_{0}}{\partial \eta}\left[f+g \phi+\delta_{\eta}\right]+\frac{\partial V_{0}}{\partial \eta} g(\xi-\phi)+(\xi-\phi)\left[f_{a}+g_{a} u+\delta_{\xi}-\frac{\partial \phi}{\partial \eta}\left(f+g \xi+\delta_{\eta}\right)\right]
$$
Taking
$$
u=\frac{1}{g_{a}}\left[\frac{\partial \phi}{\partial \eta}(f+g \xi)-\frac{\partial V_{0}}{\partial \eta} g-f_{a}-k(\xi-\phi)\right] \quad (k>0)
$$
so we have
$$
\dot{V} \le -b\norm{\eta}^{2}+(\xi-\phi)\left[\delta_{\xi}-\frac{\partial \phi}{\partial \eta} \delta_{\eta}\right]-k(\xi-\phi)^{2}
$$
Using assumptions $\eqref{assume-1}$, $\eqref{assume-2}$ then
$$
\begin{split}
\dot{V} & \le-b\norm{\eta}^{2}+2 a_{6}|\xi-\phi|\norm{\eta}-\left(k-a_{3}\right)|\xi-\phi|^{2} \\
&=-\begin{bmatrix}
\norm{\eta} \\
|\xi-\phi|
\end{bmatrix}^T\begin{bmatrix}
b & -a_{6} \\
-a_{6} & (k-a_{3})
\end{bmatrix}\begin{bmatrix}
\norm{\eta} \\
|\xi-\phi|
\end{bmatrix}
\end{split}
$$
for some $a_6 > 0$. Taking $k\ge a_3 + a_6^2/b$ yields $\dot{V} \le 0$
