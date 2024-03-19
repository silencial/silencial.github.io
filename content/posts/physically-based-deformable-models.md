---
title: Physically Based Deformable Models
date: 2018-03-15
categories:
- Study
tags:
- CG
- Paper Reading
---

This [paper](http://matthias-mueller-fischer.ch/publications/egstar2005.pdf) presents the most important methods used in computer graphics to produce realistic animations and simulations: *finite element/difference/volume methods, mass-spring systems, meshfree methods, coupled particle systems and reduced deformable models based on modal analysis*. Also, it discusses the benefits and drawbacks of each method, reports the improvement that made in other papers.
<!-- more -->

---

# Time Integration

Time integration schemes are evaluated by two main criteria, their stability and their accuracy. In the field of physically based animation in Computer Graphics, stability is often much more important than accuracy.

**Explicit(forward) Euler Integration**
$$
\begin{align*}
\mathbf{x}(t+\Delta t) &= \mathbf{x}(t) + \Delta t \mathbf{v}(t) \\
\mathbf{v}(t+\Delta t) &= \mathbf{v}(t) + \Delta t F(\mathbf{v}(t), \mathbf{x}(t), t)
\end{align*}
$$

Explicit methods are easy to implement but they are only conditionally stable, i.e. stable only if ∆t is smaller than a stability threshold. This problem can be solved by using *implicit integration*

**Implicit(backward) Euler Integration**
$$
\begin{align*}
\mathbf{x}(t+\Delta t) &= \mathbf{x}(t) + \Delta t \mathbf{v}(t + \Delta t) \\
\mathbf{v}(t+\Delta t) &= \mathbf{v}(t) + \Delta t F(\mathbf{v}(t + \Delta t), \mathbf{x}(t + \Delta t), t)
\end{align*}
$$
This method is stable for large time steps $\Delta t$ but with the price of having to solve an algebraic system of equation at each time step.

**Forward-Backward Euler Integration**
$$
\begin{align*}
\mathbf{v}(t+\Delta t) &= \mathbf{v}(t) + \Delta t F(\mathbf{v}(t), \mathbf{x}(t), t) \\
\mathbf{x}(t+\Delta t) &= \mathbf{x}(t) + \Delta t \mathbf{v}(t + \Delta t)
\end{align*}
$$
This method is still explicit but more stable than forward Euler integration, without any additional computational overhead.

# Lagrangian Mesh Based Methods

## Finite Element Method

The PDE governing dynamic elastic materials is given by
$$
\begin{equation}\label{fem}
\rho \ddot{\mathbf{x}} = \nabla \cdot \boldsymbol{\sigma} + \mathbf{f}
\end{equation}
$$
The function $\tilde{\mathbf{x}}(\mathbf{m}, t)$ is approximated using the nodal values by
$$
\tilde{\mathbf{x}}(\mathbf{m}, t) = \sum_i \mathbf{x}_i(t) \mathbf{b}_i(\mathbf{m})
$$
The deformation energy of the element is then given by
$$
E = \int_V \boldsymbol{\epsilon}(\mathbf{m}) \cdot \boldsymbol{\sigma}(\mathbf{m}) d \mathbf{m}
$$
The forces can then be computed as the derivatives of the energy with respect to the nodal positions
$$
\begin{equation}\label{nodal}
\mathbf{f}_e = \mathbf{K}_e \mathbf{u}_e
\end{equation}
$$
If a linear strain measure is used and Hooke’s law for isotropic materials is substituted into $\eqref{fem}$, Lame's linear PDE results:
$$
\rho \ddot{\mathbf{x}} = \mu \Delta \mathbf{u} + (\lambda +\mu) \nabla (\nabla \cdot \mathbf{u})
$$
Where the material constants $\lambda$ and $\mu$ can be computed directly from Young's modulus and Poisson's ratio.

Unfortunately, linearized elastic forces are only valid for small deformations. Large rotational deformations yield highly inaccurate restoring forces. Therefore, we replace the $\eqref{nodal}$ by
$$
\mathbf{f} = \mathbf{RK}(\mathbf{R}^T \mathbf{x} - \mathbf{x}_0)
$$

## Finite Differences Method

The method of Finite Differences is easier to implement than the general Finite Element Method. However, it is difficult to approximate the boundary of an arbitrary object with a regular mesh. Also, local adaptations are only possible with irregular meshes.

## Finite Volume Method

In the explicit Finite Element Method, the forces acting on the nodes of an element are computed as the derivatives of the deformation energy with respect to the nodal positions.

We compute the nodal force directly from the stress tensor $\boldsymbol{\sigma}$
$$
\mathbf{f}_A = \int_A \boldsymbol{\sigma} d \mathbf{A}
$$
When linear basis functions are used, the stress tensor is constant within an element. Then, for planar element faces, the surface integral reduces to the simple product
$$
\mathbf{f}_A = A \boldsymbol{\sigma} \mathbf{n}
$$

## Boundary Element Method

Roughly speaking, the integral form of the equation of motion is transformed into a surface integral by applying the Green-Gauss theorem. The method achieves substantial speedup because the three dimensional problem is reduced to two dimensions. However, the approach only works for objects whose interior is composed of a homogenous material. Also, topological changes (e.g. fractures) are more difficult to handle than in the explicit FEM approach where only local changes to the stiffness matrix or the connectivity of the elements need to be done.

## Mass-Spring Systems

Instead of beginning with a PDE then discretizing in space, we begin directly with a discrete model.

The entire particle system can be expressed as
$$
\mathbf{M} \ddot{\mathbf{x}} = \mathbf{f}(\mathbf{x}, \mathbf{v})
$$
where $\mathbf{M}$ is a $3n \times 3n$ diagonal mass matrix. Thus, mass-spring systems only require the solution of a system of coupled ODEs. This is done via time integration.

The force acting on mass $i$, generated by a spring connecting $i$ and $j$ together is
$$
\mathbf{f}_i = k_s(|\mathbf{x}_{ij}| - l_{ij}) \frac{\mathbf{x}_{ij}}{|\mathbf{x}_{ij}|}
$$
The *viscous force* is defined as
$$
\mathbf{f}_i = k_d (\mathbf{v}_j - \mathbf{v}_i)
$$
However, it damps rigid body rotations as well. Worse still, when modeling cloth it damps bending and wrinkling. It is preferable to use
$$
\mathbf{f}_i = k_d \left( \frac{\mathbf{v}_{ij}^T \mathbf{x}_{ij}}{\mathbf{x}_{ij}^T \mathbf{x}_{ij}} \right) \mathbf{x}_{ij}
$$
This *projects* the velocity difference onto the vector separating the masses, and only allows a force along that direction.

**Advantages:**

1. Particle systems tend to be intuitive and simple to implement.
2. Computationally efficient and can handle large deformations with ease.

**Limitations:**

1. Unlike the Finite Element and finite difference methods, which are built upon elasticity theory, mass-spring systems are not necessarily accurate.
2. Most such systems are not convergent and the behavior is dependent on the mesh resolution and topology.
3. Spring constants $k_s$ are often chosen arbitrarily so that one can say little quantitatively about the material being modeled.
4. There is often coupling between the various spring types.

**Time Integration**:

$$
\begin{bmatrix} \Delta \mathbf{x} \\ \Delta \mathbf{v} \end{bmatrix} =
\Delta t \begin{bmatrix} \mathbf{v}_n + \Delta \mathbf{v} \\ M^{-1} \mathbf{f}(\mathbf{x}_n +\Delta \mathbf{x}, \mathbf{v}_n +\Delta \mathbf{v}) \end{bmatrix}
$$
When using the first order Taylor series expansion of $\mathbf{f}$
$$
\mathbf{f}(\mathbf{x}_n +\Delta \mathbf{x}, \mathbf{v}_n +\Delta \mathbf{v}) = \mathbf{f}_n + \frac{\partial \mathbf{f}}{\partial \mathbf{x}} \Delta \mathbf{x} + \frac{\partial \mathbf{f}}{\partial \mathbf{v}} \Delta \mathbf{v}
$$
Then we can solve the linear system at each time step
$$
A \Delta \mathbf{v} = \mathbf{b}
$$
where
$$
\begin{align*}
A &\equiv (I - \Delta t M^{-1} \frac{\partial \mathbf{f}}{\partial \mathbf{v}} - \Delta t^2 M^{-1} \frac{\partial \mathbf{f}}{\partial \mathbf{x}}) \\
\mathbf{b} &\equiv \Delta t M^{-1} (\mathbf{f}_n + \Delta t \frac{\partial \mathbf{f}}{\partial \mathbf{x}} \mathbf{v}_n)
\end{align*}
$$

# Lagrangian Mesh Free Methods

## Loosely Coupled Particle Systems

Particles which interact with each other depending on their spatial relationship are referred to as spatially coupled particle systems. The interactions between particles evolve dynamically over time, thus, complex geometry and topological changes can be easily modeled using this approach.

We define the potential energy $\phi_i$ for each particle $p_i$ as the sum of the pairwise potential energies $\phi_{ij}$ between $p_i$ and all other other particles $p_j$
$$
\phi_i = \sum_{j\ne i} \phi_{ij}
$$
The force is then
$$
\mathbf{f}_i = -\nabla_{\mathbf{x}_i} \phi_i = -\sum_{j\ne i} \nabla_{\mathbf{x}_i} \phi_{ij}
$$

The **Lennard_Jones potential** is
$$
\phi_{LJ}(d) = \frac{B}{d^n} - \frac{A}{d^m}
$$
The Lennard-Jones potential is well known in molecular dynamics for modeling the interaction potential between pairs of atoms. It creates long-range at- tractive and short-range repulsive forces, yielding particles arranged into hexagonally ordered 2D layers in absence of external forces. A more convenient formulation, called the Lennard-Jones bi-reciprocal function, is written as
$$
\phi(d) = \frac{-e_0}{m-n} \left( m \left( \frac{d_0}{d} \right)^n - n \left( \frac{d_0}{d} \right)^m \right)
$$
where $d_0$ is the *equilibrium separation distance*, and $−e_0$ is the minimal potential (the magnitude is called the dissociation energy). Increasing the dissociation energy increases the stiffness of the model, while with the exponents n and m the width of the potential well can be varied. Therefore, large dissociation energy and high exponents yield rigid and brittle material, while low dissociation energy and small exponents result in soft and elastic behavior of the object. This allows the modeling of a wide variety of physical properties ranging from stiff to fluid-like behavior.

## Smoothed Particle Hydrodynamics

SPH is a Lagrangian technique where discrete, smoothed particles are used to compute approximate values of needed physical quantities and their spatial derivatives. Forces can be easily derived directly from the state equations. Furthermore, as a particle-based Lagrangian approach it has the advantage that mass is trivially conserved and convection is dispensable. This reduces both the programming and computational complexity and is therefore suitable for interactive applications. A drawback of SPH is that it is not possible so far to exactly maintain the **incompressibility** of material.

<mark>Do not actually understand this part</mark>

A function $A$ is interpolated at a position $\mathbf{x}$ from its neighboring particles using a summation interpolant:
$$
A(\mathbf{x}) = \sum_j m_j \frac{A_j}{\rho_j} W(\mathbf{r}, h)
$$
where $\mathbf{r} = \mathbf{x} - \mathbf{x}_j$ and $W(\mathbf{r}, h)$ is a smoothing kernel with the properties $\int W(\mathbf{r}, h) d\mathbf{r} = 1$ and $\lim_{h\to 0} W(\mathbf{x}, h) = \delta(\mathbf{x})$, where $h$ is the support radius. For efficiency reasons, $h$ is usually chosen such that the kernel $W(\mathbf{r}, h)$ has compact support. The choice of smoothing kernel is very important. Often spline Gaussian kernels are used.

## Point-Based Animation

Elastic body forces are derived via the strain energy density
$$
U = \frac{1}{2} (\epsilon \cdot \sigma)
$$
For a Hookean material, i.e. $\sigma = E \cdot \epsilon$
$$
\mathbf{f}_i = -\nabla_{\mathbf{u}_i} U = -\frac{1}{2} \nabla_{\mathbf{u}_i} (\epsilon_i \cdot E \cdot \epsilon_i) = -\sigma \cdot \nabla_{\mathbf{u}_i} \epsilon_i
$$
where Green's nonlinear strain tensor is used for $\epsilon_i$, the spatial derivative of the displacement field $\nabla\mathbf{u}_i$ at $\mathbf{m}_i$ is needed. To guarantee zero elastic forces for rigid body modes, the approximation of $\nabla\mathbf{u}_i$ from the displacement vectors $\mathbf{u}_j$ of the neighboring particles must be at least first order accurate. Therefore, the **Moving Least Squares Method** with a linear basis is used which yields first order accurate interpolation of point-sampled functions (contrary to the SPH approximation which is not even zeroth-order accurate).

We only consider the x-component $u$ of the displacement field $\mathbf{u} = (u, v, w)^T$. The first order approximation $\tilde{u}_j$ is
$$
\tilde{u}_j = u_i + \nabla u|_{\mathbf{m}_i} \cdot \mathbf{m}_{ij}
$$
The weighted least-squares error of $e_i$ is given by
$$
e_i = \sum_j (\tilde{u}_j - u_j)^2 w_{ij}
$$
where $w_{ij}$ is a normalized, continuously defined and monotonically decreasing weighting function. We want to find the unknowns $\nabla u|_{\mathbf{m}_i}$ such that the error is minimized. Therefore, we set the derivatives of $e$ with respect to $u_{,x}\ u_{,y}\ u_{,z}$ yielding three equations for the three unknowns
$$
\left( \sum_j \mathbf{m}_{ij} \mathbf{m}_{ij}^T w_{ij} \right) \nabla u|_{\mathbf{m}_i} = \sum_j (u_j - u_i) \mathbf{m}_{ij} w_{ij}
$$
Finally
$$
\nabla u|_{\mathbf{m}_i} = \mathbf{A}^{-1} \left( \sum_j (u_j - u_i) \mathbf{m}_{ij} w_{ij} \right)
$$
where the inverse of $\mathbf{A} = \sum_j \mathbf{m}_{ij} \mathbf{m}_{ij}^T w_{ij}$ needs to be computed only once per particle and can be used for computing the derivation of $v$ and $w$ as well.

# Linear Model Analysis

Given the mass, damping and stiffness matrices $\mathbf{M}$, $\mathbf{C}$ and $\mathbf{K}$, we can decoupled the following equations
$$
\begin{equation}\label{motion}
\mathbf{M} \ddot{\mathbf{u}} + \mathbf{C} \dot{\mathbf{u}} + \mathbf{K} \mathbf{u} = \mathbf{f}_{ext}
\end{equation}
$$
into $N = 3n$ linearly independent ODEs by solving the generalized eigenvalue problem
$$
\mathbf{M} \Phi \Lambda = \mathbf{K} \Phi
$$
such that $\Phi^T \mathbf{M} \Phi = \mathbf{I}$ and $\Phi^T \mathbf{K} \Phi = \Lambda$ are diagonal matrices. The entries of $\Lambda$ contain the eigenvalues, and the columns of $\Phi$ contain the eigenvectors of $\mathbf{M}^{-1} \mathbf{K}$. $\Phi$ is often termed the *modal matrix* or *modal displacement matrix*, where the $i$-th column $\Phi_i$ represents the $i$-th mode shape, and the $i$-th entry of $\Lambda$ is proportional to the resonant frequency of the $\Phi_i$. Any displacement $\mathbf{u}(t) = \mathbf{x}(t) - \mathbf{x}_0$ can be written as a linear combination of the columns
$$
\mathbf{u}(t) = \Phi \mathbf{q}(t)
$$

Substituting into $\eqref{motion}$ and premultiplying by $\Phi^T$ yields
$$
\Phi^T \mathbf{M} \Phi \ddot{\mathbf{q}} + \Phi^T \mathbf{C} \Phi \dot{\mathbf{q}} + \Phi^T \mathbf{K} \Phi \mathbf{q} = \Phi^T \mathbf{f}_{ext}
$$
Note that for general damping, $\Phi^T \mathbf{C} \Phi$ is dense, but if we assume proportional (Raleigh) damping $\mathbf{C} = \alpha \mathbf{M} + \beta \mathbf{K}$, then it is also diagonal. With $\mathbf{g} = \Phi^T \mathbf{f}_{ext}$ we can now express it as $3n$ independent scalar 2nd ODE
$$
\ddot{q}_i + (\alpha + \beta \lambda_i) \dot{q}_i + \lambda_i q_i = g_i
$$
where $\lambda_i$ is the $i$-th diagonal element of $\Lambda$.

We can solve this equation analytically.

# Eulerian and Semi-Lagrangian Methods

Lagrangian techniques are a convenient way to define an object as a connected mesh of points or simply a cloud of points. The Eulerian point of view, on the other hand, looks at a stationary set of points and calculates how the material properties stored at those stationary grid points change over time. One of the **drawbacks** of changing to an Eulerian perspective is that the boundary of the object is no longer explicitly defined.

In Computer Graphics, fluids are often defined in an Eulerian framework.

There are two parts to the Navier-Stokes equations:
$$
\begin{align}
\nabla \cdot \mathbf{u} &= 0 \label{mass} \\
\mathbf{u}_t &= -(\mathbf{u} \cdot \nabla) \mathbf{u} + \nabla \cdot (\nu \nabla \mathbf{u}) - \nabla p + \mathbf{f} \nonumber
\end{align}
$$
These two equations represent the conservation of mass and momentum for an incompressible fluid. In this Eulerian formulation the quantities of the fluid are stored in a grid of cells. The velocity is stored on the cell faces and the pressure is stored at the center of the cells. This is commonly called a staggered (or MAC) grid. Notice that the position, $\mathbf{x}$, of the fluid is not defined; in this Eulerian frame work, the grid positions remain fixed.

Solving these two equations is usually done in several steps (breaking up a PDE into simpler pieces and solving the pieces separately is known as *operator splitting*). The $\mathbf{f}$ term is simply scaled by the time step and added to the current velocity. The advection term, $-(\mathbf{u} \cdot \nabla) \mathbf{u}$ is then solved. One popular way to solve the advection term is with the *semi-Lagrangian* technique because it is stable for large time step sizes.

After advection, we have what can be called a *best guess velocity*
$$
\tilde{\mathbf{u}} = \mathbf{u} + \Delta t (- (\mathbf{u} \cdot \nabla) \mathbf{u} + \nabla \cdot (\nu \nabla \mathbf{u}) + \mathbf{f})
$$
which does not take into account pressure or mass conservation. The final step, *pressure-projection*, is in fact one of the reasons that Eulerian techniques are popular for fluids. The trick of the pressure-projection step is to solve for a pressure such that subtracting that pressure’s gradient from the best guess velocity will give a final velocity that conserves mass.
$$
\begin{equation}\label{unew}
\mathbf{u}^\text{new} = \tilde{\mathbf{u}} - \Delta t \nabla p
\end{equation}
$$
substituting it in $\eqref{mass}$ and rearrange to get Poisson equation
$$
\Delta t \nabla^2 p = \nabla \cdot \tilde{\mathbf{u}}
$$
with which we can solve for $p$. Then plug it back $\eqref{unew}$ to find our final velocity. The final velocity is incompressible (divergence free), and for a constant density fluid this ensures mass conservation. Compressible fluids can also conserve mass, but their density must change to do so.
