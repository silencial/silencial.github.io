---
title: Robotic Manipulation
date: 2020-12-30
updated: 2026-05-30
categories:
- Study
tags:
- JHU
- Robotics
---

EN530.646 Review. Based on *[A Mathematical Introduction to Robotic Manipulation](https://www.cse.lehigh.edu/~trink/Courses/RoboticsII/reading/murray-li-sastry-94-complete.pdf)*.

<!--more-->

---

# Math Preliminaries

## Vector Space

A vector space over a field $F$ is a set $V$ equipped with two operations $(+, \cdot)$ defined as:

1. Vector addition ($+$): $V\times V \to V$
2. Scalar multiplication ($\cdot$): $F\times V \to V$

These operations must satisfy the following eight axioms, $\forall x, y, z \in V$ and $\forall \alpha, \beta \in F$

1. Commutativity of addition: $x+y=y+x$
2. Associativity of addition: $(x+y)+z = x+(y+z)$
3. Additive identity: $\exists 0\in V$, s.t. $x+0=x$
4. Additive inverse: $\forall x \in V, \exists {-x} \in V$, s.t. $x + (-x) = 0$
5. Distributivity w.r.t. vector addition: $\alpha \cdot(x+y) = \alpha\cdot x + \alpha\cdot y$
6. Distributivity w.r.t. field addition: $(\alpha+\beta)\cdot x = \alpha\cdot x + \beta\cdot x$
7. Compatibility of scalar multiplication: $(\alpha\beta)\cdot x = \alpha\cdot(\beta\cdot x)$
8. Scalar identity: $1\cdot x = x$, where $1$ is the multiplicative identity in $F$

# Rigid Body Motion

## Rotational Motion

Let $A$ be the inertial frame, $B$ the body frame, and $\mathbf{x}_{ab}, \mathbf{y}_{ab},\mathbf{z}_{ab} \in \mathbb{R}^3$ the coordinates of the principal axes of $B$ relative to $A$. The $3\times 3$ **rotation matrix** is defined as:

$$
R_{ab} = \begin{bmatrix}
\mathbf{x}_{ab} & \mathbf{y}_{ab} & \mathbf{z}_{ab}
\end{bmatrix}
$$

Properties of $R$:

- Orthogonality: $RR^T=R^TR=I$
- Determinant: $\det R = \pm 1$. (For a right-handed coordinate frame, $\det R = 1$)

If $q_a, q_b$ are the coordinates of a point $q$ relative to frames $A$ and $B$, their relationship is given by:

$$
q_a = R_{ab}q_b
$$

### Special Orthogonal Group $SO(n)$

$$
SO(n) = \{R\in\mathbb{R}^{n\times n} : RR^T=I, \det R = 1 \}
$$

$SO(n)$ forms a **group** under matrix multiplication. A set $G$ with a binary operation $\circ$ is a group if it satisfies four axioms:

1. **Closure**: If $g_1, g_2\in G$, then $g_1 \circ g_2 \in G$
2. **Identity**: $\exists e \in G$, s.t. $g\circ e=e\circ g = g$ for all $g\in G$
3. **Inverse**: $\forall g\in G$, there exists only one inverse $g^{-1}\in G$, s.t. $g\circ g^{-1} = g^{-1} \circ g = e$
4. **Associativity**: $\forall g_1, g_2, g_3 \in G, (g_1\circ g_2)\circ g_3 = g_1\circ(g_2\circ g_3)$

The associated Lie algebra $so(n)$ is defined as the space of skew-symmetric matrices:

$$
so(n)=\{S\in\mathbb{R}^{n\times n}: S^T=-S \}
$$

### Exponential Coordinates

Let $\omega \in \mathbb{R}^3$ be a unit vector specifying the axis of rotation, and $\theta \in \mathbb{R}$ be the angle of rotation. The rotation matrix can be represented via the exponential map:

$$
R(\omega, \theta) = e^{\widehat{\omega} \theta}
$$

where the wedge operator $\wedge$ maps $\mathbb{R}^3 \to so(3)$, defined such that $\widehat{\omega}b = \omega \times b$:

$$
\widehat{\omega} = \begin{bmatrix}
0 & -\omega_3 & \omega_2 \\
\omega_3 & 0 & -\omega_1 \\
-\omega_2 & \omega_1 & 0
\end{bmatrix}
\in so(3)
$$

For $\|\omega\| = 1$, Rodrigues' formula gives:

$$
e^{\widehat{\omega}\theta} = I + \widehat{\omega}\sin\theta + \widehat{\omega}^2 (1-\cos\theta)
$$

> Note: The exponential map from $so(3)$ to $SO(3)$ is **surjective**

**Important adjoint property**:

$$
R\widehat{\omega}R^T = (R\omega)^\wedge
$$

### Euler Angles

Basic rotations about principal axes:

$$
\begin{gather*}
R_\mathbf{x}(\alpha)=e^{\widehat{\mathbf{x}}\alpha} = \begin{bmatrix}
1 & 0 & 0 \\
0 & \cos\alpha & -\sin\alpha \\
0 & \sin\alpha & \cos\alpha \\
\end{bmatrix} \\
R_\mathbf{y}(\beta)=e^{\widehat{\mathbf{y}}\beta} = \begin{bmatrix}
\cos\beta & 0 & \sin\beta \\
0 & 1 & 0 \\
-\sin\beta & 0 & \cos\beta
\end{bmatrix} \\
R_\mathbf{z}(\gamma)=e^{\widehat{\mathbf{z}}\gamma} = \begin{bmatrix}
\cos\gamma & -\sin\gamma & 0 \\
\sin\gamma & \cos\gamma & 0\\
0 & 0 & 1
\end{bmatrix}
\end{gather*}
$$

**ZYZ Euler angles**: A common intrinsic rotation sequence. Start with frame $B$ coincident with $A$. Rotate $B$ about its own $z$-axis by $\alpha$, then about its new $y$-axis by $\beta$, and then finally about its new $z$-axis by $\gamma$. The rotation of $B$ relative to A is

$$
R_{ab} = R_\mathbf{z}(\alpha)R_\mathbf{y}(\beta)R_\mathbf{z}(\gamma)
$$

### Quaternions

A quaternion $Q$ is represented as a scalar and a vector part:

$$
Q = q_0+q_1 \mathbf{i} + q_2\mathbf{j} + q_3\mathbf{k} = (q_0, \vec{q}) \qquad \text{where } q_i\in\mathbb{R}
$$

Fundamental properties of the basis:

$$
\begin{gather*}
\mathbf{i}^{2} = \mathbf{j}^{2} = \mathbf{k}^{2} = \mathbf{i\, j\, k} = -1 \\
\mathbf{i\, j}=-\mathbf{j\, i}=\mathbf{k}, \quad \mathbf{j\, k}=-\mathbf{k\, j}=\mathbf{i}, \quad \mathbf{k\, i}=-\mathbf{i\, k}=\mathbf{j}
\end{gather*}
$$

Operations:

1. Conjugate: $Q^*=(q_0, -\vec{q})$
2. Norm squared: $\|Q\|^2=Q\cdot Q^*=q_0^2 + q_1^2 + q_2^2 + q_3^2$
3. Multiplication: $Q\cdot P=(q_0 p_0 - \vec{q}\cdot\vec{p},\; q_0 \vec{p} + p_0\vec{q}+\vec{q}\times\vec{p})$

Rotation via unit quaternion: Given $R=e^{\widehat{\omega}\theta}$, the corresponding unit quaternion is:

$$
Q = \left(\cos\frac{\theta}{2},\; \omega\sin\frac{\theta}{2}\right)
$$

To rotate a point $x \in \mathbb{R}^{3}$, construct a pure quaternion $X=(0, \vec{x})$. The rotated point is the vector part of:

$$
QXQ^*
$$

## Rigid Motion

Rigid motions consist of a rotation $R_{ab}$ and a translation $p_{ab}$, forming an **affine transformation**:

$$
q_a = R_{ab}q_b + p_{ab}
$$

Using **homogeneous coordinates**, we can represent it in linear form as

$$
\bar{q}_a=\begin{bmatrix}
q_a \\ 1
\end{bmatrix}=\begin{bmatrix}
R_{ab} & p_{ab} \\
0 & 1
\end{bmatrix}\begin{bmatrix}
q_b \\ 1
\end{bmatrix}
= \bar{g}_{ab}\bar{q}_b
$$

### Special Euclidean Group $SE(3)$

$$
\begin{gather*}
SE(3)=\{(p, R): p\in\mathbb{R}^3,\ R\in SO(3)\} \\
se(3)=\{(v, \widehat{\omega}): v\in\mathbb{R}^3,\ \widehat{\omega} \in so(3) \}
\end{gather*}
$$

### Exponential Coordinates for $SE(3)$

Similar to $SO(3)$, the exponential map generalizes to $SE(3)$:

$$
\bar{g}=e^{\widehat{\xi} \theta}
$$

where $\widehat{\xi} \in se(3)$ is defined by an axis of rotation $\omega$ and a point $q$ on the axis:

$$
\widehat{\xi} = \begin{bmatrix}
\widehat{\omega} & -\omega\times q \\
0 & 0
\end{bmatrix}
$$

The vector $\xi:=(v, \omega) \in \mathbb{R}^{6}$ represents the **twist coordinates** of $\widehat{\xi}$:

$$
\xi^\wedge=\begin{bmatrix}
v \\ \omega
\end{bmatrix}^\wedge = \begin{bmatrix}
\widehat{\omega} & v \\
0 & 0
\end{bmatrix}
$$

When $\|\omega\| = 1$, the exponential map yields:

$$
\begin{equation}
\label{gexp}
e^{\widehat{\xi} \theta} = \begin{bmatrix}
e^{\widehat{\omega} \theta} & (I - e^{\widehat{\omega}\theta}) (\omega \times v) + \omega\omega^Tv\theta \\
0 & 1
\end{bmatrix}
\end{equation}
$$

> Note: The exponential map from $se(3)$ to $SE(3)$ is **surjective**.

This formulation elegantly represents the **relative** motion of a rigid body:

$$
p(\theta) = e^{\widehat{\xi}\theta} p(0), \qquad g_{ab}(\theta) = e^{\widehat{\xi}\theta}g_{ab}(0)
$$

### Screws

A screw motion is a rotation about an axis by angle $\theta = M$, followed by a translation along the **same** axis by distance $d=h\theta$. It is defined by three parameters

- Pitch: $h=d/\theta$. (If $h=\infty$, it represents pure translation by $M$)
- Axis: $l=\{q+\lambda \omega : \lambda \in \mathbb{R}\}$, where $q$ is a point on the axis and $\omega$ is the direction vector
- Magnitude: $M$, the amount of rotation (or translation if $h=\infty$)

In homogeneous coordinates:

$$
g = \begin{bmatrix}
e^{\widehat{\omega} \theta} & \left(I-e^{\widehat{\omega} \theta}\right) q+h \theta \omega \\
0 & 1
\end{bmatrix}
$$

Comparing this with $\eqref{gexp}$, setting $v=-\omega\times q + h\omega$ means the twist $\xi=(v,\omega)$ generates the identical screw motion.

**Twist to screw**: Given $\xi = (v, \omega)$, the screw coordinates are

- Pitch: $\displaystyle h=\frac{\omega^{T} v}{\|\omega\|^{2}}$
- Axis: $l=\begin{dcases}\left\{ \frac{\omega \times v}{\|\omega\|^{2}}+\lambda \omega:\lambda \in \mathbb{R} \right\}, & \text {if } \omega \ne 0 \\ \left\{ 0+\lambda v: \lambda \in \mathbb{R} \right\}, & \text {if } \omega=0 \end{dcases}$
- Magnitude: $M=\begin{dcases}\|\omega\|, & \text {if } \omega \ne 0 \\ \|v\|, & \text {if } \omega=0\end{dcases}$

**Screw to twist**: Given a screw $(h, l, M)$, the twist is

- If $h=\infty$: Let $l=\{q + \lambda v: \|v\| = 1\}$, $\theta = M$. Then $\widehat{\xi} = \begin{bmatrix}0 & v \\ 0 & 0\end{bmatrix}$
- If $h\ne\infty$: Let $l=\{q + \lambda \omega: \|\omega\| = 1\}$, $\theta=M$. Then $\widehat{\xi} = \begin{bmatrix}\widehat{\omega} & -\omega\times q + h\omega \\ 0 & 0\end{bmatrix}$

## Velocity of a Rigid Body

### Rotational Velocity

Differentiating $q_a(t)=R_{ab}(t) q_b$ gives the velocity of the point in spatial coordinates:

$$
v_{q_{a}}(t)=\dot{R}_{a b}(t) q_{b} = \dot{R}_{a b}(t) R_{a b}^{-1}(t) R_{a b}(t) q_{b}
$$

We define two critical angular velocities:

- Spatial angular velocity: $\widehat{\omega}_{ab}^s = \dot{R}_{ab} R_{ab}^{-1}$
- Body angular velocity: $\widehat{\omega}_{ab}^b = R_{ab}^{-1} \dot{R}_{ab}$

Resulting point velocities:

$$
\begin{gather*}
v_{q_{a}}(t)=\widehat{\omega}_{a b}^{s} R_{a b}(t) q_{b}=\omega_{a b}^{s}(t) \times q_{a}(t) \\
v_{q_b}(t) = R_{ab}^T(t) v_{q_a}(t) = \omega_{ab}^b(t) \times q_b
\end{gather*}
$$

### Rigid Body Velocity

Similarly, differentiating $q_a(t) = g_{ab}(t) q_b$:

$$
v_{q_a} = \frac{d}{dt}q_a(t) = \dot{g}_{ab} q_b = \dot{g}_{ab} g_{ab}^{-1} q_a
$$

Define the spatial velocity $\widehat{V}_{ab}^s \in se(3)$ and body velocity $\widehat{V}_{ab}^b \in se(3)$

$$
\begin{gather*}
\widehat{V}_{a b}^{s}=\dot{g}_{a b} g_{a b}^{-1}, \quad V_{a b}^{s} =\begin{bmatrix}
v_{a b}^{s} \\
\omega_{a b}^{s}
\end{bmatrix}=\begin{bmatrix}
-\dot{R}_{a b} R_{a b}^{T} p_{a b}+\dot{p}_{a b} \\
\left(\dot{R}_{a b} R_{a b}^{T}\right)^{\vee}
\end{bmatrix} \\
\widehat{V}_{a b}^{b}=g_{a b}^{-1}\dot{g}_{a b}, \quad V_{a b}^{b} =\begin{bmatrix}
v_{a b}^{b} \\
\omega_{a b}^{b}
\end{bmatrix}=\begin{bmatrix}
R_{a b}^{T} \dot{p}_{a b} \\
\left(R_{a b}^{T} \dot{R}_{a b}\right)^{\vee}
\end{bmatrix} \\
\end{gather*}
$$

And the velocities become

$$
\begin{gather*}
v_{q_{a}}=\widehat{V}_{a b}^{s} q_{a}=\omega_{a b}^{s} \times q_{a}+v_{a b}^{s} \\
v_{q_b} = g_{ab}^{-1} v_{q_a} = \widehat{V}_{ab}^b q_b = \omega_{a b}^{b} \times q_{b}+v_{a b}^{b}
\end{gather*}
$$

Physical interpretation:

- $\omega_{ab}^s$: Angular velocity of the body viewed in the spatial frame.
- $v_{ab}^s$: Velocity of a point on the body currently traveling through the **origin** of the spatial frame.
- $\omega_{ab}^b$: Angular velocity viewed in the current body frame.
- $v_{ab}^b$: Velocity of the origin of the body frame relative to the spatial frame, viewed in the current body frame.

The spatial and body velocities are related by

$$
\begin{gather*}
\omega_{a b}^{s}=R_{a b} \omega_{a b}^{b} \\
v_{a b}^{s}=-\omega_{a b}^{s} \times p_{a b}+\dot{p}_{a b}=p_{a b} \times\left(R_{a b} \omega_{a b}^{b}\right)+R_{a b} v_{a b}^{b}
\end{gather*}
$$

Define the **adjoint transformation** associated with $g$ as

$$
\mathrm{Ad}_g = \begin{bmatrix}
R & \widehat{p} R \\
0 & R
\end{bmatrix}
$$

Then we have $V_{ab}^s = \mathrm{Ad}_g V_{ab}^b$

### Velocity of a Screw

If the motion is generated by a twist $\xi$, the velocities are derived elegantly:

$$
\begin{split}
\widehat{V}_{ab}^s &= \dot{g}_{ab}(\theta) g_{ab}^{-1}(\theta) \\
&= \frac{d}{dt}\left(e^{\widehat{\xi}\theta} g_{ab}(0)\right) \left(g_{ab}^{-1}(0) e^{-\widehat{\xi}\theta}\right) \\
&= \widehat{\xi}\dot{\theta}
\end{split}
$$

$$
\begin{split}
\widehat{V}_{a b}^{b} &=g_{a b}^{-1}(\theta) \dot{g}_{a b}(\theta) \\
&=\left(g_{a b}^{-1}(0) e^{-\widehat{\xi} \theta}\right)\left(e^{\widehat{\xi} \theta} \widehat{\xi} \dot{\theta} g_{a b}(0)\right) \\
&=\left(g_{a b}^{-1}(0) \widehat{\xi} g_{a b}(0)\right) \dot{\theta} \\
&=\left(\mathrm{Ad}_{g_{a b}^{-1}(0)} \xi\right)^{\wedge} \dot{\theta}
\end{split}
$$

> Note: $\frac{d}{dt}e^{A(t)} = \dot{A}(t) e^{A(t)} = e^{A(t)}\dot{A}(t)$ holds if and only if $A$ and $\dot{A}$ commute

### Coordinate Transformations

Transformations across multiple frames follow specific rules:

- Spatial velocity: $V_{a c}^{s}=V_{a b}^{s}+\mathrm{Ad}_{g_{a b}} V_{b c}^{s}$
- Body velocity: $V_{a c}^{b}=\mathrm{Ad}_{g_{b c}^{-1}} V_{a b}^{b}+V_{b c}^{b}$

Key inverse properties:

$$
\begin{gather*}
V_{a b}^{b}=-V_{b a}^{s} \\
V_{a b}^{b}=-\mathrm{Ad}_{g_{b a}} V_{b a}^{b}
\end{gather*}
$$

## Wrenches

A wrench is a generalized force, consisting of a linear force $f$ and a moment/torque $\tau$.

$$
F = \begin{bmatrix}
f \\ \tau
\end{bmatrix} \in \mathbb{R}^6
$$

Let $B$ be a coordinate frame attached to a rigid body. A wrench applied at the origin of $B$ is $F_b = (f_b, \tau_b)^{T}$. The infinitesimal **work** done is the dot product of the body velocity and the body wrench:

$$
\delta W = V_{ab}^b \cdot F_b
$$

If observed from another frame $C$ stationary relative to $B$, the physical work must be invariant:

$$
V_{ac}^b \cdot F_c = V_{ab}^b \cdot F_b = (\mathrm{Ad}_{g_{bc}} V_{ac}^b)^T F_b = V_{ac}^b \cdot \mathrm{Ad}_{g_{bc}}^T F_b
$$

This yields the wrench transformation law:

$$
\begin{gather*}
F_c = \mathrm{Ad}_{g_{bc}}^T F_b \\
\begin{bmatrix}
f_c \\ \tau_c
\end{bmatrix} = \begin{bmatrix}
R_{bc}^T & 0 \\ -R_{bc}^T \widehat{p}_{bc} & R_{bc}^T
\end{bmatrix} \begin{bmatrix}
f_b \\ \tau_b
\end{bmatrix}
\end{gather*}
$$

# Manipulator Kinematics

## Forward Kinematics

Consider an open-chain manipulator with a base frame $S$ and a tool frame $T$, connected by a series of revolute or prismatic joints. The **joint (configuration) space** $Q$ of a manipulator consists of all possible values of the joint variables the robot.

The forward kinematics map $g_{st}: Q \to SE(3)$ describes the pose of the end-effector and can be expressed using the product of exponentials formula:

$$
\begin{equation}
\label{fk}
g_{s t}(\theta)=e^{\widehat{\xi}_{1} \theta_{1}} e^{\widehat{\xi}_{2} \theta_{2}} \cdots e^{\widehat{\xi}_{n} \theta_{n}} g_{s t}(0)
\end{equation}
$$

> Note: $\xi_i$ denotes the twist of the $i$-th joint evaluated at the zero configuration, and they must be numbered sequentially from the base to the tool.

## Manipulator Jacobian

Since $g_{st}: \mathbb{R}^n \to SE(3)$ is a matrix-valued function, classical Jacobian concepts are adapted using twist representations.

### End-Effector Velocity

Differentiating the forward kinematics yields the spatial velocity of the end-effector $\widehat{V}_{st}^s$:

$$
\begin{split}
\widehat{V}_{st}^s &= \dot{g}_{st}(\theta) g_{st}^{-1}(\theta) \\
&= \sum_{i=1}^n \left( \frac{\partial g_{st}}{\partial \theta_i} \dot{\theta}_i \right) g_{st}^{-1}(\theta) \\
&= \sum_{i=1}^n \left( \frac{\partial g_{st}}{\partial \theta_i} g_{st}^{-1}(\theta) \right) \dot{\theta}_i
\end{split}
$$

In twist coordinates, it can be written as

$$
V_{st}^s = J_{st}^s(\theta) \dot{\theta}
$$

where $J_{st}^s(\theta) \in \mathbb{R}^{6\times n}$ is the **spatial manipulator Jacobian**:

$$
J_{st}^s(\theta) = \left[ \left(\frac{\partial g_{st}}{\partial \theta_1} g_{st}^{-1} \right)^\vee \dots \left(\frac{\partial g_{st}}{\partial \theta_n} g_{st}^{-1} \right)^\vee \right]
$$

By expanding the partial derivative $\frac{\partial g_{st}}{\partial \theta_i}$ from $\eqref{fk}$:

$$
\begin{split}
\left(\frac{\partial g_{s t}}{\partial \theta_{i}}\right) g_{s t}^{-1} &=e^{\widehat{\xi}_{1} \theta_{1}} \cdots e^{\widehat{\xi}_{i-1} \theta_{i-1}} \frac{\partial}{\partial \theta_{i}}\left(e^{\widehat{\xi}_{i} \theta_{i}}\right) e^{\widehat{\xi}_{i+1} \theta_{i+1}} \cdots e^{\widehat{\xi}_{n} \theta_{n}} g_{s t}(0) g_{s t}^{-1} \\
&=e^{\widehat{\xi}_{1} \theta_{1}} \cdots e^{\widehat{\xi}_{i-1} \theta_{i-1}}\left(\widehat{\xi}_{i}\right) e^{\widehat{\xi}_{i} \theta_{i}} \cdots e^{\widehat{\xi}_{n} \theta_{n}} g_{s t}(0) g_{s t}^{-1} \\
&=e^{\widehat{\xi}_{1} \theta_{1}} \cdots e^{\widehat{\xi}_{i-1} \theta_{i-1}}\left(\widehat{\xi}_{i}\right) e^{-\widehat{\xi}_{i-1} \theta_{i-1}} \cdots e^{-\widehat{\xi}_{1} \theta_{1}}
\end{split}
$$

Applying the adjoint map property, the $i$-th column simplifies to

$$
\xi_{i}' = \left(\frac{\partial g_{s t}}{\partial \theta_{i}} g_{s t}^{-1}\right)^{\vee}=\mathrm{Ad}_{(e^{\widehat{\xi}_{1} \theta_{1}} \cdots e^{\widehat{\xi}_{i-1} \theta_{i-1}})} {\xi_{i}}
$$

The spatial manipulator Jacobian becomes

$$
J_{st}^s(\theta) = \begin{bmatrix}
\xi_1 & \xi_2' & \cdots & \xi_n'
\end{bmatrix}
$$

> Interpretation: The $i$-th column of the spatial Jacobian is simply the $i$-th joint twist, transformed by the rigid motion of all preceding joints to the current configuration.

The **body manipulator Jacobian** can be defined similarly:

$$
\begin{gather*}
J_{st}^b(\theta) = \begin{bmatrix}
\xi_1^\dagger & \xi_{2}^{\dagger} & \cdots & \xi_n^\dagger
\end{bmatrix} \\
\xi_i^\dagger = \mathrm{Ad}^{-1}_{(e^{\widehat{\xi}_{i} \theta_{i}} \cdots e^{\widehat{\xi}_{n} \theta_{n}} g_{st}(0))} {\xi_{i}}
\end{gather*}
$$

The columns of $J_{st}^b$ correspond to the joint twists written w.r.t. the tool frame at the current configuration.

The spatial and body Jacobians are fundamentally linked by the adjoint transformation of the current pose:

$$
J_{st}^s(\theta) = \mathrm{Ad}_{g_{st}(\theta)} J_{st}^b(\theta)
$$

When away from singularities (invertible Jacobian), the inverse kinematics rate equation is

$$
\dot{\theta}(t) = [J_{st}^s(\theta)]^{-1} V_{st}^s(t)
$$

### Singularities

A singular configuration is a state where the manipulator Jacobian $J(\theta)$ drops rank, losing one or more degrees of freedom.

There are 4 common geometric singularity cases for revolute joints:

1. **Two collinear revolute joints**: Exists two joints whose axes align perfectly. The twists are $\xi_1 = (-\omega_1 \times q_1, \omega_1)^{T}$ and $\xi_2 = (-\omega_2 \times q_2, \omega_2)^{T}$.
    - Axes are parallel: $\omega_1 = \pm \omega_2$
    - Axes are collinear: $\omega_i \times(q_1 - q_2) = 0$
2. **Three parallel coplanar revolute joint axes**:
    - Axes are parallel: $\omega_i = \pm \omega_j$ for $i,j\in \{1,2,3\}$
    - Axes are coplanar: $\exists n \in \mathbb{R}^{3}$ s.t. $n^T \omega_i = 0$ and $n^T(q_i - q_j) = 0$ for $i,j\in \{1,2,3\}$
3. **Four intersecting revolute joint axes**: All four axes intersect at a single spatial point $q$.
    - $\exists q \in \mathbb{R}^{3}$ s.t. $\omega_i \times(q_i - q) = 0$ for $i\in \{1,2,3,4\}$
4. **Four parallel joint axes**: Axes are purely parallel: $\omega_i = \pm\omega_j$ for $i\in \{1,2,3,4\}$

### Manipulability

The manipulability of a robot describes its ability to move freely in all directions in the workspace.

1. The ability to **reach** a certain position or set of positions
2. The ability to **change** the position or orientation at a given configuration

Common manipulability measures:

- Minimum singular value: $\sigma_\min(J)$
- Inverse condition number: $\sigma_\min(J) / \sigma_\max(J)$
- Determinant: $\det J$
