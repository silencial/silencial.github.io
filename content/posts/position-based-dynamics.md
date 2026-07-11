---
title: Position Based Dynamics
date: 2018-03-16
updated: 2026-06-06
categories:
- Study
tags:
- CG
- Paper Reading
---

This paper [Position-Based Simulation Methods in Computer Graphics](https://mmacklin.com/EG2015PBD.pdf) introduces Position-Based Dynamics (PBD) and compares it with traditional force-based methods. It details the core algorithm, evaluates various solvers, and analyzes specific constraints. Finally, it briefly discusses implementation strategies and practical applications.

<!--more-->

---

# Constraints

Constraints represent kinematic restrictions in the form of equations and inequalities that govern the relative motion of bodies.

- **Bilateral constraints** (Equality constraints): $C(\mathbf{x}_{i_1}, \mathbf{q}_{i_1}, \dots , \mathbf{x}_{i_{n_j}}, \mathbf{q}_{i_{n_j}}) = 0$
- **Unilateral constraint** (Inequality constraints): $C(\mathbf{x}_{i_1}, \mathbf{q}_{i_1}, \dots , \mathbf{x}_{i_{n_j}}, \mathbf{q}_{i_{n_j}}) \ge 0$

where $\{i_1, \dots, i_{n_j}\}$ is a set of body indices, and $n_j$ is the cardinality of the constraint.

Holonomic constraints: Typically, constraints used in PBD depend only on positions and time but not on velocities.

# The Algorithm

The core execution of PBD follows a predictive-corrective loop:

1. Initialization: Total of $N$ particles

    $$
    \mathbf{x}_i = \mathbf{x}_i^0, \quad \mathbf{v}_i = \mathbf{v}_i^0, \quad w_i = 1/m_i
    $$

2. Prediction: Use semi-implicit Euler method to predict unconstrained positions

    $$
    \begin{align*}
    \mathbf{v}_i &\gets \mathbf{v}_i + \Delta t w_i \mathbf{f}_\text{ext}(\mathbf{x}_i) \\
    \mathbf{p}_i &\gets \mathbf{x}_i + \Delta t \mathbf{v}_i
    \end{align*}
    $$

3. Constraint projection: Iteratively corrects the predicted positions $\mathbf{p}_{i}$ to satisfy the $M_{\text{coll}}$ external and $M$ internal constraints

    $$
    \begin{equation}
    \text{projectConstraints}(C_1, \dots, C_{M+M_\text{coll}}, \mathbf{p}_1, \dots, \mathbf{p}_N)
    \end{equation}
    $$

4. Velocity and position update: Derive the velocities from the corrected positions and update the current states

    $$
    \begin{align*}
    \mathbf{v}_i &\gets (\mathbf{p}_i - \mathbf{x}_i) / \Delta t, \\
    \mathbf{x}_i &\gets \mathbf{p}_i
    \end{align*}
    $$

## Damping

Incorporating an appropriate damping scheme improves the quality and stability of dynamic simulations by reducing temporal oscillations of point positions, thereby allowing for larger time steps. However, damping inherently alters dynamic motion. This can be beneficial (e.g., suppressing vibrations in a deformable solid) or detrimental (e.g., unintentionally dampening the linear or angular momentum of the entire object).

A general damping term $\mathbf{C} \dot{\mathbf{X}}$ can be added to the object's equations of motion, where $\dot{\mathbf{X}}$ is the vector of all first time derivatives of positions.

- Point damping: If $\mathbf{C}$ is a diagonal matrix, the absolute velocities of individual points are damped.
- Spring damping: To preserve overall linear and angular momentum, symmetric damping forces should be applied.

# Solver for Constraint Projection

Consider a single constraint function $C(\mathbf{x})$.

## Bilateral Constraints

Apply Newton's method:

$$
C(\mathbf{x} + \Delta\mathbf{x}) = C(\mathbf{x}) + \nabla C(\mathbf{x}) \cdot \Delta \mathbf{x} + O(|\Delta \mathbf{x}|^2) = 0
$$

This yields a linear system for the global correction vector $\Delta \mathbf{x}$:

$$
\nabla C(\mathbf{x}) \cdot \Delta \mathbf{x} = - C(\mathbf{x})
$$

- If $M=3N$: The number of constraints exactly matches the system's total degrees of freedom. The system can be solved by any linear solvers.
- If $M \ne 3N$: The resulting matrix of the linear system is non-symmetric and non-invertible. The pseudoinverse of the system matrix yields the best solution in a least-squares sense.

## Unilateral Constraints

A non-linear Gauss-Seidel solver is primarily employed in PBD, processing each constraint equation **separately and sequentially**.

First, the constraint is approximated linearly:

$$
C(\mathbf{x} + \Delta \mathbf{x}) \approx C(\mathbf{x}) + \nabla C(\mathbf{x}) \cdot \Delta \mathbf{x} \geq 0
$$

Calculating $\Delta \mathbf{x}$ can be framed as an optimization problem:

$$
\min \frac{1}{2} \Delta \mathbf{x}^T \mathbf{M} \Delta \mathbf{x}
$$

where $\mathbf{M}$ is the diagonal mass matrix.

Applying the KKT conditions, the correction direction $\Delta \mathbf{x}$ must lie along the direction of the constraint gradient $\nabla C$:

$$
\Delta \mathbf{x} = -\lambda \mathbf{M}^{-1} \nabla C(\mathbf{x})
$$

For an active constraint, solving for the Lagrange multiplier yields

$$
\lambda = \frac{C(\mathbf{x})}{\nabla C(\mathbf{x})^T M^{-1} \nabla C(\mathbf{x})}
$$

Unlike global Newton's method, the Gauss-Seidel solver linearizes the constraint function individually per constraint. Since positions are immediately updated after processing a single constraint, these updates continuously alter the linearization of subsequent constraints.

## Hierarchical Solver

While stable and straightforward, the Gauss-Seidel method converges significantly slower than global solvers because error corrections propagate only locally. It acts primarily as a **smoother**, evening out high-frequency errors much faster than low-frequency errors.

To accelerate convergence, the **multigrid method** creates a hierarchy of meshes. Coarse meshes facilitate rapid propagation of error corrections across the entire domain. The smoother operates iteratively across all hierarchical levels. Transferring corrections between resolutions is defined as:

- Prolongation: Transferring from coarse to fine meshes.
- Restriction: Transferring from fine to coarse meshes.

## Connection to Implicit Methods

The predictive-corrective sequence of PBD is mathematically equivalent to solving a backward Euler integration step. This equivalence proves PBD's unconditional stability.

Consider a standard implicit integration step:

$$
\begin{align*}
\mathbf{x}^{n+1} &= \mathbf{x}^n + \Delta t \mathbf{v}^{n+1} \\
\mathbf{v}^{n+1} &= \mathbf{v}^n + \Delta t \mathbf{M}^{-1} (\mathbf{F}_\text{ext} + k \nabla \mathbf{C}^{n+1})
\end{align*}
$$

where $\mathbf{C}$ represents the vector of constraint potentials, and $k$ is the stiffness scalar.

Eliminating velocities and rearrange:

$$
\mathbf{x}^{n+1} = \underbrace{\mathbf{x}^n + \Delta t \mathbf{v}^n + \Delta t^2 \mathbf{M}^{-1} \mathbf{F}_\text{ext}}_{\text{PBD Prediction } \tilde{\mathbf{x}}} + \Delta t^2 \mathbf{M}^{-1} k \nabla \mathbf{C}^{n+1}
$$

This can be seen as the first order optimality condition for the following minimization problem:

$$
\min_{\mathbf{x}} \frac{1}{2} (\mathbf{x}^{n+1} - \tilde{\mathbf{x}})^T \mathbf{M} (\mathbf{x}^{n+1} - \tilde{\mathbf{x}}) - \Delta t^2 k \mathbf{C}^{n+1}
$$

**Physical interpretation**: Find the closest point on the constraint manifold to the predicted position (in a mass-weighted measure).

## Second Order Methods

After the connection to implicit method is made, we can apply higher order integration schemes to PBD, such as a second-order backward differentiation formula (BDF2):

$$
\begin{align*}
\mathbf{x}^{n+1} &= \frac{4}{3} \mathbf{x}^n - \frac{1}{3} \mathbf{x}^{n-1} + \frac{2}{3} \Delta t \mathbf{v}^{n+1}\\
\mathbf{v}^{n+1} &= \frac{4}{3} \mathbf{v}^n - \frac{1}{3} \mathbf{v}^{n-1} + \frac{2}{3} \Delta t \mathbf{M}^{-1} (\mathbf{F}_\text{ext} + k \nabla \mathbf{C}^{n+1})
\end{align*}
$$

Similarly, velocity can be eliminated:

$$
\mathbf{M}(\mathbf{x}^{n+1} - \tilde{\mathbf{x}}) = \frac{4}{9} \Delta t^{2}k\nabla \mathbf{C}^{n+1}
$$

where the predicted position $\tilde{\mathbf{x}}$ is given by

$$
\mathbf{x}^{n+1} = \frac{4}{3} \mathbf{x}^n - \frac{1}{3} \mathbf{x}^{n-1} + \frac{8}{9} \Delta t \mathbf{v}^{n} - \frac{2}{9} \Delta t \mathbf{v}^{n-1} + \frac{4}{9} \Delta t^2 \mathbf{M}^{-1} \mathbf{F}_{\text{ext}}
$$

To implement BDF2, we need only store the previous time step's position and velocity, and perform some additional arithmetic during the prediction phase, while the core PBD constraint projection solver remains completely unchanged.

# Specific Constraints

The paper extensively formulates projection gradients for various specific constraints:

- Stretching
- Bending
- Isometric bending
- Collisions
- Volume conservation
- Long range attachments
- Strands
- Continuous materials
- Rigid body dynamics
- Fluids
- Shape matching

# Discussion

Advantages:

- Computes positions directly (a geometric/quasi-static problem) rather than integrating accelerations.
- Extremely fast, unconditionally stable, and controllable (eliminates overshoot issues from stiff springs).
- Highly suitable for VR, games, and VFX (prioritizes visual plausibility over strict physical accuracy).

Limitations:

- Stiffness coupling: Macroscopic stiffness relies heavily on time step size $\Delta t$ and iteration count, not just the stiffness parameter $k$. (Solved in later works like XPBD).
- Convergence: As a smoother, Gauss-Seidel is not mathematically guaranteed to converge to a unique global solution, especially with conflicting constraints.
