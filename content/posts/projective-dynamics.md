---
title: Projective Dynamics
date: 2018-03-17
updated: 2026-06-16
categories:
- Study
tags:
- CG
- Paper Reading
---

Paper review: [Projective Dynamics: Fusing Constraint Projections for Fast Simulation](https://sofienbouaziz.com/papers/Projective%20Dynamics-%20Fusing%20Constraint%20Projections%20for%20Fast%20Simulation.pdf)

- Author: Sofien Bouaziz, Sebastian Martin, Tiantian Liu, Ladislav Kavan, Mark Pauly
- Venue: ACM SIGGRAPH 2014
- Core Contribution: Bridges the gap between Continuum Mechanics (FEM) and Position Based Dynamics (PBD) by introducing an unconditionally stable, hyper-fast implicit Euler solver based on a **Local/Global alternating optimization** framework (Block Coordinate Descent).

<!--more-->

---

# Background & Motivation

Simulating deformable bodies (e.g., cloth, soft tissue, shells) in physics-based animation traditionally forces a compromise between two conflicting paradigms:

- **FEM / Continuum Mechanics**:
    - Mechanism: Rigorously formulated from strain energy potentials and typically integrated via the implicit Euler method for large time steps.
    - Bottleneck: Solving the nonlinear implicit system relies on Newton's method. This requires recomputing and refactoring a massive, position-dependent Hessian matrix at every single iteration. Extreme element inversion or deformation can render the Hessian indefinite, leading to numerical divergence (explosions) without expensive line search protections.
- **PBD**:
    - Mechanism: Bypasses force computation, directly manipulating vertex positions by sequentially projecting them onto geometric constraints via a Gauss-Seidel approach.
    - Bottleneck: Lacks a rigorous continuum mechanics foundation. Material stiffness is artificially tied to iteration counts and mesh resolution. Furthermore, sequential projection violates momentum conservation, washing away individual point inertia and resulting in purely inelastic behavior (like plasticine) at high iteration counts.

**Goal**: Design an implicit integrator that inherits the **physical accuracy and resolution-independence** of continuum mechanics, while matching the **computational speed, simplicity, and absolute stability** of PBD.

# Core Mathematical Insight: Decoupling Energy Potentials

The core innovation lies in decoupling geometric nonlinearities from material distance metrics.

An implicit Euler time step can be framed as an optimization problem:

$$
\min_{\mathbf{q}} \frac{1}{2h^2} \norm*{\mathbf{M}^{\frac{1}{2}} (\mathbf{q} - \mathbf{s}_n)}_F^2 + \sum_i W_i(\mathbf{q})
$$

- $\mathbf{q}$: The unknown position vector for the next frame.
- $\mathbf{M}$: The mass matrix.
- $h$: The time step length.
- $\mathbf{s}_n = \mathbf{q}_n + h\mathbf{v}_n + h^2\mathbf{M}^{-1}\mathbf{f}_{\text{ext}}$: The inertial predictor position.
- $W_i(\mathbf{q})$: The nonlinear elastic potentials.

Instead of directly optimizing the nonlinear strain energy $W_{i}(\mathbf{q})$, Projective Dynamics introduces an auxiliary target variable $\mathbf{p}_{i}$ for each constraint. This $\mathbf{p}_{i}$ is mathematically forced to reside on an ideal, undeformed constraint manifold defined by $\mathbf{C}_{i}(\mathbf{p}_{i}) = 0$, enforced via an indicator function $\delta_{\mathbf{C}_{i}}(\mathbf{p}_{i})$.

Consequently, the deformation energy is reformulated as a convex, purely quadratic distance between the actual deformed coordinates $\mathbf{q}$ and the ideal projected states $\mathbf{p}_{i}$:

$$
W_{i}(\mathbf{q}, \mathbf{p}_{i}) = \frac{w_{i}}{2}\norm*{\mathbf{A}_{i}\mathbf{S}_{i}\mathbf{q} - \mathbf{B}_{i}\mathbf{p}_{i}}_F^2 + \delta_{\mathbf{C}_{i}}(\mathbf{p}_{i})
$$

- $\mathbf{S}_{i}$: A constant selection matrix extracting vertices involved in constraint $i$.
- $\mathbf{A}_{i}, \mathbf{B}_{i}$: Constant matrices derived from rest-state geometry (e.g., differential/discrete gradient operators).
- $w_{i}$: A non-negative material stiffness weight.

# The Local/Global Optimization Framework

By splitting the objective function across $\mathbf{q}$ and $\mathbf{p}_i$, the system can be solved with extreme efficiency using **Block Coordinate Descent**:

1. **Local Solve (Constraint Projection)**: Fixing the vertex positions $\mathbf{q}$, optimize each auxiliary variable $\mathbf{p}_{i}$ independently

    $$
    \min_{\mathbf{p}_i} \frac{w_i}{2} \norm*{\mathbf{A}_i \mathbf{S}_i \mathbf{q} - \mathbf{B}_i \mathbf{p}_i }_F^2 \quad \text{s.t.} \quad \mathbf{C}_{i}(\mathbf{p}_{i}) = 0
    $$

    - Geometric Meaning: Find a state $\mathbf{p}_{i}$ on the constraint manifold that is geometrically closest to the element's current deformed state.
    - Performance: Completely decoupled across elements, allowing massive CPU/GPU parallelization. For standard constraints, this reduces to trivial local SVDs or analytic normalizations.
2. **Global Solve (Linear Consensus)**: Fixing all target states $\mathbf{p}_i$ computed in the local step, optimize the global vertex coordinates $\mathbf{q}$. Since the energy function is purely quadratic w.r.t. $\mathbf{q}$, setting its gradient to zero yields a linear system

    $$
    \left(\frac{\mathbf{M}}{h^2} + \sum_i w_i \mathbf{S}_i^T \mathbf{A}_i^T \mathbf{A}_i \mathbf{S}_i \right) \mathbf{q} = \frac{\mathbf{M}}{h^2} \mathbf{s}_n + \sum_i w_i \mathbf{S}_i^T \mathbf{A}_i^T \mathbf{B}_i \mathbf{p}_i
    $$

    - Performance: The left-hand side system matrix consists entirely of constant terms ($\mathbf{M}, h, w_{i}, \mathbf{A}_{i}, \mathbf{S}_{i}$). It remains invariant to rotation and deformation, allowing it to be pre-factored once during initialization using sparse Cholesky factorization ($\mathbf{L}\mathbf{L}^T$). Runtime execution only requires updating the right-hand side vector and performing computationally cheap back-substitution.

# Connection to PBD

The paper establishes a formal unification demonstrating how PBD relates to implicit integration:

| Feature             | PBD                                                                     | Projective Dynamics                                                                    |
| ------------------- | ----------------------------------------------------------------------- | -------------------------------------------------------------------------------------- |
| Solver Paradigm     | Gauss-Seidel (sequential update)                                        | Jacobi (parallel update) + Linear Consensus                         |
| Energy Minimization | Often oscillates or fails under conflicting constraints.                | Guaranteed to reach a global energy consensus.                                         |
| Momentum & Inertia  | Strips point inertia during projection; violates momentum conservation. | Explicitly locks inertial momentum into the optimization via the global linear system. |

**The Unification**: Mathematically, if one sets $\mathbf{A}_{i} = \mathbf{B}_{i} = \mathbf{M}_{i}^{\frac{1}{2}}$ (mass-weighted absolute coordinates), Projective Dynamics directly reduces to a generalized Jacobi version of PBD, but crucially fortified with true implicit Euler momentum properties.

# Supported Continuum and Discrete Constraints

By defining specific $\mathbf{A}_i, \mathbf{B}_i$ matrices and local projection manifolds, Projective Dynamics unifies diverse constraint types under a single solver:

- **Strain & Elasticity**: Built upon continuous deformation gradients. Setting the constraint manifold to $SO(3)$ models standard elasticity. Capping the allowable singular values ($\sigma_{\min} \le \sigma \le \sigma_{\max}$) yields robust **isotropic strain limiting** (e.g., non-ripping cloth).
- **Volume Preservation**: Restricting the determinant of local transformations ($\sigma_{\min} \le \det(\mathbf{T}) \le \sigma_{\max}$) preserves volume for incompressible materials like flesh or rubber.
- **Bending**: Leverages discrete differential geometry where the mean curvature normal equals the Laplace-Beltrami operator acting on coordinates. The resulting local step bypasses SVD entirely, requiring only trivial vector normalization.
- **Discrete External Constraints**: Positional constraints and **collisions** fit natively into the framework. Collisions are injected dynamically as unilateral half-space constraints ($\mathbf{n}^T(\mathbf{q}-\mathbf{b}) \ge 0$). The projection is a trivial plane mapping. Because it is solved via Jacobi consensus, it completely circumvents the sticky artifact common in classical implicit collision handling.

# Performance & Limitations

**Strengths**:

- Unconditional Numerical Stability: Both the Local and Global steps mathematically guarantee a monotonic decrease (or weak decrease) of the objective energy. The solver cannot diverge or explode, even with massive time steps or degenerate mesh configurations.
- **Interactive Performance**: While Newton's method exhibits quadratic convergence (requiring fewer iterations), Projective Dynamics is vastly faster in terms of clock time. Roughly 30 Projective Dynamics iterations match the execution time of a single Newton step.

**Limitations**:

- **Implicit Damping**: Due to its implicit Euler ancestry, terminating the solver early (with low iteration counts) introduces artificial numerical damping, causing materials to appear overly viscous or sluggish.
- **Topological Changes**: Cutting or fracturing alters mesh topology, changing the global system matrix. This violates the "constant matrix" premise, requiring expensive refactorization or dynamic rank-1 updates.
- **Soft Approximations of Hard Constraints**: Treating hard boundaries (e.g., collisions, pinning) as soft constraints with high stiffness weights $w_i$ degrades the numerical conditioning of the linear system, potentially leading to visual locking artifacts.
