---
title: Particle and Rigid Body Dynamics
date: 2018-03-20
categories:
- Study
tags:
- CG
- Paper Reading
---

The [course notes](http://www.cs.cmu.edu/~baraff/sigcourse/) give detailed code implementation and mathematical derivation.

<!--more-->

---

# Newton Method

$$
\begin{gather*}
f(x)\approx f(x_n)+f'(x_n)\Delta x+\frac{1}{2}f''(x_n)\Delta x^2 \\
0=\frac{d}{d\Delta x}\left(f(x_n)+f'(x_n)\Delta x+\frac{1}{2}f''(x_n)\Delta x^2\right)=f'(x_{n})+f''(x_{n})\Delta x
\end{gather*}
$$

so

$$
x_{n+1}=x_n-H^{-1}\nabla f(x_n)
$$

where $H=\nabla^2f$ is the Hessian matrix

# Euler Method

$$
\mathbf{x}(t_0 + h) = \mathbf{x}_0+h \dot{\mathbf{x}}(t_0)
$$

Euler Method is the simplest numerical method and achieve $O(h^2)$ accuracy. But it has many disadvantages. It will produce outward drift when evaluating an orbiting movement so it is not accurate. It is also unstable when the stepsize is large.

Euler Method could be improve by leaving more terms in the Taylor series.

# Improving Euler Method

## Midpoint Method

We want to achieve the $O(h^3)$ accuracy. First expand the Taylor series:

$$
\begin{equation}\label{MM}
\mathbf{x}(t_0 + h) = \mathbf{x}(t_0)  + h\dot{\mathbf{x}} +\dfrac{h^2}{2} \ddot{\mathbf{x}}(t_0) + O(h^3)
\end{equation}
$$

Note that $\mathbf{\dot{x}}$ is given by a function $f(\mathbf{x}(t), t)$. For simplicity we assume $f$ depends on time only indirectly through $\mathbf{x}$. So that

$$
\ddot{\mathbf{x}} = \dfrac{\partial f}{\partial  \mathbf{x}} \dot{\mathbf{x}} =f'f
$$

To avoid evaluating $f'$, we perform another Taylor expansion

$$
f(\mathbf{x}_0 + \Delta\mathbf{x}) = f(\mathbf{x}_0) + \Delta\mathbf{x} f'(\mathbf{x}_0) + O(\Delta \mathbf{x}^2 )
$$

We introduce $\ddot{\mathbf{x}}$ by choosing $\Delta\mathbf{x} = \dfrac{h}{2} f( \mathbf{x}_0 )$ and multiply both sides by h, yielding

$$
\dfrac{h^2}{2} \ddot{\mathbf{x}} + O(h^3) = h \left(  f \big( \mathbf{x}_0 + \dfrac{h}{2} f(\mathbf{x}_0) \big) - f(\mathbf{x}_0)\right)
$$

Substituting the right hand side into $\eqref{MM}$ gives the update formula

$$
\mathbf{x}(t_0 + h) = \mathbf{x}(t_0) + h\left(f\big( \mathbf{x}_0 + \dfrac{h}{2}f(\mathbf{x}_0) \big) \right)
$$

We can also evaluate $f$ a few more times to get more accurate methods like [Runge-Kutta Method](https://en.wikipedia.org/wiki/Runge%E2%80%93Kutta_methods).

## Adaptive Stepsize

A major problem of numerical methods lies in determining a good step size. We want to choose $h$ as large as possible while remain stable, so we vary $h$ over the course of solving the ODE.

Here we present the adaptive stepsizing for Euler's method. We compute two estimates for $\mathbf{x}(t_0 + h)$. One is $\mathbf{x}_a$ by taking a step size of $h$, the other is $\mathbf{x}_b$ by taking two step size of $\dfrac{h}{2}$. We can write the current error as $e = \|\mathbf{x}_a - \mathbf{x}_b\|$ and define the tolerance error as $tol$. We can change the stepsize each step as follows

$$
\left( \dfrac{e}{\text{tol}} \right) ^ \frac{1}{2} h
$$

# Particle System Dynamics

The position/velocity product space is called the [phase space](https://en.wikipedia.org/wiki/Phase_space) and the whole system may be regarded as a point moving through $6n$-space.

Forces can be grouped into three broad categories:

- Unary forces, such as gravity and drag, that act independently on each particles.
- $n$-ary forces, such as springs, that apply forces to a fixed set of particles.
- Forces of spatial interaction, such as attraction and repulsion, that my act on any or all pairs of particles.

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

The use of extra energy terms to impose constrains is known as the **penalty method**. Its difficulty is that applied forces and restoring forces communicate only indirectly, through displacements. This make it impossible to achieve accuracy without stiffness.

$$
\ddot{\mathbf{q}}  = \mathbf{WQ}
$$

where $\mathbf{q}$ is the position, $\mathbf{W}$ is the inverse matrix of $\mathbf{M}$, $\mathbf{Q}$ is the force.

We first set constrains to $\mathbf{C}$ and assume $\mathbf{q}$ and $\dot{\mathbf{q}}$ are both initially legal, i.e. that $\mathbf{C} = \dot{\mathbf{C}} = 0$. Then our problem is to solve for a constraint force $\hat{\mathbf{Q}}$ that, added to the applied force $\mathbf{Q}$, guarantees $\ddot{\mathbf{C}} = 0$

By chain rule:

$$
\ddot{\mathbf{C}}  = \dot{\mathbf{J}} \dot{\mathbf{q}} + \mathbf{J} \ddot{\mathbf{q}}
$$

where $\mathbf{J} = \frac{\partial \mathbf{C}}{\partial \mathbf{q}}$

replace $\ddot{\mathbf{q}}$ by the force expression and set $\ddot{\mathbf{C}} = 0$:

$$
\begin{equation}\label{CD}
\mathbf{JW} \hat{\mathbf{Q}} = -\dot{\mathbf{J}} \dot{\mathbf{q}} - \mathbf{JWQ}
\end{equation}
$$

then we ensure that the constraint force does no work, we therefore require:

$$
\hat{\mathbf{Q}} \cdot \dot{\mathbf{q}} = 0, \quad \forall \dot{\mathbf{q}} \mid \mathbf{J} \dot{\mathbf{q}} = 0
$$

which means $\hat{\mathbf{Q}} = \mathbf{J}^T \lambda$

To better understand what this expression means, $\mathbf{J}$ are normals to the constraint hypersurfaces, $\mathbf{J}^T \lambda$ are the linear combination of these gradient vectors, and hence span exactly the set of prohibited directions. So its dot product with any legal displacement of the system will be zero.

Replace it in $\eqref{CD}$:

$$
\mathbf{JW} \mathbf{J}^T \lambda = -\dot{\mathbf{J}} \dot{\mathbf{q}} - \mathbf{JWQ}
$$

**Lagrangian Dynamics**: if we represent constraints with a parametric function $\mathbf{q}(\mathbf{u})$, then

$$
\mathbf{J} = \frac{\partial \mathbf{q}} {\partial \mathbf{u}}
$$

with the same process we can finally get

$$
\mathbf{J}^T \mathbf{MJ} \ddot{\mathbf{u}} + \mathbf{J}^T \mathbf{M} \dot{\mathbf{J}} \dot{\mathbf{u}} - \mathbf{J}^T \mathbf{Q} = 0
$$

# Rigid Body Simulation

## Unconstrained Rigid Body Dynamics

If $p_0$ is an arbitrary point on the rigid body, the world-space location p(t) of $p_0$ is:

$$
p(t) = R(t) p_0 + x(t)
$$

where $R(t)$ is the rotation matrix and $x(t)$ is the translation

Their derivatives are:

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

If we define $\omega^*$ as

$$
\begin{pmatrix}
0 & -\omega_z & \omega_y \\
\omega_z & 0 & -\omega_x \\
-\omega_y & \omega_x & 0
\end{pmatrix}
$$

we can rewrite $\dot{R}(t)$ as

$$
\dot{R}(t) = \omega(t)^* R(t)
$$

Now we have the velocity

$$
\begin{split}
\dot{r}_i (t) &= \omega^* R(t) {r_0}_i + v(t) \\
&=\omega(t) \times (r_i(t) - x(t)) +v(t)
\end{split}
$$

Torque is defined as

$$
\tau(t) = \sum \tau_i(t) = \sum (r_i(t) - x(t)) \times F_i(t)
$$

Linear Momentum is

$$
\begin{split}
P(t) &= \sum m_i \dot{r}_i(t) \\
&= \sum m_i v(t) + \omega(t) \times \sum m_i (r_i(t) - x(t)) \\
& = \sum m_i v(t) = M v(t)
\end{split}
$$

Angular Momentum is

$$
\begin{split}
L(t) &= \sum m_i \dot{r}_i(t) \times r'_i \\
&= \sum m_i \omega(t) \times  r'_i \times  r'_i + v(t) \times \sum m_i r'_i \\
&= I(t) \omega
\end{split}
$$

where $r'_i = r_i(t) - x(t)$ and $I(t)$ is the Inertial Tensor

$$
\begin{split}
I(t) &= \sum m_i (({r'_i}^T r'_i) \mathbf{I} - r'_i {r'_i}^T) \\
&= \sum m_i (({r_0}_i^T {r_0}_i) \mathbf{I} - R(t) {r_0}_i {r_0}_i^T R(t)^T) \\
&= R(t) \left(\sum m_i (({r_0}_i^T {r_0}_i) \mathbf{I} - {r_0}_i {r_0}_i^T)\right) R(t)^T \\
&= R(t) I_{body} R(t)^T
\end{split}
$$

Since $I_{body}$ is specified in body-space, it is constant over the simulation.

Now we have the state vector and its derivatives:

$$
\frac{d}{dt} \mathbf{Y}(t) = \frac{d}{dt} \begin{pmatrix} x(t) \\ R(t) \\ P(t) \\ L(t) \end{pmatrix}
= \begin{pmatrix} v(t) \\ \omega(t)^*R(t) \\ F(t) \\ \tau(t) \end{pmatrix}
$$

## Nonpenetration Constraints

**Collision Detection**: we only consider Convex Polyhedra. If a pair of convex polyhedra are
disjoint or contacting (but not inter-penetrating), then a separating plane exists with the following
property: either the plane contains a face of one of the polyhedra, or the plane contains an edge from
one of the polyhedra and is parallel to an edge of the other polyhedra. (That is, the separating plane’s
normal is the cross product of the two edge directions, and the plane itself contains one of the edges.)
We will call the face or edges in question the *defining face* or edges.

**Bounding Boxes** are further used to increase the efficiency: if two bounding boxes are found not to overlap, no further comparisons involving the contents of the boxes are needed. A naive pairwise comparison of all pairs requires $O(n^2)$ work, while computational geometry algorithms exist that can solve this problem in time $O(n\log^{d-2} n + k)$ for $d$-dimensional bounding boxes.

We need impulse to change the velocity after collision happens:

$$
J = j\hat{n}(t_0)
$$

by the Conservation of momentum we can solve for $j$

$$
j = \frac{-(1 + \epsilon) v^-_{rel}}{\frac{1}{M_a} + \frac{1}{M_b} + \hat{n}(t_0) \cdot \left(I^{-1}_a(t_0) (r_a \times \hat{n}(t_0)\right) \times r_a + \hat{n}(t_0) \cdot \left(I^{-1}_b(t_0) (r_b \times \hat{n}(t_0)\right) \times r_b}
$$

where $\epsilon$ is the *coefficient of restitution*

**Resting Contact** are cases when bodies are neither colliding nor separating at a contact point. We have three restrictions:

1. The contact forces must prevent inter-penetration
2. The contact forces must be repulsive
3. The contact forces must become zero if the bodies begin to separate

Let $d(t)$ be the separation between the two contact points lies in two objects. The restrictions can be represented as follows:

$$
\begin{equation}\label{NC}
\begin{aligned}
\ddot{d}_i(t_0) &\ge 0 \\
f_i &\ge 0 \\
f_i \ddot{d}_i(t_0) &= 0
\end{aligned}
\end{equation}
$$

By writing $\ddot{d}_i(t_0)$ as a function of the unknown $f_i$

$$
\ddot{d} = \mathbf{A} f + b
$$

we can then solve $\eqref{NC}$ by an algorithm called *quadratic program*
