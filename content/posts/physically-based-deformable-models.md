---
title: Physically Based Deformable Models
date: 2018-03-15
updated: 2026-06-11
categories:
- Study
tags:
- CG
- Paper Reading
---

This paper [Physically Based Deformable Models in Computer Graphics](https://matthias-research.github.io/pages/publications/egstar2005.pdf) outlines the core methods used in computer graphics to produce realistic animations and physical simulations, discussing the benefits and drawbacks of each method while summarizing improvements from related research.

- Finite element / Difference / Volume methods
- Mass-spring systems
- Meshfree methods
- Coupled particle systems
- Reduced deformable models (based on modal analysis)

<!--more-->

---

# Continuum Elasticity

In continuum mechanics, a deformable object is treated as a continuous mass rather than a collection of discrete particles. The state of the material is analyzed using continuous fields of position, displacement, strain, and stress.

The deformation of a body is tracked by mapping its rest (material) coordinates $\mathbf{m}$ to its current (spatial) coordinates $\mathbf{x}$ via a continuous function $\mathbf{x}(\mathbf{m})$. The displacement field $\mathbf{u}(\mathbf{m})$ is defined as

$$
\mathbf{u}(\mathbf{m}) = \mathbf{x}(\mathbf{m}) - \mathbf{m}
$$

From $\mathbf{u}(\mathbf{m})$ the elastic strain $\boldsymbol{\epsilon}$ is computed:

- Green-Lagrange strain: Rotationally invariant. Suitable for large deformations. However, the quadratic term makes it nonlinear and computationally expensive.

    $$
    \boldsymbol{\epsilon} = \frac{1}{2}(\nabla \mathbf{u} + [\nabla \mathbf{u}]^T + [\nabla \mathbf{u}]^T \nabla \mathbf{u})
    $$

- Cauchy strain: Computationally cheap but only valid for small deformations.

    $$
    \boldsymbol{\epsilon}_{C} = \frac{1}{2}(\nabla \mathbf{u} + [\nabla \mathbf{u}]^T)
    $$

The stress $\boldsymbol{\sigma}$ is usually computed by Hooke's linear material law:

$$
\boldsymbol{\sigma} = \mathbf{E} \cdot \boldsymbol{\epsilon}
$$

# Time Integration

Time integration schemes are evaluated based on two primary criteria: **stability** and **accuracy**. In the field of physically based animation for computer graphics, stability is typically prioritized over accuracy.

## Explicit (Forward) Euler Method

$$
\begin{align*}
\mathbf{x}(t+\Delta t) &= \mathbf{x}(t) + \Delta t \mathbf{v}(t) \\
\mathbf{v}(t+\Delta t) &= \mathbf{v}(t) + \Delta t F(\mathbf{v}(t), \mathbf{x}(t), t)
\end{align*}
$$

- **Pros**: Easy to implement.
- **Cons**: Only conditionally stable, i.e., stable only if time step $\Delta t$ is smaller than a specific stability threshold.
- The instability issue can be solved by using implicit methods.

## Implicit (Backward) Euler Method

$$
\begin{align*}
\mathbf{x}(t+\Delta t) &= \mathbf{x}(t) + \Delta t \mathbf{v}(t + \Delta t) \\
\mathbf{v}(t+\Delta t) &= \mathbf{v}(t) + \Delta t F(\mathbf{v}(t + \Delta t), \mathbf{x}(t + \Delta t), t)
\end{align*}
$$

- **Pros**: Unconditionally stable, even for large time steps $\Delta t$.
- **Cons**: Computationally expensive. It requires solving an algebraic system of equations at each time step to find the future state.

## Semi-Implict Euler Method

$$
\begin{align*}
\mathbf{v}(t+\Delta t) &= \mathbf{v}(t) + \Delta t F(\mathbf{v}(t), \mathbf{x}(t), t) \\
\mathbf{x}(t+\Delta t) &= \mathbf{x}(t) + \Delta t \mathbf{v}(t + \Delta t)
\end{align*}
$$

- **Pros**: Remains an explicit method without any additional computational overhead, yet it is more stable than the standard forward Euler method.

# Lagrangian Mesh Based Methods

## Finite Element Method (FEM)

The PDE governing dynamic elastic materials based on continuum mechanics is given by

$$
\begin{equation}
\label{fem}
\rho \ddot{\mathbf{x}} = \nabla \cdot \boldsymbol{\sigma} + \mathbf{f}_{\text{ext}}
\end{equation}
$$

To discretize the continuous domain, the function $\tilde{\mathbf{x}}(\mathbf{m}, t)$ is approximated using discrete nodal values $\mathbf{x}_{i}(t)$ and fixed basis functions $\mathbf{b}_{i}(\mathbf{m})$:

$$
\tilde{\mathbf{x}}(\mathbf{m}, t) = \sum_i \mathbf{x}_i(t) \mathbf{b}_i(\mathbf{m})
$$

The deformation energy $E$ of an element is calculated by integrating the componentwise scalar product of the strain tensor $\boldsymbol{\epsilon}$ and stress tensor $\boldsymbol{\sigma}$ over its volume $V$:

$$
E = \int_V \boldsymbol{\epsilon}(\mathbf{m}) \cdot \boldsymbol{\sigma}(\mathbf{m}) d \mathbf{m}
$$

The forces acting on the nodes are derived as the derivatives of this deformation energy with respect to the nodal positions. When linearized, the relationship between the nodal forces $\mathbf{f}_e$ and the nodal displacements $\mathbf{u}_e$ for an element can be expressed using an element stiffness matrix $\mathbf{K}_e$:

$$
\begin{equation}
\label{nodal}
\mathbf{f}_e = \mathbf{K}_e \mathbf{u}_e
\end{equation}
$$

By assembling the element stiffness matrices ($\mathbf{K}=\sum_{e} \mathbf{K}_{e}$) and applying mass lumping for the mass matrix $\mathbf{M}$ and damping matrix $\mathbf{D}$, the linear algebraic equation of motion for the entire discretized mesh becomes

$$
\mathbf{M} \ddot{\mathbf{u}} + \mathbf{D} \dot{\mathbf{u}} + \mathbf{K} \mathbf{u} = \mathbf{f}_{\text{ext}}
$$

Substituting a linear strain measure (Cauchy strain) and Hooke’s law for isotropic materials into the governing PDE $\eqref{fem}$ yields Lame's linear PDE:

$$
\rho \ddot{\mathbf{x}} = \mu \nabla^2 \mathbf{u} + (\lambda +\mu) \nabla (\nabla \cdot \mathbf{u})
$$

where the Lame parameters $\lambda$ and $\mu$ can be computed directly from Young's modulus and Poisson's ratio.

Unfortunately, linearized elastic forces are only valid for small deformations. Large rotational deformations yield highly inaccurate, artificially inflated restoring forces. To resolve this, the nodal force equation $\eqref{nodal}$ is updated to extract the rotational component $\mathbf{R}$:

$$
\mathbf{f} = \mathbf{RK}(\mathbf{R}^T \mathbf{x} - \mathbf{x}_0)
$$

## Finite Difference Method (FDM)

Unlike FEM, which utilizes integral formulations and basis functions over complex volumes, FDM discretizes the governing PDE $\eqref{fem}$ directly. It substitutes continuous spatial derivatives with finite difference approximations on nodes of a regular grid.

- **Pros**: Easier to implement than FEM due to its straightforward mathematical formulation.
- **Cons**:
    - Difficult to approximate the boundary of an arbitrary object with a regular mesh.
    - Local adaptations are only feasible with irregular meshes, which complicates the formulation.

## Finite Volume Method (FVM)

Unlike FEM, which computes nodal forces via deformation energy derivatives, FVM computes nodal forces directly from the stress tensor $\boldsymbol{\sigma}$:

$$
\mathbf{f}_A = \int_A \boldsymbol{\sigma} d \mathbf{A}
$$

When linear basis functions are used, the stress tensor remains constant within an element. For planar element faces, the surface integral reduces to a simple product:

$$
\mathbf{f}_A = A \boldsymbol{\sigma} \mathbf{n}
$$

## Boundary Element Method (BEM)

Apply the divergence theorem to transform the integral form of the motion equation into a surface integral.

- **Pros**: Achieves substantial computational speedup by reducing a 3D volumetric problem into a 2D surface problem.
- **Cons**:
    - Only works for objects composed of a homogenous material.
    - Handling topological changes (e.g., fractures) is more difficult than in explicit FEM, where only local stiffness matrices and connectivity of the elements require updates.

## Mass-Spring Systems

Instead of discretizing a continuous PDE, mass-spring systems build a discrete model directly from the ground up. The entire particle system is governed by

$$
\mathbf{M} \ddot{\mathbf{x}} = \mathbf{f}(\mathbf{x}, \mathbf{v})
$$

where $\mathbf{M}$ is a $3n \times 3n$ diagonal mass matrix. This reduces the physics simulation to solving a system of coupled ODEs via time integration.

- **Pros**:
    - Intuitive and straightforward to implement.
    - Computationally efficient, handling large deformations with ease.
- **Cons**:
    - Lacks the rigorous accuracy of FEM or FDM, which are grounded in continuum elasticity theory.
    - Behavior is non-convergent; physical properties change drastically depending on mesh resolution and topology.
    - Spring constants $k_s$ are usually tuned arbitrarily, making it difficult to model real-world, quantitative material properties.
    - Cross-coupling effects often occur between different spring types (e.g., structural, shear, bending springs).

### Force Computations

- Spring force: Generated by a spring connecting mass $i$ and $j$, where $\mathbf{x}_{ij} = \mathbf{x}_{j} - \mathbf{x}_{i}$

    $$
    \mathbf{f}_i = k_s(|\mathbf{x}_{ij}| - l_{ij}) \frac{\mathbf{x}_{ij}}{|\mathbf{x}_{ij}|}
    $$

- Damping: Standard damping $\mathbf{f}_i = k_d (\mathbf{v}_j - \mathbf{v}_i)$ erroneously damps rigid body rotations. When modeling cloth, it inappropriately dampens bending and wrinkling behaviors. It is preferable to project the velocity difference onto the vector separating the masses, allowing force application solely along that direction.

    $$
    \mathbf{f}_i = k_d \left( \frac{\mathbf{v}_{ij}^T \mathbf{x}_{ij}}{\mathbf{x}_{ij}^T \mathbf{x}_{ij}} \right) \mathbf{x}_{ij}
    $$

# Lagrangian Mesh Free Methods

## Loosely Coupled Particle Systems

Particles that interact with each other based on their spatial relationship are referred to as spatially coupled particle systems. Because the interactions between particles evolve dynamically over time, complex geometries and topological changes (e.g., merging, splitting) can be easily modeled.

The potential energy $\phi_i$ for each particle $p_i$ is defined as the sum of the pairwise potential energies $\phi_{ij}$ between $p_i$ and all other particles $p_j$:

$$
\phi_i = \sum_{j\ne i} \phi_{ij}
$$

The resulting force on the particle is the negative gradient of this potential:

$$
\mathbf{f}_i = -\nabla_{\mathbf{x}_i} \phi_i = -\sum_{j\ne i} \nabla_{\mathbf{x}_i} \phi_{ij}
$$

### Lennard-Jones Potential

A classic example used in molecular dynamics is the Lennard-Jones potential:

$$
\phi_{LJ}(d) = \frac{B}{d^n} - \frac{A}{d^m}
$$

It creates long-range attractive forces and short-range repulsive forces. In the absence of external forces, it naturally yields particles arranged into hexagonally ordered 2D layers.

A more physically intuitive formulation is the Lennard-Jones bi-reciprocal function:

$$
\phi(d) = \frac{-e_0}{m-n} \left( m \left( \frac{d_0}{d} \right)^n - n \left( \frac{d_0}{d} \right)^m \right)
$$

- $d_0$: Equilibrium separation distance.
- $−e_0$: Minimal potential (its magnitude is the **dissociation energy**).

Tuning physical properties:

- Rigid/Brittle materials: Achieved by setting a large dissociation energy and high exponents ($n, m$), creating a narrow and deep potential well.
- Soft/Elastic/Fluid-like materials: Achieved by setting a low dissociation energy and small exponents.

## Smoothed Particle Hydrodynamics (SPH)

SPH is a Lagrangian technique where discrete, smoothed particles are used to compute approximate values of physical quantities and their spatial derivatives.

- **Pros**:
    - Forces can be derived directly from state equations.
    - As a particle-based Lagrangian approach, mass conservation is trivially guaranteed, and explicit convection tracking is dispensable. This reduces both programming and computational complexity, making it suitable for interactive applications.
- **Cons**: Hard to exactly maintain the **incompressibility** in materials.

In SPH, a continuous function $A$ is interpolated at any position $\mathbf{x}$ using a summation interpolant from its neighboring particles:

$$
A(\mathbf{x}) = \sum_j m_j \frac{A_j}{\rho_j} W(\mathbf{r}, h)
$$

- $\mathbf{r} = \mathbf{x} - \mathbf{x}_j$: The distance vector between the evaluation point and particle $j$.
- $W(\mathbf{r}, h)$: A chosen smoothing kernel with a support radius $h$.

The smoothing kernel must satisfy two properties:

- $\int W(\mathbf{r}, h) d\mathbf{r} = 1$
- $\lim_{h\to 0} W(\mathbf{x}, h) = \delta(\mathbf{x})$

For efficiency reasons, $h$ is usually chosen such that $W(\mathbf{r}, h)$ has compact support (i.e., it drops to exactly zero beyond the radius $h$). Spline-based or Gaussian kernels are most commonly used.

## Point-Based Animation

For deformable solids, elastic body forces are derived via the strain energy density $U$:

$$
U = \frac{1}{2} (\boldsymbol{\epsilon} \cdot \boldsymbol{\sigma})
$$

Assuming a Hookean material ($\boldsymbol{\sigma} = \mathbf{E} \cdot \boldsymbol{\epsilon}$), the force on particle $i$ is

$$
\mathbf{f}_i = -\nabla_{\mathbf{u}_i} U = -\frac{1}{2} \nabla_{\mathbf{u}_i} (\boldsymbol{\epsilon}_i \cdot \mathbf{E} \cdot \boldsymbol{\epsilon}_i) = -\boldsymbol{\sigma} \cdot \nabla_{\mathbf{u}_i} \boldsymbol{\epsilon}_i
$$

### Moving Least Squares (MLS) Approximation

If Green's nonlinear strain tensor is used for $\boldsymbol{\epsilon}_i$, evaluating the force requires the spatial derivative of the displacement field $\nabla\mathbf{u}_i$ at position $\mathbf{m}_i$.

To guarantee that rigid body motions do not incorrectly produce elastic forces, the approximation of $\nabla\mathbf{u}_i$ from neighboring particles must be at least first order accurate. Therefore, point-based animation utilizes the **Moving Least Squares Method** with a linear basis (contrary to the SPH approximation which is not even zeroth-order accurate).

Consider only the $x$-component $u$ of the displacement field $\mathbf{u} = (u, v, w)^T$. The first order approximation $\tilde{u}_j$ is

$$
\tilde{u}_j = u_i + \nabla u|_{\mathbf{m}_i} \cdot \mathbf{m}_{ij}
$$

We define a weighted least-squares error function $e_i$:

$$
e_i = \sum_j (\tilde{u}_j - u_j)^2 w_{ij}
$$

where $w_{ij}$ is a normalized, monotonically decreasing weighting function based on distance.

To find the unknown gradient $\nabla u|_{\mathbf{m}_i}$ that minimizes this error, we set the partial derivatives of $e_{i}$ (with respect to $u_{,x}\ u_{,y}\ u_{,z}$) to zero. This yields three linear equations for the three unknowns:

$$
\left( \sum_j \mathbf{m}_{ij} \mathbf{m}_{ij}^T w_{ij} \right) \nabla u|_{\mathbf{m}_i} = \sum_j (u_j - u_i) \mathbf{m}_{ij} w_{ij}
$$

Solving for the gradient:

$$
\nabla u|_{\mathbf{m}_i} = \mathbf{A}^{-1} \left( \sum_j (u_j - u_i) \mathbf{m}_{ij} w_{ij} \right)
$$

Here, the shape matrix $\mathbf{A} = \sum_j \mathbf{m}_{ij} \mathbf{m}_{ij}^T w_{ij}$ depends strictly on the reference geometry. Its inverse $\mathbf{A}^{-1}$ only needs to be computed once per particle during initialization and can be reused to compute the spatial derivatives for the $v$ and $w$ components.

## Shape Matching

Instead of calculating internal elastic forces through complex PDEs or strain energies, shape matching simulates deformation by continually pulling particles back toward their original undeformed "rest shape".

# Linear Model Analysis

Given the mass, damping and stiffness matrices $\mathbf{M}, \mathbf{C}, \mathbf{K}$, the governing equations of motion are typically coupled:

$$
\begin{equation}
\label{motion}
\mathbf{M} \ddot{\mathbf{u}} + \mathbf{C} \dot{\mathbf{u}} + \mathbf{K} \mathbf{u} = \mathbf{f}_{\text{ext}}
\end{equation}
$$

We can decouple this system into $N = 3n$ linearly independent ODEs by solving the generalized eigenvalue problem:

$$
\mathbf{M} \boldsymbol{\Phi} \boldsymbol{\Lambda} = \mathbf{K} \boldsymbol{\Phi}
$$

The matrices are constructed such that $\boldsymbol{\Phi}^T \mathbf{M} \boldsymbol{\Phi} = \mathbf{I}$ and $\boldsymbol{\Phi}^T \mathbf{K} \boldsymbol{\Phi} = \boldsymbol{\Lambda}$ are purely diagonal matrices.

- $\boldsymbol{\Lambda}$: A diagonal matrix containing the eigenvalues $\lambda_{i}$ of $\mathbf{M}^{-1} \mathbf{K}$.
- $\boldsymbol{\Phi}$: The **modal displacement matrix**. Its $i$-th column represents the $i$-th mode shape, and $\lambda_i$ is proportional to the resonant frequency of that mode.

Any displacement $\mathbf{u}(t) = \mathbf{x}(t) - \mathbf{x}_0$ can be expressed as a linear combination of these mode shapes using modal coordinates $\mathbf{q}(t)$:

$$
\mathbf{u}(t) = \boldsymbol{\Phi} \mathbf{q}(t)
$$

Substituting this into $\eqref{motion}$ and premultiplying by $\boldsymbol{\Phi}^T$ yields

$$
\boldsymbol{\Phi}^T \mathbf{M} \boldsymbol{\Phi} \ddot{\mathbf{q}} + \boldsymbol{\Phi}^T \mathbf{C} \boldsymbol{\Phi} \dot{\mathbf{q}} + \boldsymbol{\Phi}^T \mathbf{K} \boldsymbol{\Phi} \mathbf{q} = \boldsymbol{\Phi}^T \mathbf{f}_{\text{ext}}
$$

For general damping, $\boldsymbol{\Phi}^T \mathbf{C} \boldsymbol{\Phi}$ is a dense matrix. However, if we assume Raleigh (proportional) damping where $\mathbf{C} = \alpha \mathbf{M} + \beta \mathbf{K}$, the damping matrix also becomes strictly diagonal.

Defining the projected external forces as $\mathbf{g} = \boldsymbol{\Phi}^T \mathbf{f}_{\text{ext}}$, the entire system is reduced to $3n$ independent scalar 2nd order ODEs:

$$
\ddot{q}_i + (\alpha + \beta \lambda_i) \dot{q}_i + \lambda_i q_i = g_i
$$

We can solve each equation analytically.

# Eulerian and Semi-Lagrangian Methods

While Lagrangian techniques explicitly track moving particles or meshes, the Eulerian perspective observes a stationary grid and calculates how material properties (like fluid density or velocity) flow through those fixed grid points over time.

| Feature             | Lagrangian (Particle/Mesh)                                      | Eulerian (Grid-based)                                                   |
| ------------------- | --------------------------------------------------------------- | ----------------------------------------------------------------------- |
| Perspective         | Moving with the material.                                       | Stationary observer watching flow.                                      |
| Topological Changes | Difficult (requires remeshing or complex connectivity updates). | **Trivial** (splashing, merging, tearing handled naturally).            |
| Boundary Tracking   | **Trivial** (explicitly defined by particle/mesh positions).    | Difficult (requires extra techniques like Level Sets or MAC particles). |
| Mass Conservation   | Naturally perfectly conserved.                                  | Requires strict enforcement (e.g., via Pressure Projection).            |
| Advection           | Handled trivially by simply moving the particles.               | Suffers from numerical dissipation (smearing/blurring over grid cells). |

In computer graphics, fluid simulation is almost universally handled within an Eulerian framework.

The behavior of incompressible fluids is governed by the Navier-Stokes equations:

$$
\begin{align*}
\nabla \cdot \mathbf{u} = 0 \quad &\text{(Mass Conservation / Incompressibility)} \\
\mathbf{u}_t = -(\mathbf{u} \cdot \nabla) \mathbf{u} + \nu \nabla^2 \mathbf{u} - \nabla p + \mathbf{f} \quad &\text{(Momentum Conservation)}
\end{align*}
$$

In standard Eulerian implementations, fluid quantities are stored on a **staggered grid**: Velocities are stored on the cell faces, while scalar values like pressure are stored at the cell centers.

Solving the full momentum equation simultaneously is highly complex. Instead, it is broken down into simpler sequential steps—a technique known as operator splitting.

1. External forces: The force term $\mathbf{f}$ is scaled by $\Delta t$ and added to the current velocity.
2. Advection: The advection term $-(\mathbf{u} \cdot \nabla) \mathbf{u}$ computes how the fluid's own velocity carries itself through the grid. A popular algorithm for this is the **semi-Lagrangian scheme** because it is stable for large time steps.

After applying forces, viscosity, and advection, we obtain an intermediate, unconstrained velocity field called the best guess velocity $\tilde{\mathbf{u}}$:

$$
\tilde{\mathbf{u}} = \mathbf{u} + \Delta t \left( -(\mathbf{u} \cdot \nabla) \mathbf{u} + \nu \nabla^2 \mathbf{u} + \mathbf{f} \right)
$$

This $\tilde{\mathbf{u}}$ is invalid because it violates the incompressibility constraint (mass conservation). The **pressure-projection** step rectifies this. We must find a pressure field $p$ such that subtracting its gradient from $\tilde{\mathbf{u}}$ produces a final, divergence-free velocity $\mathbf{u}^\text{new}$:

$$
\begin{equation}
\mathbf{u}^\text{new} = \tilde{\mathbf{u}} - \Delta t \nabla p
\end{equation}
$$

Taking the divergence of both sides and set $\nabla \cdot \mathbf{u}^\text{new} = 0$, we obtain the Poisson equation for pressure:

$$
\nabla^2 p = \frac{1}{\Delta t} \nabla \cdot \tilde{\mathbf{u}}
$$
