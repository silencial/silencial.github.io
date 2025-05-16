---
title: Projective Dynamics
date: 2018-03-17
categories:
- Study
tags:
- CG
- Paper Reading
---

[Paper](http://sofienbouaziz.com/publications.php) Reading Note

<!--more-->

---

# Implicit Euler Solver

$$
\begin{gather*}
\mathbf{q}_{n+1}=\mathbf{q}_n+h\mathbf{v}_{n+1} \\
\mathbf{v}_{n+1}=\mathbf{v}_n+h\mathbf{M}^{-1}(\mathbf{f}_{\text{int}}(\mathbf{q}_{n+1})+\mathbf{f}_{\text{ext}})
\end{gather*}
$$
This system can be converted to an optimization problem:
$$
\require{mathtools}
\DeclarePairedDelimiters\norm{\lVert}{\rVert}
\min_{\mathbf{q}_{n+1}} \dfrac{1}{2h^2} \norm*{\mathbf{M}^{\frac{1}{2}} (\mathbf{q}_{n+1} - \mathbf{s}_n)}_F^2 + \sum_i W_i(\mathbf{q}_{n+1})
$$
where $\mathbf{s}_n = \mathbf{q}_n + h\mathbf{v}_n + h^2\mathbf{M}^{-1}\mathbf{f}_{\text{ext}}$ and $W_i(\mathbf{q})$ is a scalar potential energy function.

Intuitively, this minimization problem describes the compromise between the momentum potential
$$
\begin{equation}\label{potential}
\dfrac{1}{2h^2} \norm*{\mathbf{M}^{\frac{1}{2}} (\mathbf{q}_{n+1} - \mathbf{s}_n)}_F^2
\end{equation}
$$
which states that the solution should follow its momentum (plus external forces), and the elastic potential, that requires the solution to minimize the elastic deformation. The corresponding weighting terms, i.e. the mass distribution in $M$, the time step $h$ and the material stiffness of $W$, determine which potential has more importance in this balance.

# Nonlinear Elasticity

In nonlinear continuum mechanics elastic potentials are $W(\mathbf{q}) = \Psi (\mathbf{E}(\mathbf{q}))$, where $\mathbf{E}(\mathbf{q})$ is the strain. We can observe that $\mathbf{E}(\mathbf{q}) = 0$ defines a constraint manifold of all possible undeformed configurations, while $\Psi (\mathbf{E}(\mathbf{q}))$ measures how far the deformed configuration is from this manifold (like level set). Therefore, these two concepts can be decoupled; the distance metric does not have to be a complicated nonlinear function because the nonlinearities are already captured by the constraint manifold.

**Decoupling distance measure and constraint manifold**:
$$
W(\mathbf{q}, \mathbf{p}) = d(\mathbf{q}, \mathbf{p}) + \delta_\mathbf{E}(\mathbf{p})
$$
Here, $\delta_\mathbf{E}(\mathbf{p})$ is an indicator function that evaluates to zero if $\mathbf{E}(\mathbf{p}) = 0$ and to $+\infty$ otherwise, and formalizes the requirement that $\mathbf{p}$ should lie on the constraint manifold. The function $d(\mathbf{q}, \mathbf{p})$ then measures a distance between ${\bf q}$ and ${\bf p}$. Minimizing it over $\mathbf{p}$ corresponds to a projection of $\mathbf{q}$ onto the constraint manifold. An analogous elastic potential can be defined as $\tilde{W}(\mathbf{q}) = \min_\mathbf{p} W(\mathbf{q}, \mathbf{p})$

**Quadratic distance measures**:

An important advantage of this formulation is that the distance measure can be freely chosen. The constraint nonlinearity (also known as geometric nonlinearity) is already taken care of by the projection on the constraint set, so the distance metric can be kept simple, trading general material nonlinearity against efficiency and robustness. Specifically, consider distance metrics leading to the following potentials:
$$
W(\mathbf{q}, \mathbf{p}) = \frac{w}{2}\norm*{\mathbf{Aq} - \mathbf{Bp}}_F^2 + \delta_\mathbf{C}(\mathbf{p})
$$
where $\mathbf{A}$ and $\mathbf{B}$ are constant matrices and $w$ is a nonnegative weight. We are not restricted to Green’s strains but can use any constraint definition $\mathbf{C}(\mathbf{q}) = 0$ for the set of desired configurations.

From Implicit Euler Solver we now get **Projective Implicit Euler Solver**:
$$
\frac{1}{2h^2} \norm*{\mathbf{M}^{\frac{1}{2}} (\mathbf{q} - \mathbf{s}_n)}_F^2 + \sum_i \frac{w_i}{2} \norm*{\mathbf{A}_i \mathbf{S}_i \mathbf{q} - \mathbf{B}_i \mathbf{p}_i }_F^2 + \delta_{\mathbf{C}_i}(\mathbf{p}_i)
$$
where $\mathbf{S}_i$ is a constant selection matrix that selects the vertices involved in the $i$-th constraint.

**Local solve**: First minimize over the auxiliary variables keeping the positions fixed
$$
\min_{\mathbf{p}_i} \dfrac{w_i}{2} \norm*{\mathbf{A}_i \mathbf{S}_i \mathbf{q} - \mathbf{B}_i \mathbf{p}_i }_F^2 + \delta_{\mathbf{C}_i}(\mathbf{p}_i)
$$
**Global solve**: Then minimize over the positions, keeping the auxiliary variables fixed.
$$
\left(\frac{\mathbf{M}}{h^2} + \sum_i w_i \mathbf{S}_i^T \mathbf{A}_i^T \mathbf{A}_i \mathbf{S}_i \right) \mathbf{q} = \frac{\mathbf{M}}{h^2} \mathbf{s}_n + \sum_i w_i \mathbf{S}_i^T \mathbf{A}_i^T \mathbf{B}_i \mathbf{p}_i
$$
The system matrix (left hand side) is constant as long as the constraints are not changing and therefore can be prefactored at initializations. The right hand side requires recomputation in each iteration after the projection variables have been updated in the local step.

Can choose $\mathbf{A}_i = \mathbf{B}_i$ as differential coordinate matrices to improve convergence since the internal physical constraints are translation invariant. But needed to be derived from continuum formulations to avoid tessellation and resolution dependent. Note that the choice of $\mathbf{A}_i$ and $\mathbf{B}_i$ only impacts the numerical solution procedure and does not affect the conservation of momentum.

# PBD View

The constraint resolution strategy of PBD actually implements a Gauss-Seidel type minimization on the energy
$$
\dfrac{1}{2} \sum_i \norm*{\mathbf{M}^{\frac{1}{2}} (\mathbf{S}_i \mathbf{q} - \mathbf{p}_i)}_F^2 + \delta_{C_i}(\mathbf{p}_i)
$$
using a lumped mass matrix $\mathbf{M}_i$ only involving the constraint's points. A Gauss-Seidel approach minimizes this energy by optimizing each summand sequentially, i.e. minimizing potentials of the form $\dfrac{1}{2} \norm{\mathbf{M}^{\frac{1}{2}} \Delta \mathbf{q}}_F^2 + \delta_C (\mathbf{q} + \Delta \mathbf{q})$ where $\Delta\mathbf{q} = \mathbf{p} - \mathbf{q}$. Using Lagrange multipliers for the linearized constraint $C(\mathbf{q}) + \operatorname{tr}(\nabla C(\mathbf{q})^T \Delta \mathbf{q}) = 0$, we can define the Lagrangian
$$
\dfrac{1}{2} \norm*{\mathbf{M}^{\frac{1}{2}} \Delta \mathbf{q}}_F^2 + \lambda \left(C(\mathbf{q}) + \operatorname{tr}(\nabla C(\mathbf{q})^T \Delta \mathbf{q})\right)
$$
Using the critical point condition w.r.t. $\Delta \mathbf{q}$, we find the optimal direction $\Delta \mathbf{q} = -\lambda \mathbf{M}^{-1} \nabla C(\mathbf{q})$. The Lagrange multiplier $\lambda$ can then be found by requiring that the linearized constraint vanishes in this direction, i.e. $C(\mathbf{q}) - \lambda \norm{\mathbf{M}^{-\frac{1}{2}} \nabla C(\mathbf{q})}_F^2 = 0$, leading to the final update
$$
\Delta \mathbf{q} = - \mathbf{M}^{-1} \nabla C(\mathbf{q}) \frac{C(\mathbf{q})}{\norm*{\mathbf{M}^{-\frac{1}{2}} \nabla C(\mathbf{q})}_F^2}
$$
corresponding exactly to the mass-weighted update rule of PBD.

**Jacobi Solver**:

While Jacobi solvers have in general slower convergence than Gauss-Seidel solvers, they allow the use of differential coordinate representations for faster convergence and efficient parallelization of the constraint projections that resolve this shortcoming. Second, we introduce the momentum constraint into the optimization to take into account the inertia of each point. As seen in the continuum mechanics view, to achieve a correct behavior we need to add back the inertia of each point by integrating the momentum constraint term defined in $\eqref{potential}$
$$
\dfrac{1}{2h^2} \norm*{\mathbf{M}^{\frac{1}{2}} (\mathbf{q}_{n+1} - \mathbf{s}_n)}_F^2 + \sum_i \dfrac{w_i}{2} \norm*{\mathbf{M}_i^{\frac{1}{2}} (\mathbf{S}_i \mathbf{q} - \mathbf{p}_i)}_F^2 + \delta_{C_i}(\mathbf{p}_i)
$$
Its connection to projective implicit Euler is by choosing $\mathbf{A}_i = \mathbf{B}_i = \mathbf{M}_i^{\frac{1}{2}}$

# Discussion

The rest of the paper implements several constraints from continuum mechanics' view. Discrete constraints like positional constraints and collisions are also discussed.

This method has a great generality and numerical stability. The only requirement is that
the mesh elements of the input model are well behaved in order to compute the discretization of the gradient and Laplace-Beltrami operators of the original manifold.

**Comparison with Newton**: Newton’s method exhibits quadratic convergence while local/global
solvers have linear convergence.

**Comparison with PBD**: PBD does not include the momentum constraints making the material stiffness dependent of the number of iterations.

**Limitation**:

1. Implicit damping
2. Trading hard constraints for simplicity and efficiency. Although they can be approximated by increasing the weight of the constraints, this can degrade the conditioning of the linear system and can result in locking artifacts.
