---
title: Position Based Dynamics
date: 2018-03-16
categories:
- Study
tags:
- CG
- Paper Reading
---

This [paper](http://interactive-graphics.de/index.php/research/89-position-based-simulation-methods-in-computer-graphics) introduces position-based methods and its differences from other methods. Moreover, it presents the algorithm and several solvers. Specific constraints are also discussed in detail. Finally, Implementation and application are briefly presented.
<!-- more -->

---

# Constraints

Constraints are kinematic restrictions in the form of equations and inequalities that constrain the relative motion of bodies. Equality and inequality constraints are referred to as *bilateral* and *unilateral* constraints, respectively.

**Bilateral constraint**
$$
C(\mathbf{x}_{i_1}, \mathbf{q}_{i_1}, \cdots , \mathbf{x}_{i_{n_j}}, \mathbf{q}_{i_{n_j}}) = 0
$$
**Unilateral constraint**
$$
C(\mathbf{x}_{i_1}, \mathbf{q}_{i_1}, \cdots , \mathbf{x}_{i_{n_j}}, \mathbf{q}_{i_{n_j}}) \ge 0
$$
where $\{i_1, \cdots, i_{n_j}\}$ is a set of body indices and $n_j$ is the cardinality of the constraint. Typically, the constraints used in PBD only depend on positions and time but not on velocities. Such constraints are called *holonomic*.

# The Algorithm

Initialization
$$
\mathbf{x}_i = \mathbf{x}_i^0, \quad \mathbf{v}_i = \mathbf{v}_i^0, \quad w_i = 1/m_i
$$
for all vertices do
$$
\begin{equation}\label{predict}
\begin{gathered}
\mathbf{v}_i \gets \mathbf{v}_i + \Delta t w_i \mathbf{f}_\text{ext}(\mathbf{x}_i) \\
\mathbf{p}_i \gets \mathbf{x}_i + \Delta t \mathbf{v}_i
\end{gathered}
\end{equation}
$$
then loop iterations times and
$$
\begin{equation}\label{correction}
\text{projectConstarints}(C_1, \dots, C_{M+M\text{coll}}, \mathbf{p}_1, \dots, \mathbf{p}_N)
\end{equation}
$$
finally for all vertices do
$$
\begin{gather*}
\mathbf{v}_i \gets (\mathbf{p}_i - \mathbf{x}_i) / \Delta t, \\
\mathbf{x}_i \gets \mathbf{p}_i
\end{gather*}
$$
Eq. $\eqref{predict}$ perform a simple symplectic Euler integration step on the velocities and the positions. The new location $\mathbf{p}_i$ are only used as predictions. Eq. $\eqref{correction}$ then iteratively corrects the predicted positions such that satisfy the $M_{coll}$ external as well as the $M$ internal constraints.

**Damping**:

The quality of dynamic simulations can generally be improved by the incorporation of an appropriate damping scheme. As a positive effect, damping can improve the stability by reducing temporal oscillations of the point positions of an object. This enables larger time steps. On the other hand, damping changes the dynamic motion. The resulting effects can be either desired, e.g. reduced oscillations of a deformable solid, or disturbing, e.g. changes of the linear or angular momentum of the entire object.

Generally, a damping term $\mathbf{C} \dot{\mathbf{X}}$ can be incorporated into the motion equation of an object where $\dot{\mathbf{X}}$ denoted the vector of all first time derivatives of positions. If $\mathbf{C}$ is diagonal, absolute velocities of the points are damped, which sometimes is referred to as point damping. In order to preserve linear and angular momentum, symmetric damping forces, usually referred to as spring damping forces, can be used.

# Solver

## Bilateral Constraints

Newton-Raphson iteration can be used
$$
C(\mathbf{x} + \Delta\mathbf{x}) = C(\mathbf{x}) + \nabla C(\mathbf{x}) \cdot \Delta \mathbf{x} + O(|\Delta \mathbf{x}|^2) = 0
$$
which yields a linear system for the global correction vector $\Delta \mathbf{x}$
$$
\Delta C(\mathbf{x}) \cdot \Delta \mathbf{x} = - C(\mathbf{x})
$$
if $M=3N$ then it could be solved by any linear solver, if $M \ne 3N$ the resulting matrix of the linear system is non-symmetric and not invertible. This could be solved by using the pseudo-inverse of the system matrix which yields the best solution in the least-squares sense.

## Unilateral Constraints

Non-linear Gauss-Seidel solver is used. It solves each constraint equation **separately**. First the constraint equation is approximated by
$$
C(\mathbf{x} + \Delta \mathbf{x}) \approx C(\mathbf{x}) + \nabla C(\mathbf{x}) \cdot \Delta \mathbf{x} \succ 0
$$
The problem of the system being under-determined is solved by restricting $\Delta \mathbf{x}$ be in the direction of $\nabla C$ which is also a requirement for linear and angular momentum conservation. This means that a Lagrange multiplier has to be found such that
$$
\Delta \mathbf{x} = -\lambda \mathbf{M}^{-1} \nabla C(\mathbf{x})
$$
where $\mathbf{M} = \operatorname{diag}(m_1, m_2, \dots, m_N)$.

for each particle $i$
$$
\Delta \mathbf{x} = -\lambda w_i \nabla_{\mathbf{x}_i} C(\mathbf{x})
$$
where
$$
\lambda = \frac{C(\mathbf{x})}{\sum_j w_j |\nabla_{\mathbf{x}_j} C(\mathbf{x})|^2}
$$
formulated for the concatenated vector $\mathbf{x}$ of all positions we get
$$
\lambda = \frac{C(\mathbf{x})}{\nabla C(\mathbf{x})^T M^{-1} \nabla C(\mathbf{x})}
$$
The solver linearized the constraint function but in contrast to the Newton-Raphson method, it happen individually per constraint. Because the positions are immediately updated after a constraint is processed, these updates will influence the linearization of the next constraint because the linearization depends on the actual position.

## Hierarchical Solver

The Gauss-Seidel method is stable and easy to implement but it typically converges significantly slower than global solvers. The main reason is that error corrections are propagated only locally from constraint to constraint. Therefore, the Gauss-Seidel method is called a smoother because it evens out the high frequency errors much faster than low frequency errors.

A popular method to increase the convergence rate of the Gauss-Seidel method is to create a hierarchy of meshes in which the coarse meshes make sure that error corrections propagate fast across the domain. A smoother works on all meshes of the hierarchy one by one while the error corrections are carried over across meshes of different resolutions typically in multiple cycles from fine to coarse levels and back. This technique is called the **multi-grid method**. Transferring corrections from coarse to fine meshes and from fine to coarse meshes is called *prolongation* and *restriction*, respectively.

## Connection to Implicit Methods

$$
\begin{align*}
\mathbf{x}^{n+1} &= \mathbf{x}^n + \Delta t \mathbf{v}^{n+1} \\
\mathbf{v}^{n+1} &= \mathbf{v}^n + \Delta t \mathbf{M}^{-1} (\mathbf{F}_\text{ext} + k \nabla \mathbf{C}^{n+1})
\end{align*}
$$

where $\mathbf{C}$ is the vector of constraint potentials, and $k$ is the stiffness, we can eliminate velocity and see this as the first order optimality condition for the following minimization:
$$
\min_{\mathbf{x}} \dfrac{1}{2} (\mathbf{x}^{n+1} - \tilde{\mathbf{x}})^T \mathbf{M} (\mathbf{x}^{n+1} - \tilde{\mathbf{x}}) - \Delta t^2 k \mathbf{C}^{n+1}
$$
where $\tilde{\mathbf{x}}$ is the predicted position
$$
\begin{split}
\tilde{\mathbf{x}} &= 2\mathbf{x}^n - \mathbf{x}^{n-1} + \Delta t^2 \mathbf{M}^{-1} \mathbf{F}_\text{ext} \\
&= \mathbf{x}^n + \Delta t \mathbf{v}^n + \Delta t^2 \mathbf{M}^{-1} \mathbf{F}_\text{ext}
\end{split}
$$

**Second Order Methods**:

We can also use a second order accurate BDF2 update equations:
$$
\begin{align*}
\mathbf{x}^{n+1} &= \dfrac{4}{3} \mathbf{x}^n - \dfrac{1}{3} \mathbf{x}^{n-1} + \dfrac{2}{3} \Delta t \mathbf{x}^{n+1} \\
\mathbf{v}^{n+1} &= \dfrac{4}{3} \mathbf{v}^n - \dfrac{1}{3} \mathbf{v}^{n-1} + \dfrac{2}{3} \Delta t \mathbf{M}^{-1} (\mathbf{F}_\text{ext} + k \nabla \mathbf{C}^{n+1})
\end{align*}
$$
we can also eliminating velocity and consider it a minimization problem.

To evaluate this more accurate scheme we need only store the previous position and velocity, and perform some additional basic arithmetic during the prediction and velocity up-date steps, while the rest of the PBD algorithm is unchanged.

# Specific Constraints

It discussed specific constraints, stretching, bending, isometric bending, collisions, volume conservation, long range attachments, strands, continuous materials, rigid body dynamics, fluids and shape matching.

# Discussion

**Advantages:** Classical dynamics simulation methods formulate the change of momentum of a system as a function of applied forces, and evolve positions through numerical integration of accelerations and velocities. Position-based approaches, instead, compute positions directly, based on the solution to a quasi-static problem. Therefore, they are more fast, stable and controllable which make them well-suited for use in interactive environment. However, they are generally not as accurate as force-based methods but still provide visual plausibility. Hence, the main application are virtual reality, computer games and special effects in movies and commercials.

**Limitations:**

1. The stiffness of the model does not only depend on the user-defined stiffness parameter but also on the time step size and the number of solver iterations. Although the dependency can be reduces, it cannot be completely removed. Therefore, it is difficult to adjust parameters independently.
2. Not convergent
