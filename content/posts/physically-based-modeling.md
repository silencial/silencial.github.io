---
title: Physically Based Modeling
date: 2018-03-20
lastmod: 2025-05-27
categories:
- Study
tags:
- CG
- Paper Reading
---

Based on course note [Physically Based Modeling: Principles and Practice](http://www.cs.cmu.edu/~baraff/sigcourse/)

<!--more-->

---

# Newton's Method

$$
\begin{gather*}
f(x)\approx f(x_n)+f'(x_n)\Delta x+\frac{1}{2}f''(x_n)\Delta x^2 \\
0=\frac{d}{d\Delta x}\left(f(x_n)+f'(x_n)\Delta x+\frac{1}{2}f''(x_n)\Delta x^2\right)=f'(x_{n})+f''(x_{n})\Delta x
\end{gather*}
$$

Thus,

$$
x_{n+1}=x_n-\mathbf{H}^{-1}\nabla f(x_n)
$$

where $\mathbf{H}=\nabla^2f$ is the Hessian matrix

# Euler Method

$$
\mathbf{x}(t_0 + h) = \mathbf{x}_0+h \dot{\mathbf{x}}(t_0)
$$

Euler method is the simplest numerical integration method and achieves $O(h^2)$ accuracy. However, it has several disadvantages. It produces an outward drift when simulating orbiting movements, leading to inaccuracies. Furthermore, it can become unstable if the step size is too large.

Euler method can be improved by including more terms in the Taylor series.

# Improving the Euler Method

## Midpoint Method

To achieve $O(h^3)$ accuracy, consider the Taylor series:

$$
\begin{equation}
\label{MM}
\mathbf{x}(t_0 + h) = \mathbf{x}(t_0)  + h\dot{\mathbf{x}}(t_{0}) +\dfrac{h^2}{2} \ddot{\mathbf{x}}(t_0) + O(h^3)
\end{equation}
$$

Note that $\dot{\mathbf{x}}$ is given by a function $f(\mathbf{x}(t), t)$. For simplicity, assume $f$ depends on time only indirectly through $\mathbf{x}$. Thus,

$$
\ddot{\mathbf{x}} = \dfrac{\partial f}{\partial  \mathbf{x}} \dot{\mathbf{x}} =f'f
$$

To avoid evaluating $f'$, another Taylor expansion is performed:

$$
f(\mathbf{x}_0 + \Delta\mathbf{x}) = f(\mathbf{x}_0) + \Delta\mathbf{x} f'(\mathbf{x}_0) + O(\Delta \mathbf{x}^2 )
$$

By choosing $\Delta\mathbf{x} = \dfrac{h}{2} f( \mathbf{x}_0 )$ and multiplying both sides by $h$, we can introduce $\ddot{\mathbf{x}}$, yielding:

$$
\dfrac{h^2}{2} \ddot{\mathbf{x}}(t_{0}) + O(h^3) = h \left(  f \left( \mathbf{x}_0 + \dfrac{h}{2} f(\mathbf{x}_0) \right) - f(\mathbf{x}_0)\right)
$$

Substituting the right-hand side into $\eqref{MM}$ yields the update formula:

$$
\mathbf{x}(t_0 + h) = \mathbf{x}(t_0) + h\left(f\big( \mathbf{x}_0 + \dfrac{h}{2}f(\mathbf{x}_0) \big) \right)
$$

Evaluating $f$ multiple times leads to more accurate methods, such as the [[Runge-Kutta methods]].

## Adaptive Step Size

A significant challenge in numerical methods is determining an appropriate step size $h$. We want to choose $h$ as large as possible while maintaining numerical stability; thus, $h$ is varied during the ODE solving process.

An adaptive step size technique for the Euler method is presented below. Two estimates for $\mathbf{x}(t_0 + h)$ are computed: $\mathbf{x}_a$ is obtained by taking one step of size $h$, and $\mathbf{x}_b$ is obtained by taking two steps of size $\frac{h}{2}$. The current error $e$ can be defined as $e = \|\mathbf{x}_a - \mathbf{x}_b\|$, and a tolerance error is denoted by $\text{tol}$. The step size can then be adjusted at each step:

$$h_{\text{new}} = h_{\text{old}} \left( \frac{\text{tol}}{e} \right) ^ \frac{1}{2}$$

# Particle System Dynamics

The combined position and velocity space is known as the [phase space](https://en.wikipedia.org/wiki/Phase_space). The entire system can be viewed as a single point moving through this $6n$-dimensional space.

Forces can be grouped into three broad categories:

- Unary forces (e.g., gravity, drag) that act independently on each particle.
- $n$-ary forces (e.g., springs) that apply forces to a predefined set of particles.
- Forces of spatial interaction (e.g., attraction, repulsion) that may act on any or all pairs of particles.

# Implicit Euler Method

$$
\begin{gather*}
\mathbf{q}_{n+1}=\mathbf{q}_n+h\mathbf{v}_{n+1} \\
\mathbf{v}_{n+1}=\mathbf{v}_n+h\mathbf{M}^{-1}(\mathbf{f}_{\text{int}}(\mathbf{q}_{n+1})+\mathbf{f}_{\text{ext}})
\end{gather*}
$$

This system can be converted to an optimization problem:

$$
\min_{\mathbf{q}_{n+1}} \frac{1}{2h^2} \left\|\mathbf{M}^{\frac{1}{2}} (\mathbf{q}_{n+1} - \mathbf{s}_n)\right\|_F^2 + \sum_i W_i(\mathbf{q}_{n+1})
$$

where $\mathbf{s}_n = \mathbf{q}_n + h\mathbf{v}_n + h^2\mathbf{M}^{-1}\mathbf{f}_{\text{ext}}$ and $W_i(\mathbf{q})$ is a scalar potential energy function.

# Constrained Dynamics

Using additional energy terms to enforce constraints is known as the penalty method. The difficulty with this method is that applied and restoring forces interact only indirectly via displacements. This makes achieving accuracy without introducing stiffness challenging.

## General Case

The equations of motion are

$$
\ddot{\mathbf{q}}  = \mathbf{WQ}
$$

where $\mathbf{q}$ represents position, $\mathbf{W} = \mathbf{M}^{-1}$ is the inverse of the mass matrix, and $\mathbf{Q}$ is the force vector.

First, define the equality constraint equations as $\mathbf{C}(\mathbf{q}) = \mathbf{0}$. Assume that initially, both $\mathbf{q}$ and $\dot{\mathbf{q}}$ satisfy these constraints, meaning $\mathbf{C}(\mathbf{q}(t_0)) = \mathbf{0}$ and $\dot{\mathbf{C}}(\mathbf{q}(t_0), \dot{\mathbf{q}}(t_0)) = \mathbf{0}$. The problem is to find a constraint force $\hat{\mathbf{Q}}$ such that when added to the applied force $\mathbf{Q}$, it ensures $\ddot{\mathbf{C}} = \mathbf{0}$.

Using the chain rule:

$$
\ddot{\mathbf{C}}  = \dot{\mathbf{J}} \dot{\mathbf{q}} + \mathbf{J} \ddot{\mathbf{q}}
$$

where $\mathbf{J} = \frac{\partial \mathbf{C}}{\partial \mathbf{q}}$ is the Jacobian of the constraint equations.

Substitute $\ddot{\mathbf{q}}=\mathbf{W}(\mathbf{Q} + \hat{\mathbf{Q}})$ into the equation and set $\ddot{\mathbf{C}} = 0$:

$$
\begin{equation}
\label{CD}
\mathbf{JW} \hat{\mathbf{Q}} = -\dot{\mathbf{J}} \dot{\mathbf{q}} - \mathbf{JWQ}
\end{equation}
$$

To ensure that the constraint force does no work (i.e., is perpendicular to all virtual displacements $\delta\mathbf{q}$ satisfying $\mathbf{J}\delta\mathbf{q}=\mathbf{0}$), we require $\hat{\mathbf{Q}} \cdot \delta\mathbf{q} = 0$. This implies

$$\hat{\mathbf{Q}} = \mathbf{J}^T \boldsymbol{\lambda}$$

where $\boldsymbol{\lambda}$ is a vector of Lagrange multipliers.

In this expression, the rows of $\mathbf{J}$ represent vectors normal to the constraint hypersurfaces. Thus, $\mathbf{J}^T \boldsymbol{\lambda}$ is a linear combination of these normal vectors, spanning the space of forces that can maintain the constraints. Consequently, its dot product with any legal velocity vector $\dot{\mathbf{q}}$ (where $\mathbf{J}\dot{\mathbf{q}} = \mathbf{0}$) will be zero.

Substitute this into $\eqref{CD}$:

$$
\mathbf{JW} \mathbf{J}^T \lambda = -\dot{\mathbf{J}} \dot{\mathbf{q}} - \mathbf{JWQ}
$$

## Lagrangian Dynamics with Generalized Coordinates

If constraints are incorporated by representing system configurations using a set of independent generalized coordinates $\mathbf{u}$, such that $\mathbf{q} = \mathbf{q}(\mathbf{u})$, then the Jacobian of the transformation is

$$
\mathbf{J} = \frac{\partial \mathbf{q}} {\partial \mathbf{u}}
$$

The principle of virtual work requires $\hat{\mathbf{Q}} \cdot (J\delta\mathbf{u}) = 0$ for all $\delta \mathbf{u}$, which simply means that $\mathbf{J}^T \hat{\mathbf{Q}} = 0$. As before, write the unconstrained equations of motion as

$$
\mathbf{M}\ddot{\mathbf{q}} = \mathbf{Q} + \hat{\mathbf{Q}}
$$

Multiplying both sides by $\mathbf{J}^T$ and substituting $\ddot{\mathbf{q}} = \mathbf{J}\ddot{\mathbf{u}} + \dot{\mathbf{J}}\dot{\mathbf{u}}$ gives

$$
\mathbf{J}^T \mathbf{MJ} \ddot{\mathbf{u}} + \mathbf{J}^T \mathbf{M} \dot{\mathbf{J}} \dot{\mathbf{u}} - \mathbf{J}^T \mathbf{Q} = 0
$$

# Rigid Body Dynamics

## Unconstrained Rigid Body Dynamics

If $p_0$ is an arbitrary point fixed in the body's local coordinate system, its world-space position $p(t)$ is given by:

$$
p(t) = R(t) p_0 + x(t)
$$

where $R(t)$ is the rotation matrix that describes the rotation of the body in the world-space, and $x(t)$ is the translation vector that describes the world-space position of the body's origin. For simplicity, we choose the center of mass of the body as the origin.

The time derivatives of the position and orientation of the body are:

$$
\begin{gather*}
v(t) = \dot{x}(t) \\
\dot{R}(t) =
\begin{pmatrix}
\omega(t) \times \begin{pmatrix} r_{xx} \\ r_{xy} \\ r_{xz} \end{pmatrix}
&
\omega(t) \times \begin{pmatrix} r_{yx} \\ r_{yy} \\ r_{yz} \end{pmatrix}
&
\omega(t) \times \begin{pmatrix} r_{zx} \\ r_{zy} \\ r_{zz} \end{pmatrix}
\end{pmatrix}
\end{gather*}
$$

If we define the skew-symmetric matrix $\omega^*$ as:

$$
w^* = 
\begin{pmatrix}
0 & -\omega_z & \omega_y \\
\omega_z & 0 & -\omega_x \\
-\omega_y & \omega_x & 0
\end{pmatrix}
$$

we can rewrite $\dot{R}(t)$ as:

$$
\dot{R}(t) = \omega(t)^* R(t)
$$

The velocity of an arbitrary point $i$ on the rigid body, with body-fixed position $r_{0,i}$ and world-space position $r_{i}(t)$, is:

$$
\begin{split}
\dot{r}_i (t) &= \omega^* R(t) r_{0,i} + v(t) \\
&=\omega(t) \times (r_i(t) - x(t)) +v(t)
\end{split}
$$

The total torque about the center of mass, due to external force $F_{i}(t)$ applied at points $r_{i}(t)$, is defined as:

$$
\tau(t) = \sum \tau_i(t) = \sum (r_i(t) - x(t)) \times F_i(t)
$$

The total linear momentum is:

$$
\begin{split}
P(t) &= \sum m_i \dot{r}_i(t) \\
&= \sum m_i v(t) + \omega(t) \times \sum m_i (r_i(t) - x(t)) \\
& = \sum m_i v(t) \\
&= M v(t)
\end{split}
$$

The total angular momentum about the center of mass is:

$$
\begin{split}
L(t) &= \sum r'_i \times (m_i \dot{r}_i(t)) \\
&= \sum m_i r'_i \times (\omega(t) \times r'_i) + v(t) \times \sum m_i r'_i \\
&= I(t) \omega
\end{split}
$$

where $r'_i = r_i(t) - x(t)$ and $I(t)$ is the world-space inertia tensor:

$$
\begin{split}
I(t) &= \sum m_i (({r'_i}^T r'_i) \mathbf{I} - r'_i {r'_i}^T) \\
&= \sum m_i ((r_{0,i}^T r_{0,i}) \mathbf{I} - R(t) r_{0,i} r_{0,i}^T R(t)^T) \\
&= R(t) \left(\sum m_i ((r_{0,i}^T r_{0,i}) \mathbf{I} - r_{0,i} r_{0,i}^T)\right) R(t)^T \\
&= R(t) I_{\text{body}} R(t)^T
\end{split}
$$

Here $I_\text{body}$ is the constant inertia tensor defined in the body-fixed frame.

Finally, we have the state-space model:

$$
\frac{d}{dt} \mathbf{Y}(t) = \frac{d}{dt} \begin{pmatrix} x(t) \\ R(t) \\ P(t) \\ L(t) \end{pmatrix}
= \begin{pmatrix} v(t) \\ \omega(t)^*R(t) \\ F(t) \\ \tau(t) \end{pmatrix}
$$

## Non-penetration Constraints

**Collision Detection**: This discussion focuses on convex polyhedra. For any pair of disjoint or contacting (but not interpenetrating) convex polyhedra, a separating plane exists. This plane either: (a) contains a face of one polyhedron, or (b) contains an edge from one polyhedron and is parallel to an edge of the other. (In case (b), the separating plane’s normal is the cross product of the two edge direction vectors, and the plane contains one of these edges.) The face or edges involved in defining such a separating plane are termed the defining features.

**Bounding Boxes** can be used to improve efficiency: If the bounding boxes of two objects do not overlap, no further collision checks between these objects are necessary. A naive pairwise comparison of $n$ objects requires $O(n^2)$ time. However, computational geometry algorithms (e.g., sweep and prune) can reduce this to $O(n\log^{d-2} n + k)$ for $d$-dimensional bounding boxes, where $k$ is the number of overlapping pairs.

An impulse $J$ is applied to change velocities instantaneously upon collision:

$$
J = j\hat{n}(t_0)
$$

The magnitude of the impulse, $j$, can be solved as:

$$
j = \frac{-(1 + \epsilon) v^-_\text{rel}}{\frac{1}{M_a} + \frac{1}{M_b} + \hat{n}(t_0) \cdot \left(I^{-1}_a(t_0) (r_a \times \hat{n}(t_0)\right) \times r_a + \hat{n}(t_0) \cdot \left(I^{-1}_b(t_0) (r_b \times \hat{n}(t_0)\right) \times r_b}
$$

where $\epsilon$ is the *coefficient of restitution*, $v^-_\text{rel}$ is the pre-collision relative normal velocity at the contact point, $\hat{n}(t_{0})$ is the contact normal, and $r_{a}, r_{b}$ are vectors from the respective centers of mass to the contact point.

**Resting Contact** occurs when bodies are in contact but are neither approaching nor separating at the contact point. Three conditions must be met for resting contact forces:

1. The contact forces must prevent inter-penetration
2. The contact forces must be repulsive
3. The contact forces must become zero if separating

Let $d_{i}(t)$ be the separation distance along the normal and $f_{i}$ be the contact force for contact $i$. The conditions can be represented as:

$$
\begin{equation}
\label{NC}
\begin{aligned}
\ddot{d}_i(t_0) &\ge 0 && \text{(Non-penetration)}\\
f_{i} &\ge 0 && \text{(Repulsive)}\\
f_{i} \ddot{d}_i(t_0) &= 0 && \text{(Complementarity)}
\end{aligned}
\end{equation}
$$

The normal accelerations $\ddot{d}_i(t_0)$ can be expressed as a linear function of all the unknown $f$:

$$
\begin{pmatrix}
\ddot{d}_1\left(t_0\right) \\
\vdots \\
\ddot{d}_n\left(t_0\right)
\end{pmatrix}
=\mathbf{A}\begin{pmatrix}
f_1 \\
\vdots \\
f_n
\end{pmatrix}
+\begin{pmatrix}
b_i \\
\vdots \\
b_n
\end{pmatrix}
$$

Then $\eqref{NC}$ forms a quadratic programming problem.

