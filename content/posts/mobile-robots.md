---
title: Mobile Robots
date: 2020-03-19
lastmod: 2025-05-05
categories:
- Study
tags:
- UW
- CS
---

[CSE 490R](https://courses.cs.washington.edu/courses/cse490r/19sp/) Course Review

My [solution](https://github.com/silencial/Mobile-Robots) to the homework

[Introduction to Mobile Robotics](http://ais.informatik.uni-freiburg.de/teaching/ss13/robotics/) and [Probabilistic Robotics](http://www.probabilistic-robotics.org/) are good references

<!--more-->

---

# Lab0

- Localization: Determine the pose of a robot relative to a given map of the environment.
- Mapping: Construct a map.
- Planning: Plan a trajectory to goal based on localization and maps.

## Bayes Filter

1. prediction: push belief through dynamics given action
2. correction: apply Bayes rule given measurement

$$
\begin{align*}
\overline{bel}\left(x_{t}\right) &= \int p\left(x_{t} | u_{t}, x_{t-1}\right) bel\left(x_{t-1}\right) d x_{t-1} \\
bel\left(x_{t}\right) &= \eta p\left(z_{t} | x_{t}\right) \overline{bel}\left(x_{t}\right)
\end{align*}
$$

## Kalman Filter

**Assumptions**:

1. Linear dynamics $p(x_t | u_t, x_{t-1}) = A_{t} x_{t-1}+B_{t} u_{t}+\varepsilon_{t}$, where $\varepsilon_t \sim \mathcal{N}(0, R_t)$
2. Linear measurement model $p(z_t | x_t) = C_{t} x_{t}+\delta_{t}$, where $\delta_t \sim \mathcal{N}(0, Q_t)$
3. Initial belief $bel(x_0)$ is a normal distribution:

$$
bel\left(x_{0}\right)=p\left(x_{0}\right)=\det\left(2 \pi \Sigma_{0}\right)^{-\frac{1}{2}} \exp \left\{-\frac{1}{2}\left(x_{0}-\mu_{0}\right)^{T} \Sigma_{0}^{-1}\left(x_{0}-\mu_{0}\right)\right\}
$$

**Algorithm**:

$$
\begin{align*}
&\textbf{Algorithm KalmanFilter} (\mu_{t-1}, \Sigma_{t-1}, u_t, z_t): \\
&\bar{\mu}_{t} =A_{t} \mu_{t-1}+B_{t} u_{t} \\
&\bar{\Sigma}_{t} =A_{t} \Sigma_{t-1} A_{t}^{T}+R_{t} \\
&K_{t} =\bar{\Sigma}_{t} C_{t-1}^{T}\left(C_{t} \bar{\Sigma}_{t} C_{t}^{T}+Q_{t}\right)^{-1} \\
&\mu_{t} =\bar{\mu}_{t}+K_{t}\left(z_{t}-C_{t} \bar{\mu}_{t}\right) \\
&\Sigma_{t} =\left(I-K_{t} C_{t}\right) \bar{\Sigma}_{t} \\
&\text{return } \mu_{t}, \Sigma_{t}
\end{align*}
$$

# Lab1

## Motion Model

- Kinematic model: maps wheel speeds to robot velocities.
- Dynamic model: maps wheel torques to robot accelerations.

Let's focus on the kinematic model $p(x_t | u_t, x_{t-1})$ for now (assume we can set the speed directly), then the state is $x = [x, y, \theta]^T$ and the control input is $u = [V, \delta]^T$, where $\theta$ is the heading and $\delta$ is the steering angle.

Now derive the motion model for the rear axle. Note that a rigid body undergoing both rotation and translation can be viewed as undergoing pure rotation about an instant center of rotation:

![Car Kinematic model](https://i.imgur.com/TNXGKde.png)

$$
\begin{align*}
&\dot{x}=V \cos (\theta)\\
&\dot{y}=V \sin (\theta)\\
&\dot{\theta}=\omega=\frac{V \tan \delta}{L}
\end{align*}
$$

Using numerical integration, we get:

$$
\begin{align*}
x_{t+1} &=x_{t}+\frac{L}{\tan (\delta)}\left(\sin \left(\theta_{t+1}\right)-\sin \left(\theta_{t}\right)\right) \\
y_{t+1} &=y_{t}+\frac{L}{\tan (\delta)}\left(-\cos \left(\theta_{t+1}\right)+\cos \left(\theta_{t}\right)\right) \\
\theta_{t+1} &=\theta_{t}+\frac{v}{L} \tan (\delta) \Delta t \quad
\end{align*}
$$

### Noise

1. Control signal error: $\hat{V} \sim \mathcal{N}(V, \sigma_v^2)$, $\hat{\delta} \sim \mathcal{N}(\delta, \sigma_\delta^2)$
2. Model inaccuracies: $\hat{x} \sim \mathcal{N}\left(x, \sigma_{x}^{2}\right)$, $\hat{y} \sim \mathcal{N}\left(y, \sigma_{y}^{2}\right)$, $\hat{\theta} \sim \mathcal{N}\left(\theta, \sigma_{\theta}^{2}\right)$

## Sensor Model

$p(z_t | x_t, m)$ is the probability of sensor reading $z_t$ given state $x_t$ and map $m$. We calculate a simulated sensor reading $z^*$ from $x$ and $m$ and then compare it with the actual reading $z$.

Assume individual beams are conditionally independent given the map ==(Note: this may lead to overconfidence)==:

$$
p\left(z_{t} | x_{t}, m\right)=\prod_{i=1}^{K} p\left(z_{t}^{k} | x_{t}, m\right)
$$

### Noise

1. Simple measurement noise:

    $$
    \require{mathtools}
    p_{\text {hit}}\left(z_{t}^{k} | x_{t}, m\right)=\begin{dcases}
    \eta \mathcal{N}\left(z_{t}^{k} ; z_{t}^{k *}, \sigma_{\text {hit}}^{2}\right) & \text {if } 0 \le z_{t}^{k} \le z_{\max} \\
    0 & \text { otherwise }
    \end{dcases}
    $$

2. Presence of unexpected objects

    $$
    p_{\text {short}}\left(z_{t}^{k} | x_{t}, m\right)=\begin{dcases}
    \eta \lambda_{\text {short}} e^{-\lambda_{\text {short}} z_{t}^{k}} & \text {if } 0 \le z_{t}^{k} \le z_{t}^{k *} \\
    0 & \text {otherwise }
    \end{dcases}
    $$

3. Laser returns max range when no objects

    $$
    p_{\max}\left(z_{t}^{k} | x_{t}, m\right)=I\left(z=z_{\max}\right)=\begin{cases}
    1 & \text {if } z=z_{\max } \\
    0 & \text {otherwise }
    \end{cases}
    $$

4. Failures in sensing

    $$
    p_{\text {rand}}\left(z_{t}^{k} | x_{t}, m\right)=\begin{dcases}
    \frac{1}{z_{\max}} & \text {if } 0 \le z_{t}^{k} < z_{\max } \\
    0 & \text {otherwise}
    \end{dcases}
    $$

Combining these four models gives us:

$$
p\left(z_{t}^{k} | x_{t}, m\right)=\begin{pmatrix}
z_{\mathrm{hit}} \\
z_{\text {short}} \\
z_{\text {max}} \\
z_{\text {rand}}
\end{pmatrix}^{T} \begin{pmatrix}
p_{\text {hit}}(z_{t}^{k} | x_{t}, m) \\
p_{\text {short}}(z_{t}^{k} | x_{t}, m) \\
p_{\text {max}}(z_{t}^{k} | x_{t}, m) \\
p_{\text {rand}}(z_{t}^{k} | x_{t}, m)
\end{pmatrix}
$$

## Particle Filter

The particle filter is an alternative, non-parametric implementation of the Bayes filter. The key idea is to approximate the posterior $bel(x_t)$ by a set of random state samples (particles) drawn from it.

$$
\begin{align*}
&\textbf{Algorithm ParticleFilter} (\mathcal{X}_{t-1}, u_t, z_t ): \\
&\bar{\mathcal{X}}_{t}=\mathcal{X}_{t}=\varnothing \\
&\text{for } m=1 \text{ to } M \text{ do} \\
&\qquad \text{sample } x_{t}^{[m]} \sim p\left(x_{t} | u_{t}, x_{t-1}^{[m]}\right) \\
&\qquad {w_{t}^{[m]}=p\left(z_{t} | x_{t}^{[m]}\right)} \\
&\qquad {\bar{\mathcal{X}}_{t}=\bar{\mathcal{X}}_{t}+\left\langle x_{t}^{[m]}, w_{t}^{[m]}\right\rangle} \\
&\text{endfor } \\
&\text{for } m = 1 \text{ to } M \text{ do} \\
&\qquad \text{draw } i \text{ with probability} \propto w_{t}^{[i]} \\
&\qquad \text{add } x_t^{[i]} \text{ to } \mathcal{X}_t \\
&\text{endfor} \\
&\text{return } \mathcal{X}_t
\end{align*}
$$

We first sample $x_t$ from the state transition distribution, then calculate the **importance factor** $w_t$. In the next loop, we perform resampling (more specifically importance sampling) to change the distribution of particles from $\overline{bel}(x_t)$ to $bel(x_t)$.

### Resample

Resampling can lead to a high-variance (low-entropy) problem, where particles are depleted. Possible fixes:

1. If the variance of weights is low, don't resample.
2. Use low-variance sampling.

$$
\begin{align*}
&\textbf{Algorithm LowVarianceSampler} (\mathcal{X}_{t}, \mathcal{W}_{t}): \\
&\bar{X}_{t}=\varnothing \\
&r=\operatorname{rand}\left(0 ; M^{-1}\right) \\
&c=w_{t}^{[1]} \\
&i=1 \\
&\text{for } m=1 \text{ to } M \text{ do} \\
&\qquad U=r+(m-1) \cdot M^{-1} \\
&\qquad \text{while } U > c \\
&\qquad\quad i=i+1 \\
&\qquad\quad c=c+w_{t}^{[i]} \\
&\qquad \text{endwhile} \\
&\qquad \text{add } x_{t}^{[i]} \text{ to } \bar{X}_{t} \\
&\text{endfor } \\
&\text{return } \bar{\mathcal{X}}_t
\end{align*}
$$

![Low-Variance Sampling](https://i.imgur.com/wE1c9rd.png)

### Expected Pose

Calculate the expected pose from particles: $x$ and $y$ can be computed directly using the weighted average. However, the weighted average of $\theta$ is not accurate. Thus, we use [cosine and sine averaging](https://en.wikipedia.org/wiki/Mean_of_circular_quantities).

## Code

`MotionModel.py`:

1. Subscribes to the motor topic to get control info (speed and steering angle). Also saves last frame info for calculations.
2. Adds variance to speed and steering angle. Applies the motion model to particles. Adds variance to states.

`SensorModel.py`:

1. Precomputes the sensor model table and uses the [range_libc](https://github.com/kctess5/range_libc) package to perform 2D ray casting on a 2D occupancy grid.
2. Subscribes to the scan topic, filters out invalid and extreme scan values, downsamples, and passes processed data to update weights.

`ParticleFilter.py`:

1. Transfers the map to an occupancy grid.
2. Globally initializes particles and weights. Initializes the motion model and sensor model.
3. Subscribes to the `/initialpose` topic, initializes particles and weights around the initial pose.
4. When receiving scan data, updates weights, resamples, and calculates the expected pose for visualization.

# Lab2

|                                   |  Uses Model   |  Stability Guarantee   | Minimize Cost |
| :-------------------------------: | :-----------: | :--------------------: | :-----------: |
|              PID              |      No       |           No           |      No       |
|  Pure Pursuit   | Circular arcs | Yes - with assumptions |      No       |
| Lyapunov |  Non-linear   |          Yes           |      No       |
|              LQR              |    Linear     |          Yes           |   Quadratic   |
|             iLQR              |  Non-linear   |          Yes           |      Yes      |

## Control

![Coordinate Transformation](https://i.imgur.com/LVolGiu.png)

Position error in frame A:

$$
{}^{A}e = \begin{bmatrix}
x \\ y
\end{bmatrix} - \begin{bmatrix}
x_{ref} \\ y_{ref}
\end{bmatrix}
$$

We want the position error in frame B so that the $x$ and $y$ components correspond to along-track and cross-track errors, respectively:

$$
\begin{split}
{}^{B}e &= {}_A^{B} R {}^{A}e \\
&=R(-\theta_{ref}) \left( \begin{bmatrix}
x \\ y
\end{bmatrix} - \begin{bmatrix}
x_{ref} \\ y_{ref}
\end{bmatrix} \right) \\
&= \begin{bmatrix}
\cos \left(\theta_{r e f}\right)\left(x-x_{r e f}\right)+\sin \left(\theta_{r e f}\right)\left(y-y_{r e f}\right) \\
-\sin \left(\theta_{r e f}\right)\left(x-x_{r e f}\right)+\cos \left(\theta_{r e f}\right)\left(y-y_{r e f}\right)
\end{bmatrix} \\
&= \begin{bmatrix}
e_{at} & e_{ct}
\end{bmatrix}^T
\end{split}
$$

For now, we only consider the cross-track error $e_{ct}$ and controlling the steering angle.

## PID

$$
u=-\left(K_{p} e_{c t}+K_{i} \int e_{c t}(t) d t+K_{d} \dot{e}_{c t}\right)
$$

where

- $K_p$: Proportional coefficient
- $K_i$: Integral coefficient
- $K_d$: Derivative coefficient

Analytically compute the derivative term:

$$
\begin{split}
\dot{e}_{c t} &=-\sin \left(\theta_{ref}\right) \dot{x}+\cos \left(\theta_{ref}\right) \dot{y} \\
&=-\sin \left(\theta_{ref}\right) V \cos (\theta)+\cos \left(\theta_{ref}\right) V \sin (\theta) \\
&=V \sin \left(\theta-\theta_{ref}\right) \\
&=V \sin \left(\theta_{e}\right)
\end{split}
$$

## Pure-Pursuit

![Pure-pursuit Arc](https://i.imgur.com/3UaQVkE.png)

**Key idea**: The car follows a circular arc.

1. Find a lookahead point and compute the arc
2. Move along the arc
3. Go to step 1

Solve for arc:

$$
\begin{gather*}
\alpha =\tan ^{-1}\left(\frac{y_{ref}-y}{x_{ref}-x}\right)-\theta \\
R = \frac{L}{2 \sin\alpha} \\
\dot{\theta} = \frac{V}{R} = \frac{2V\sin\alpha}{L} \\
\dot{\theta} = \frac{V\tan u}{B}
\end{gather*}
$$

where $B$ is the wheelbase. Solving for $u$:

$$
u = \tan^{-1}\left( \frac{2B \sin \alpha}{L} \right)
$$

## Lyapunov

Define Lyapunov function:

$$
V\left(e_{ct}, \theta_{e}\right)=\frac{1}{2} k_{1} e_{ct}^{2}+\frac{1}{2} \theta_{e}^{2}
$$

Compute the derivative:

$$
\begin{split}
\dot{V}\left(e_{c t}, \theta_{e}\right)&=k_{1} e_{c t} \dot{e}_{c t}+\theta_{e} \dot{\theta}_{e}\\
&=k_{1} e_{c t} V \sin \theta_{e}+\theta_{e} \frac{V}{B} \tan u
\end{split}
$$

Set $u$ to ensure $\dot{V} < 0$:

$$
\begin{gather*}
\theta_{e} \frac{V}{B} \tan u=-k_{1} e_{c t} V \sin \theta_{e}-k_{2} \theta_{e}^{2} \\
\tan u=-\frac{k_{1} e_{c t} B}{\theta_{e}} \sin \theta_{e}-\frac{B}{V} k_{2} \theta_{e} \\
u=\tan ^{-1}\left(-\frac{k_{1} e_{c t} B}{\theta_{e}} \sin \theta_{e}-\frac{B}{V} k_{2} \theta_{e}\right)
\end{gather*}
$$

## LQR

Turn the problem into an optimization problem to trade off both driving error and keeping control action small:

$$
\min _{u(t)} \int_{0}^{\infty}\left(w_{1} e(t)^{2}+w_{2} u(t)^{2} \right) dt
$$

Given:

1. Linear dynamic system

$$
x_{t+1} = Ax_t + Bu_t
$$

2. Quadratic cost

$$
J=\sum_{t=0}^{T-1} x_{t}^{T} Q x_{t}+u_{t}^{T} R u_{t}
$$

The optimal control sequence minimizing the cost is:

$$
\begin{gather*}
u_t = K_t x_t \\
K_{t}=-\left(R+B^{T} V_{t+1} B\right)^{-1} B^{T} V_{t+1} A \\
V_{t}=Q+K_{t}^{T} R K_{t}+\left(A+B K_{t}\right)^{T} V_{t+1}\left(A+B K_{t}\right)
\end{gather*}
$$

## MPC

1. Plans a sequence of control actions
2. Predicts the set of next states up to a horizon H
3. Evaluates the cost/constraint of the states and controls
4. Optimizes the cost

## Code

`controlnode.py`: main script

1. Defines Subscribers to pose and path info; Publishers for visualization and Services for reset
2. Enters the main loop when the initial pose is set and the controller is ready. Gets the current pose and reference pose, calculates the next control command, and publishes it to the `/vesc/high_level/ackermann_cmd_mux/input/nav_0` topic.
3. Stops when the path is completed.

`runner_script.py`:

1. Loads different paths and speeds.
2. Starts the controller.

`controller.py`: base controller class

1. Stores path info.
2. Defines utility functions: get the reference pose by index, get pose and pose_ref error.

==Note: The method for finding the reference pose is the same across all controllers: it finds the nearest point on the path and looks ahead a certain distance.==

`pid.py`:

1. Uses the PD equation to get the next control (steering angle).

`purepursuit.py`:

1. Uses the Pure pursuit equation to get the next control.

`mpc.py`:

1. Divides the steering angle range $[-\pi, \pi]$ equally for $K$ rollouts.
2. Executes each steering angle through $T$ timesteps to collect $K \times T$ poses.
3. Evaluates the cost of each rollout based on collision cost and error cost.
    1. Collision cost: Adds a large cost if any pose in a trajectory collides with the map.
    2. Error cost: The norm of the distance between the last pose and the reference pose, weighted by a constant.
4. Chooses the rollout with the minimal cost and executes the first step.
5. Returns to step 1.

`mpc2.py`: Similar to `mpc.py`, but uses scan data rather than the map. The only difference is how the obstacle cost is calculated.

1. Calculates $N$ obstacle poses in the map from scan data.
2. For the $K \times T$ poses, calculates the distance to every obstacle to get a $K \times T \times N$ array.
3. Finds the minimal distance for every pose to get a $K \times T$ array.
4. Averages across timesteps to get a $K$ array, then weights it by a constant to get the obstacle cost.

`nonlinear.py`:

1. Uses the Lyapunov control equation to get the control.

# Lab3

Steps for planning a path from one point to another:

1. Randomly samples points on the map and constructs the graph.
2. Uses a planning algorithm to search the graph for an optimal path.

## Graph Construction

The constructed graph is called a random geometric graph

1. Samples a set of collision-free vertices $V$.
2. Connects neighboring vertices to get edges $E$.

### Sampling

Random sampling tends to clump points together. We want points to be spread out evenly, which can be achieved using a Halton sequence.

### Optimal Radius

We want to choose a radius small enough for efficiency while ensuring connectivity. The optimal radius can be chosen as:

$$
r = \left(\frac{\ln|V|}{\alpha_{p,d} |V|}\right)^{1/n}
$$

where $\alpha_{p,d}$ is a constant. For the special case of a two-dimensional space and the 2-norm ($d=2$, $p=2$), $\alpha_{p,d} = \pi$.

### Dubins Path

Since we are considering 2D car dynamics, we need to connect two points with a feasible path instead of straight lines. Mathematically, this requires solving the boundary value problem:

$$
\dot{q}(t) = f(q(t), u(t)) \\
q(0) = q_1,\quad q(t) = q_2
$$

where $q=(x,y,\theta)$ in our case.

Dubins path theory shows that a solution always exists and must belong to one of 6 classes:

$$
\{LRL, RLR, LSL, LSR, RSL, RSR\}
$$

where $L,R,S$ represent turn left, right, straight, respectively.

## Planning Algorithm

Given a start node $s_0$, a goal node $s_1$, and a cost function $c(s, s')$, create objects:

1. OPEN: priority queue of nodes to be processed
2. CLOSED: list of nodes already processed
3. $g(s)$: estimate of the least cost from start to a given node

The pseudocode for the best-first search can be expressed as:

1. Push $s_0$ into OPEN.
2. While $s_1$ not expanded:
    1. Pop *best* from OPEN based on $f(s)$
    2. Add *best* to CLOSED.
    3. For every successor *s'*:
        1. If $g(s') > g(s) + c(s, s')$:
            1. $g(s') = g(s) + c(s, s')$
            2. Add (update) $s'$ to OPEN.

The main challenge is how to choose the heuristic function $f(s)$.

### Dijkstra's Algorithm

Choose $f(s) = g(s)$. Always pops the node with the smallest cost from the origin first.

### A*

If we can pre-evaluate the estimated cost from the node to the goal $h(s)$, we can choose a better heuristic $f(s) = g(s) + h(s)$.

- If $h(s)$ is admissible $h(s) \le h^*(s)$, $h(\text{goal}) = 0$, then the path returned by A* is optimal.
- If $h(s)$ is consistent $h(s) \le c(s, s') + h(s')$, $h(\text{goal}) = 0$, then A* is optimal and efficient (will not re-expand a node).

All consistent heuristics are admissible, not vice versa.

### Weighted A*

Choose $f(s) = g(s) + \epsilon h(s)$, where $\epsilon > 1$. It is more efficient and the solution is $\epsilon$-optimal $c \le \epsilon c^*$.

### Lazy A*

Instead of checking edge collisions for all neighbors, only checks the edge to the parent when expanding. ==Note: The OPEN list may contain multiple copies of a node because collision checks are deferred.==

### Shortcut

After a path is found, we can randomly pick two nodes on the path and connect them directly if the resulting edge is collision-free.

## Code

`run.py`: main program

1. Loads map info.
2. Constructs the graph given the environment, sampler, number of vertices, and connection radius.
3. Adds start and end nodes.
4. Uses A\* or Lazy A\* algorithm to search for the optimal path and visualizes it.

`MapEnvironment.py`: defines utility functions associated with planning

1. Checks edge collisions.
2. Computes heuristic and distance functions.
3. Generates a path on the map.
4. Visualizes the graph and path.

`Sampler.py`: creates random samples for graph construction

1. Uses [Halton sequence](https://gist.github.com/tupui/cea0a91cc127ea3890ac0f002f887bae) to generate 2D random vertices in $(0,1)$.
2. Scales samples based on map information.

`graph_maker.py`: constructs the graph

1. Uses the [NetworkX](https://networkx.github.io/) Python package to easily construct a graph.
2. Adds sampled valid vertices.
3. Connects edges within the radius and without collision (collision checks are skipped if using Lazy A\*).

`astar.py`: Implements the A\* algorithm.

1. Uses the `heapq` package to create a priority queue storing `[f(s), count, node, g(s), parent]` info. `count` is used to break ties when $f(s)$ values are equal.
2. Uses a dictionary `enqueued` to store $g(s)$ and $h(s)$ for nodes currently in the queue, and another dictionary `explored` to store nodes that have been explored and their parent node.
3. Adds the start node to the queue.
4. While the queue is not empty, pops the node with the highest priority and adds it to `explored` if it hasn't been explored already.
5. For all neighbor nodes $s'$, computes $g(s') = g(s) + c(s,s')$ if $s'$ has not been explored.
6. If $s'$ is already in `enqueued`, retrieves its previous $g'(s')$ and $h'(s')$ values. Continues to the next neighbor if $g'(s') \leq g(s')$. If $s'$ is not in `enqueued`, computes $h(s')$ using the heuristic function.
7. Updates $g(s')$ and $h(s')$ in `enqueued` and pushes $s'$ to the queue.
8. If the target node is reached, reconstructs the path by following parent pointers in `explored` and returns the path.

`lazy_astar.py`: Implements the Lazy A\* algorithm.

1. When expanding a node, checks its edge collision with the parent.
2. When checking neighbors, it does not need to compare $g'$ with $g$.
3. Multiple entries for the same node, potentially with different parents, can be added to the queue.

`runDubins.py`: Similar to `run.py`, but uses the Dubins environment.

`DubinsMapEnvironment.py`: Inherits from `MapEnvironment`.

1. Computes distances using Dubins paths.
2. Computes the heuristic using Dubins paths.
3. Generates paths using Dubins path planning.

`DubinsSampler.py`:

1. Samples $N$ vertices the same way as `Sampler.py`.
2. Divides angles into $M$ parts, adds angles to each vertex to create $M \times N$ samples.

`Dubins.py`: Provides utility functions for Dubins paths.

`ROSPlanner.py`: Takes goals from RViz and performs planning. ==Note: Be mindful of coordinate transformations between the map and world.==

1. Loads map info.
2. Constructs the graph and saves it for later use.
3. Subscribes to the pose topic and saves the current pose.
4. Subscribes to the goal topic, plans from the current pose to the goal, and publishes the path using the service defined in #Lab2.
5. If there are multiple goals, plans sequentially through them and combines the paths.
