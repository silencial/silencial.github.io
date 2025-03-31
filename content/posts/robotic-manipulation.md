---
title: Robotic Manipulation
date: 2020-12-30
categories:
- Study
tags:
- JHU
- Robotics
---

EN530.646 Review. Based on *[A Mathematical Introduction to Robotic Manipulation](https://www.cse.lehigh.edu/~trink/Courses/RoboticsII/reading/murray-li-sastry-94-complete.pdf)* book.

<!--more-->

---

# Math Preliminaries

## Vector Space

A vector space over a field $F$ is a set $V$ together with two operations $(+, \cdot)$ defined as

1. Vector addition $+$: $V\times V \to V$
2. Scalar multiplication $\cdot$: $F\times V \to V$

It has to satisfy eight axioms, $\forall x, y, z \in V$ and $\forall \alpha, \beta \in F$

1. $x+y=y+x$
2. $(x+y)+z = x+(y+z)$
3. $\exists 0\in V$, s.t. $x+0=x$
4. $\exists -x \in V$, s.t. $x + (-x) = 0$
5. $\alpha \cdot(x+y) = \alpha\cdot x + \alpha\cdot y$
6. $(\alpha+\beta)\cdot x = \alpha\cdot x + \beta\cdot x$
7. $(\alpha\beta)\cdot x = \alpha\cdot(\beta\cdot x)$
8. $1\cdot x = x$, where $1$ is the multiplicative identity in $F$

# Rigid Body Motion

## Rotational Motion

Let $A$ be the inertial frame, $B$ the body frame, and $\mathbf{x}_{ab}, \mathbf{y}_{ab},\mathbf{z}_{ab} \in \mathbb{R}^3$ the coordinated of the principal axes of $B$ relative to $A$. Define the $3\times 3$ **rotation matrix**
$$
R_{ab} = \begin{bmatrix}
\mathbf{x}_{ab} & \mathbf{y}_{ab} & \mathbf{z}_{ab}
\end{bmatrix}
$$
Properties:

1. $RR^T=R^TR=I$
2. $\det R = \pm 1$. If the coordinate frame is right-handed, then $\det R = 1$

Let $q_a, q_b$ be the coordinates of a point $q$ relative to frames $A$ and $B$, then we have
$$
q_a = R_{ab}q_b
$$

### Special Orthogonal Group

Define $SO(n)$ as
$$
SO(n) = \{R\in\mathbb{R}^{n\times n} : RR^T=I, \det R = 1 \}
$$
$SO(n)$ is a **group** under the operation of matrix multiplication.

A set $G$ with a binary operation $\circ$ defined on elements of $G$ is called a group if it satisfies the following axioms:

1. *Closure*: If $g_1, g_2\in G$, then $g_1 \circ g_2 \in G$
2. *Identity*: There exists an identity element $e$, s.t. $g\circ e=e\circ g = g$ for every $g\in G$
3. *Inverse*: For each $g\in G$, there exists only one inverse $g^{-1}\in G$, s.t. $g\circ g^{-1} = g^{-1} \circ g = e$
4. *Associativity*: If $g_1, g_2, g_3 \in G$, then $(g_1\circ g_2)\circ g_3 = g_1\circ(g_2\circ g_3)$

Defined $so(n)$ as
$$
so(n)=\{S\in\mathbb{R}^{n\times n}: S^T=-S \}
$$

### Exponential Coordinates

Let $\omega \in \mathbb{R}^3$ be a unit vector which specifies the direction of the rotation and $\theta \in \mathbb{R}$ be the angle of rotation. The rotation matrix can be represented as
$$
R(\omega, \theta) = e^{\hat{\omega} \theta}
$$
where $\hat{\omega} \in so(3)$ with the property $\omega \times b = \hat{\omega}b$
$$
\hat{\omega} = \begin{bmatrix}
0 & -\omega_3 & \omega_2 \\
\omega_3 & 0 & -\omega_1 \\
-\omega_2 & \omega_1 & 0
\end{bmatrix}
$$
When $\|\omega\| = 1$
$$
e^{\hat{\omega}\theta} = I + \hat{\omega}\sin\theta + \hat{\omega}^2 (1-\cos\theta)
$$
**The exponential map from $so(3)$ to $SO(3)$ is surjective**

**Important properties:**
$$
R\hat{\omega}R^T = (R\omega)^\wedge \\
$$

### Euler Angles

$$
\begin{gather*}
R_x(\alpha):=e^{\hat{x}\alpha} = \begin{bmatrix}
1 & 0 & 0 \\
0 & \cos\alpha & -\sin\alpha \\
0 & \sin\alpha & \cos\alpha \\
\end{bmatrix} \\
R_y(\beta):=e^{\hat{y}\beta} = \begin{bmatrix}
\cos\beta & 0 & \sin\beta \\
0 & 1 & 0 \\
-\sin\beta & 0 & \cos\beta
\end{bmatrix} \\
R_z(\gamma):=e^{\hat{z}\gamma} = \begin{bmatrix}
\cos\gamma & -\sin\gamma & 0 \\
\sin\gamma & \cos\gamma & 0\\
0 & 0 & 1
\end{bmatrix}
\end{gather*}
$$

$ZYZ$ is a commonly used Euler angles: Start with frame $B$ coincident with frame $A$. First rotate $B$ about the $z$-axis of frame $B$ by an angle $\alpha$, then rotate about the (new) $y$-axis of frame $B$ by an angle $\beta$, and then rotate about the (new) $z$-axis of frame $B$ by an angle of $\gamma$. The rotation of $B$ relative to A is
$$
R_{ab} = R_z(\alpha)R_y(\beta)R_z(\gamma)
$$

### Quaternions

$$
\begin{split}
Q &= q_0+q_1 \mathbf{i} + q_2\mathbf{j} + q_3\mathbf{k} \qquad q_i\in\mathbb{R} \\
&=(q_0, \vec{q})
\end{split}
$$

where
$$
\begin{gather*}
\mathbf{i}\cdot\mathbf{i}=\mathbf{j}\cdot\mathbf{j}=\mathbf{k}\cdot\mathbf{k}=-1 \\
\mathbf{i}\cdot\mathbf{j}=-\mathbf{j}\cdot\mathbf{i}=\mathbf{k} \qquad \mathbf{j}\cdot\mathbf{k}=-\mathbf{k}\cdot\mathbf{j}=\mathbf{i} \qquad \mathbf{k}\cdot\mathbf{i}=-\mathbf{i}\cdot\mathbf{k}=\mathbf{j}
\end{gather*}
$$
Definitions:

1. The *conjugate* of a quaternion: $Q^*=(q_0, -\vec{q})$
2. $\|Q\|^2=Q\cdot Q^*=q_0^2 + q_1^2 + q_2^2 + q_3^2$
3. $Q\cdot P=(q_0 p_0 - \vec{q}\cdot\vec{p}, q_0 \vec{p} + p_0\vec{q}+\vec{q}\times\vec{p})$

Given a rotation matrix $R=e^{\hat{\omega}\theta}$, define the *unit quaternion* as
$$
Q=\big(\cos(\theta / 2), \omega\sin(\theta / 2)\big)
$$
To rotate a point $x$, first let $X=(0, \vec{x})$ be a *pure* quaternion, then
$$
QXQ^*
$$
is also a pure quaternion and the vector part is the rotated $x$

## Rigid Motion

Rigid motions consist of rotation $R_{ab}$ and translation $p_{ab}$ is an **affine transformation**
$$
q_a = p_{ab} + R_{ab}q_b
$$
By using **homogeneous coordinates**, it can be represented in linear form
$$
\bar{q}_a=\begin{bmatrix}
q_1 \\ 1
\end{bmatrix}=\begin{bmatrix}
R_{ab} & p_{ab} \\
0 & 1
\end{bmatrix}\begin{bmatrix}
q_b \\ 1
\end{bmatrix}
:= \bar{g}_{ab}\bar{q}_b
$$

### Special Euclidean Group

Define $SE(3)$ as
$$
SE(3)=\{(p, R): p\in\mathbb{R}^3,\ R\in SO(3)\}
$$
Define $se(3)$ as
$$
se(3):=\{(v, \hat{\omega}: v\in\mathbb{R}^3,\ \hat{\omega} \in so(3) \}
$$

### Exponential Coordinates

Similar to $SO(3)$, the exponential mapping can be generalized to $SE(3)$
$$
\bar{g}=e^{\hat{\xi} \theta} \\
\hat{\xi} = \begin{bmatrix}
\hat{\omega} & -\omega\times q \\
0 & 0
\end{bmatrix} \in se(3)
$$
where $\omega$ is the axis of rotation and $q$ is a point on the axis.

$\xi:=(v, \omega)$ is the **twist coordinates** of $\hat{\xi}$
$$
\begin{bmatrix}
v \\ \omega
\end{bmatrix}^\wedge = \begin{bmatrix}
\hat{\omega} & v \\
0 & 0
\end{bmatrix}
$$
When $\|\omega\| = 1$
$$
\begin{equation}\label{gexp}
e^{\hat{\xi} \theta} = \begin{bmatrix}
e^{\hat{\omega} \theta} & (I - e^{\hat{\omega}\theta}) (\omega \times v) + \omega\omega^Tv\theta) \\
0 & 1
\end{bmatrix} \\
\end{equation}
$$
**The exponential map from $se(3)$ to $SE(3)$ is surjective**

Note that this represents the **relative** motion of a rigid body:
$$
\begin{gather*}
p(\theta) = e^{\hat{\xi}\theta} p(0) \\
g_{ab}(\theta) = e^{\hat{\xi}\theta}g_{ab}(0)
\end{gather*}
$$

## Screws

Screw motion is defined as rotation about an axis by $\theta = M$ angles, followed by translation along the *same* axis by $d=h\theta$.

- Pitch: $h:=d/\theta$. If $h=\infty$ then it represent pure translation by $M$
- Axis: $l=\{q+\lambda \omega : \lambda \in \mathbb{R}\}$, where $q$ is a point on the axis and $\omega$ is the direction
- Magnitude: $M$

Represent screw motion in homogeneous coordinates:
$$
g = \begin{bmatrix}
e^{\hat{\omega} \theta} & \left(I-e^{\hat{\omega} \theta}\right) q+h \theta \omega \\
0 & 1
\end{bmatrix}
$$
Compared with $\eqref{gexp}$, if we choose $v=-\omega\times q + h\omega$, then $\xi=(v,\omega)$ generates the same screw motion.

### Twist to Screw

Given twist $\xi = (v, \omega)$, the screw coordinates are:

- Pitch: $h=\frac{\omega^{T} v}{\|\omega\|^{2}}$
- Axis: $l=\begin{cases}
  \frac{\omega \times v}{\|\omega\|^{2}}+\lambda \omega &\lambda \in \mathbb{R}, & \text {if } \omega \ne 0 \\
  0+\lambda v &\lambda \in \mathbb{R}, & \text {if } \omega=0
  \end{cases}$
- Magnitude: $M=\begin{cases}
  \|\omega\|, & \text {if } \omega \ne 0 \\
  \|v\|, & \text {if } \omega=0
  \end{cases}$

### Screw to Twist

If $h=\infty$. Let $l=\{q + \lambda v: \|v\| = 1\}$, $\theta = M$, the twist is $\hat{\xi} = \begin{bmatrix}0 & v \\ 0 & 0\end{bmatrix}$

if $h\ne\infty$. Let $l=\{q + \lambda \omega: \|\omega\| = 1\}$, $\theta=M$, the twist is $\hat{\xi} = \begin{bmatrix}\hat{\omega} & -\omega\times q + h\omega \\ 0 & 0\end{bmatrix}$

## Velocity of a Rigid Body

### Rotational Velocity

From $q_a(t)=R_{ab}(t) q_b$, we can get the velocity of the point in spatial coordinates:
$$
v_{q_{a}}(t)=\frac{d}{d t} q_{a}(t)=\dot{R}_{a b}(t) q_{b} = \dot{R}_{a b}(t) R_{a b}^{-1}(t) R_{a b}(t) q_{b}
$$
Define spatial angular velocity $\hat{\omega}_{ab}^s$ and body angular velocity $\hat{\omega}_{ab}^b$ as
$$
\hat{\omega}_{ab}^s := \dot{R}_{ab} R_{ab}^{-1}, \qquad \hat{\omega}_{ab}^b := R_{ab}^{-1} \dot{R}_{ab}
$$
And the velocity becomes
$$
\begin{gather*}
v_{q_{a}}(t)=\hat{\omega}_{a b}^{s} R_{a b}(t) q_{b}=\omega_{a b}^{s}(t) \times q_{a}(t) \\
v_{q_b}(t) = R_{ab}^T(t) v_{q_a}(t) = \omega_{ab}^b(t) \times q_b
\end{gather*}
$$

### Rigid Body Velocity

Similar to rotational velocity, from $q_a(t) = g_{ab}(t) q_b$ we have
$$
v_{q_a} = \frac{d}{dt}q_a(t) = \dot{g}_{ab} q_b = \dot{g}_{ab} g_{ab}^{-1} q_a
$$
Define the spatial velocity $\hat{V}_{ab}^s \in se(3)$ and body velocity $\hat{V}_{ab}^b \in se(3)$
$$
\begin{gather*}
\hat{V}_{a b}^{s}=\dot{g}_{a b} g_{a b}^{-1}, \qquad V_{a b}^{s} =\begin{bmatrix}
v_{a b}^{s} \\
\omega_{a b}^{s}
\end{bmatrix}=\begin{bmatrix}
-\dot{R}_{a b} R_{a b}^{T} p_{a b}+\dot{p}_{a b} \\
\left(\dot{R}_{a b} R_{a b}^{T}\right)^{\vee}
\end{bmatrix} \\
\hat{V}_{a b}^{b}=g_{a b}^{-1}\dot{g}_{a b}, \qquad V_{a b}^{b} =\begin{bmatrix}
v_{a b}^{b} \\
\omega_{a b}^{b}
\end{bmatrix}=\begin{bmatrix}
R_{a b}^{T} \dot{p}_{a b} \\
\left(R_{a b}^{T} \dot{R}_{a b}\right)^{\vee}
\end{bmatrix} \\
\end{gather*}
$$

And the velocity becomes
$$
\begin{gather*}
v_{q_{a}}=\hat{V}_{a b}^{s} q_{a}=\omega_{a b}^{s} \times q_{a}+v_{a b}^{s} \\
v_{q_b} = g_{ab}^{-1} v_{q_a} = \hat{V}_{ab}^b q_b = \omega_{a b}^{b} \times q_{b}+v_{a b}^{b}
\end{gather*}
$$

- $\omega_{ab}^s$: angular velocity of the body viewed in the spatial frame
- $v_{ab}^s$: velocity of a point on the body which is traveling through the **origin** of the spatial frame
- $\omega_{ab}^b$: angular velocity of the coordinate frame viewed in the current body frame
- $v_{ab}^b$: velocity of the origin of the body frame viewed in the current body frame

The spatial and body velocity are related by
$$
\begin{gather*}
\omega_{a b}^{s}=R_{a b} \omega_{a b}^{b} \\
v_{a b}^{s}=-\omega_{a b}^{s} \times p_{a b}+\dot{p}_{a b}=p_{a b} \times\left(R_{a b} \omega_{a b}^{b}\right)+R_{a b} v_{a b}^{b}
\end{gather*}
$$
Define the **adjoint transformation** associated with $g$ as
$$
\mathrm{Ad}_g = \begin{bmatrix}
R & \hat{p} R \\
0 & R
\end{bmatrix}
$$
Then we have $V_{ab}^s = \mathrm{Ad}_g V_{ab}^b$

### Velocity of a Screw

$$
\begin{split}
\hat{V}_{ab}^s &= \dot{g}_{ab}(\theta) g_{ab}^{-1}(\theta) \\
&= \frac{d}{dt}\left(e^{\hat{\xi}\theta} g_{ab}(0)\right) \left(g_{ab}^{-1}(0) e^{-\hat{\xi}\theta}\right) \\
&= \hat{\xi}\dot{\theta}
\end{split}
$$

$$
\begin{split}
\hat{V}_{a b}^{b} &=g_{a b}^{-1}(\theta) \dot{g}_{a b}(\theta) \\
&=\left(g_{a b}^{-1}(0) e^{-\hat{\xi} \theta}\right)\left(e^{\hat{\xi} \theta} \hat{\xi} \dot{\theta} g_{a b}(0)\right) \\
&=\left(g_{a b}^{-1}(0) \hat{\xi} g_{a b}(0)\right) \dot{\theta} \\
&=\left(\mathrm{Ad}_{g_{a b}^{-1}(0)} \xi\right)^{\wedge} \dot{\theta}
\end{split}
$$

==Note== that $\frac{d}{dt}e^{A(t)} = \dot{A}(t) e^{A(t)} = e^{A(t)}\dot{A}(t)$ iff $A$ and $\dot{A}$ commute

### Coordinate Transformation

Transformation of spatial velocity:
$$
V_{a c}^{s}=V_{a b}^{s}+\mathrm{Ad}_{g_{a b}} V_{b c}^{s}
$$
Transformation of body velocity:
$$
V_{a c}^{b}=\mathrm{Ad}_{g_{b c}^{-1}} V_{a b}^{b}+V_{b c}^{b}
$$
Important properties:
$$
\begin{gather*}
V_{a b}^{b}=-V_{b a}^{s} \\
V_{a b}^{b}=-\mathrm{Ad}_{g_{b a}} V_{b a}^{b}
\end{gather*}
$$

## Wrenches

Define wrench as a force/moment pair
$$
F = \begin{bmatrix}
f \\ \tau
\end{bmatrix} \in \mathbb{R}^6
$$
Let $B$ be a coordinate frame attached to a rigid body, then write $F_b = (f_b, \tau_b)$ for a wrench applied at the origin of $B$.

The infinitesimal work can be represented as
$$
\delta W = V_{ab}^b \cdot F_b
$$
Now consider another frame $C$ that is stationary w.r.t. $B$, the work should be the same:
$$
V_{ac}^b \cdot F_c = V_{ab}^b \cdot F_b = (\mathrm{Ad}_{g_{bc}} V_{ac}^b)^T F_b = V_{ac}^b \cdot \mathrm{Ad}_{g_{bc}}^T F_b
$$
so that
$$
\begin{gather*}
F_c = \mathrm{Ad}_{g_{bc}}^T F_b \\
\begin{bmatrix}
f_c \\ \tau_c
\end{bmatrix} = \begin{bmatrix}
R_{bc}^T & 0 \\ -R_{bc}^T \hat{p}_{bc} & R_{bc}^T
\end{bmatrix} \begin{bmatrix}
f_b \\ \tau_b
\end{bmatrix}
\end{gather*}
$$

# Manipulator Kinematics

## Forward Kinematics

Consider open-chain manipulators with base frame $S$ and tool frame $T$ connected by a series of revolute or prismatic joints. The **joint (configuration) space** $Q$ of a manipulator consists of all possible values of the joint variables the robot.

The forward kinematics map $g_{st}: Q \to SE(3)$ is given by
$$
\begin{equation}\label{fk}
g_{s t}(\theta)=e^{\hat{\xi}_{1} \theta_{1}} e^{\hat{\xi}_{2} \theta_{2}} \cdots e^{\hat{\xi}_{n} \theta_{n}} g_{s t}(0)
\end{equation}
$$
where $\xi_i$ must be numbered sequentially starting from the base

## Manipulator Jacobian

Since $g: \mathbb{R}^n \to SE(3)$ is a matrix-valued function, the Jacobian $\frac{\partial g}{\partial \theta}$ cannot be easily obtained. Instead we derive it from the twist notation.

### End-Effector Velocity

$$
\begin{split}
\hat{V}_{st}^s &= \dot{g}_{st}(\theta) g_{st}^{-1}(\theta) \\
&= \sum_{i=1}^n \left( \frac{\partial g_{st}}{\partial \theta_i} \dot{\theta}_i \right) g_{st}^{-1}(\theta) \\
&= \sum_{i=1}^n \left( \frac{\partial g_{st}}{\partial \theta_i} g_{st}^{-1}(\theta) \right) \dot{\theta}_i
\end{split}
$$

It can be written as
$$
\begin{gather*}
V_{st}^s = J_{st}^s(\theta) \dot{\theta} \\
J_{st}^s(\theta) = \left[ \left(\frac{\partial g_{st}}{\partial \theta_1} g_{st}^{-1} \right)^\vee \dots \left(\frac{\partial g_{st}}{\partial \theta_n} g_{st}^{-1} \right)^\vee \right]
\end{gather*}
$$
where $J_{st}^s(\theta) \in \mathbb{R}^{6\times n}$ is called the **spatial manipulator Jacobian**

If we represent the forward kinematics as $\eqref{fk}$ then
$$
\begin{split}
\left(\frac{\partial g_{s t}}{\partial \theta_{i}}\right) g_{s t}^{-1} &=e^{\hat{\xi}_{1} \theta_{1}} \cdots e^{\hat{\xi}_{i-1} \theta_{i-1}} \frac{\partial}{\partial \theta_{i}}\left(e^{\hat{\xi}_{i} \theta_{i}}\right) e^{\hat{\xi}_{i+1} \theta_{i+1}} \cdots e^{\hat{\xi}_{n} \theta_{n}} g_{s t}(0) g_{s t}^{-1} \\
&=e^{\hat{\xi}_{1} \theta_{1}} \cdots e^{\hat{\xi}_{i-1} \theta_{i-1}}\left(\hat{\xi}_{i}\right) e^{\hat{\xi}_{i} \theta_{i}} \cdots e^{\hat{\xi}_{n} \theta_{n}} g_{s t}(0) g_{s t}^{-1} \\
&=e^{\hat{\xi}_{1} \theta_{1}} \cdots e^{\hat{\xi}_{i-1} \theta_{i-1}}\left(\hat{\xi}_{i}\right) e^{-\hat{\xi}_{i-1} \theta_{i-1}} \cdots e^{-\hat{\xi}_{1} \theta_{1}}
\end{split}
$$
Converting to twist coordinates:
$$
\left(\frac{\partial g_{s t}}{\partial \theta_{i}} g_{s t}^{-1}\right)^{\vee}=\mathrm{Ad}_{(e^{\hat{\xi}_{1} \theta_{1}} \cdots e^{\hat{\xi}_{i-1} \theta_{i-1}})} {\xi_{i}}
$$
The spatial manipulator Jacobian becomes
$$
\begin{gather*}
J_{st}^s(\theta) = \begin{bmatrix}
\xi_1 & \xi_2' & \dots & \xi_n'
\end{bmatrix} \\
\xi_i' = \mathrm{Ad}_{(e^{\hat{\xi}_{1} \theta_{1}} \cdots e^{\hat{\xi}_{i-1} \theta_{i-1}})} {\xi_{i}}
\end{gather*}
$$
which means that the $i$-th column of the spatial Jacobian is the $i$-th joint twist, transformed to the current manipulator configuration.

The **body manipulator Jacobian** can be defined similarly:
$$
\begin{gather*}
J_{st}^b(\theta) = \begin{bmatrix}
\xi_1^\dagger & \dots & \xi_{n-1}^\dagger & \xi_n^\dagger
\end{bmatrix} \\
\xi_i^\dagger = \mathrm{Ad}^{-1}_{(e^{\hat{\xi}_{i} \theta_{i}} \cdots e^{\hat{\xi}_{n} \theta_{n}} g_{st}(0))} {\xi_{i}}
\end{gather*}
$$
The columns of $J_{st}^b$ correspond to the joint twists written w.r.t. the tool frame at the current configuration.

The spatial and boy Jacobians are related by an adjoint transformation:
$$
J_{st}^s(\theta) = \mathrm{Ad}_{g_{st}(\theta)} J_{st}^b(\theta)
$$
The manipulator Jacobian (when invertible) can be used to move a robot without calculating the inverse kinematics by
$$
\dot{\theta}(t) = [J_{st}^s(\theta)]^{-1} V_{st}^s(t)
$$

### Singularities

A **singular configuration** of a robot manipulator is a configuration at which the manipulator Jacobian drops rank.

4 common singularity cases:

**Two collinear revolute joints**: There exist two revolute joints with twists
$$
\xi_1 = \begin{bmatrix}
-\omega_1 \times q_1 \\
\omega_1
\end{bmatrix} \qquad
\xi_2 = \begin{bmatrix}
-\omega_2 \times q_2 \\
\omega_2
\end{bmatrix}
$$
with the following conditions:

1. The axes are parallel: $\omega_1 = \pm \omega_2$
2. The axes are collinear: $\omega_i \times(q_1 - q_2) = 0$

**Three parallel coplanar revolute joint axes**:

1. The axes are parallel: $\omega_i = \pm \omega_j$ for $i,j=1,2,3$
2. The axes are coplanar: there exists $n$ s.t. $n^T \omega_i = 0$ and $n^T(q_i - q_j) = 0$ for $i,j=1,2,3$

**Four intersecting revolute joint axes**: There exists a point $q$ s.t. $\omega_i \times(q_i - q) = 0$ for $i=1,2,3,4$

**Four parallel joint axes**: The axes are parallel: $\omega_i = \pm\omega_j$ for $i=1,2,3,4$

### Manipulability

The **manipulability** of a robot describes its ability to move freely in all directions in the workspace.

1. The ability to *reach* a certain position or set of positions
2. The ability to *change* the position or orientation at a given configuration

Manipulability measure:

1. Minimum singular value: $\sigma_\min(J)$
2. Inverse of the condition number: $\sigma_\min(J) / \sigma_\max(J)$
3. Determinant: $\det J$
