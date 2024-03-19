---
title: Mobile Robots
date: 2020-03-19
lastmod: 2020-03-20
categories:
- Study
tags:
- UW
- CS
---

[CSE 490R](https://courses.cs.washington.edu/courses/cse490r/19sp/) Review

My [solution](https://github.com/silencial/Mobile-Robots) to the homework

<!--more-->

---

# Lab0

- **Localization**: Determine the pose of a robot relative to a given map of the environment.
- **Mapping**: Construct a map.
- **Planning**: Plan a trajectory to goal based on localization and maps.

## Bayes Filter

The **Bayes filter algorithm** includes two steps, the first step is prediction (push belief through dynamics given action), the second step is correction (apply Bayes rule given measurement).

$$
\begin{gather*}
\overline{bel}\left(x_{t}\right) = \int p\left(x_{t} | u_{t}, x_{t-1}\right) bel\left(x_{t-1}\right) d x_{t-1} \\
bel\left(x_{t}\right) = \eta p\left(z_{t} | x_{t}\right) \overline{bel}\left(x_{t}\right)
\end{gather*}
$$

## Kalman Filter

**Assumptions**:

1. Linear dynamics $p(x_t | u_t, x_{t-1}) = A_{t} x_{t-1}+B_{t} u_{t}+\varepsilon_{t}$, where $\varepsilon_t \sim \mathcal{N}(0, R_t)$
2. Linear measurement model $p(z_t | x_t) = C_{t} x_{t}+\delta_{t}$, where $\delta_t \sim \mathcal{N}(0, Q_t)$
3. Initial belief $bel(x_0)$ is a Gaussian distribution
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

- **Kinematic model**: map wheel speeds to robot velocities.
- **Dynamic model**: map wheel torques to robot accelerations.

Consider only kinematic model $p(x_t | u_t, x_{t-1})$ for now (assume we can set the speed directly), then $x = [x, y, \theta]^T$, $u = [V, \delta]^T$, where $\theta$ is the heading and $\delta$ is the steering angle.

Now we will derive the motion model for rear axel. Note that a rigid body undergoing rotation and translation can be viewed as pure rotation about a instant center of rotation:

![Car Kinematic model](https://i.imgur.com/TNXGKde.png)

$$
\begin{align*}
&\dot{x}=V \cos (\theta)\\
&\dot{y}=V \sin (\theta)\\
&\dot{\theta}=\omega=\frac{V \tan \delta}{L}
\end{align*}
$$

With numerical integration we can get
$$
\begin{align*}
x_{t+1} &=x_{t}+\frac{L}{\tan (\delta)}\left(\sin \left(\theta_{t+1}\right)-\sin \left(\theta_{t}\right)\right) \\
y_{t+1} &=y_{t}+\frac{L}{\tan (\delta)}\left(-\cos \left(\theta_{t+1}\right)+\cos \left(\theta_{t}\right)\right) \\
\theta_{t+1} &=\theta_{t}+\frac{v}{L} \tan (\delta) \Delta t \quad
\end{align*}
$$

### Noise

1. Control signal error: $\hat{V} \sim \mathcal{N}(V, \sigma_v^2)$, $\hat{\delta} \sim \mathcal{N}(\delta, \sigma_\delta^2)$
2. Incorrect physics (the Kinematic Model is inaccurate): $\hat{x} \sim \mathcal{N}\left(x, \sigma_{x}^{2}\right)$, $\hat{y} \sim \mathcal{N}\left(y, \sigma_{y}^{2}\right)$, $\hat{\theta} \sim \mathcal{N}\left(\theta, \sigma_{\theta}^{2}\right)$

## Sensor Model

$p(z_t | x_t, m)$ is the probability of sensor reading $z_t$ given state $x_t$ and map $m$. Calculate simulated sensor reading $z^*$ form $x$ and $m$ and then compare with $z$.

Assume individual beams are conditionally independent given map <mark>(may result in overconfidence problem)</mark>:
$$
p\left(z_{t} | x_{t}, m\right)=\prod_{i=1}^{K} p\left(z_{t}^{k} | x_{t}, m\right)
$$

### Noise

1. Simple measurement noise in distance value
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
    \frac{1}{z_{\max}} & \text {if } 0 \le z_{t}^{k}<z_{\max } \\
    0 & \text {otherwise}
    \end{dcases}
    $$

Combine these 4 model to get
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

The particle filter is an alternative **nonparametric** implementation of the Bayes filter. The key idea is to represent the posterior $bel(x_t)$ by a set of random state samples (particles) drawn from this posterior.
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
&\qquad \text{draw } i \text{ with probability } \propto w_{t}^{[i]} \\
&\qquad \text{add } x_t^{[i]} \text{ to } \mathcal{X}_t \\
&\text{endfor} \\
&\text{return } \mathcal{X}_t
\end{align*}
$$

We first sample $x_t$ from the state transition distribution, then calculate the **importance factor**, $w_t$. In the next loop, we do the **resampling**/**importance sampling**, to change the distribution of particles from $\overline{bel}(x_t)$ to $bel(x_t)$.

### Resample

Resampling can cause high-variance (low-entropy) problem, where particles are depleted. Possible fixes:

1. If the variance of weights low, don't resample.
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

Calculate expected pose from particles: $x$ and $y$ can be computed directly by the weighted average. However, the weighted average of $\theta$ is not accurate. Thus we use cosine and sine averaging. ([Ref](https://en.wikipedia.org/wiki/Mean_of_circular_quantities))

## Code

`MotionModel.py`:

1. Subscribe to motor topic, get control info (speed and steering angle), also save last frame info for calculation.
2. Add variance to speed and steering angle. Apply motion model on particles. Add variance to states.

`SensorModle.py`:

1. Precompute the sensor model table and use [range_libc](https://github.com/kctess5/range_libc) package to achieve 2D raycasting for 2D occupancy grid.
2. Subscribe to scan topic, filter out invalid and extreme scan values, downsample, and pass processed data to update weights.

`ParticleFilter.py`:

1. Transfer map to occupancy grid.
2. Globally initialize particles and weights. Initialize motion model and sensor model.
3. Subscribe to `/intialpose` topic, initialize particles and weights around initial pos.
4. When receiving scan infos, update weights, resample and calculate the expected pose for visualization.

# Lab2

|              |  Uses Model   |  Stability Guarantee   | Minimize Cost |
| :----------: | :-----------: | :--------------------: | :-----------: |
|     PID      |      No       |           No           |      No       |
| Pure Pursuit | Circular arcs | Yes - with assumptions |      No       |
|   Lyapunov   |  Non-linear   |          Yes           |      No       |
|     LQR      |    Linear     |          Yes           |   Quadratic   |
|     iLQR     |  Non-linear   |          Yes           |      Yes      |

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

We want position error in frame B so that $x$ and $y$ error correspond to along-track and cross-track error respectively:
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

Only consider cross-track error $e_{ct}$ and control steering angle for now.

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

**Key idea**: The car is always moving in a circular arc.

1. Find a lookahead and compute arc
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

where $B$ is the car length. Solve for $u$ we can get
$$
u = \tan^{-1}\left( \frac{2B \sin \alpha}{L} \right)
$$

![Pure-pursuit Arc](https://i.imgur.com/3UaQVkE.png)

## Lyapunov Control

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

Set $u$ to get $\dot{V} < 0$:
$$
\begin{gather*}
\theta_{e} \frac{V}{B} \tan u=-k_{1} e_{c t} V \sin \theta_{e}-k_{2} \theta_{e}^{2} \\
\tan u=-\frac{k_{1} e_{c t} B}{\theta_{e}} \sin \theta_{e}-\frac{B}{V} k_{2} \theta_{e} \\
u=\tan ^{-1}\left(-\frac{k_{1} e_{c t} B}{\theta_{e}} \sin \theta_{e}-\frac{B}{V} k_{2} \theta_{e}\right)
\end{gather*}
$$

## LQR

Turn the problem into an optimization to trade-off both driving error and keeping control action small:
$$
\min _{u(t)} \int_{0}^{\infty}\left(w_{1} e(t)^{2}+w_{2} u(t)^{2} \right) dt
$$

Given

1. **Linear** dynamic system
$$
x_{t+1} = Ax_t + Bu_t
$$
2. **Quadratic** cost
$$
J=\sum_{t=0}^{T-1} x_{t}^{T} Q x_{t}+u_{t}^{T} R u_{t}
$$

The optimal control sequence minimizing the cost is
$$
\begin{gather*}
u_t = K_t x_t \\
K_{t}=-\left(R+B^{T} V_{t+1} B\right)^{-1} B^{T} V_{t+1} A \\
V_{t}=Q+K_{t}^{T} R K_{t}+\left(A+B K_{t}\right)^{T} V_{t+1}\left(A+B K_{t}\right)
\end{gather*}
$$

## MPC

1. Plan a sequence of control actions
2. Predict the set of next states to a horizon H
3. Evaluate the cost/constraint of the states and controls
4. Optimize the cost

## Code

`controlnode.py`: main script

1. Define Subscribers to pose and path infos; Publishers for visualization and Services for reset
2. Enter main program when initial pose is set and controller is ready. Get pose and reference pose, get next control and publish to `/vesc/high_level/ackermann_cmd_mux/input/nav_0` topic.
3. Stop when path is completed

`runner_script.py`:

1. Loading different paths and speed
2. Start the controller

`controller.py`: base controller class

1. Store path info
2. Define utility functions: get reference pose by index, get pose and pose_ref error

<mark>Find reference pose function is the same for all controllers: Find the nearest point on the path and lookahead some distance.</mark>

`pid.py`:

1. Use PD equation to get next control (steering angle)

`purepursuit.py`:

1. Use Purepursuit equation to get next control

`mpc.py`:

1. Dividing steering angle $[-\pi, \pi]$ equally to $K$ rollouts
2. Execute each steering angel through $T$ timesteps to collect $K * T$ poses
3. Evaluate the cost of each rollout by collision cost and error cost
   1. Collision cost: if any pose in a trajectory is in collision with the map, add a large cost
   2. Error cost: norm of the distance between last pose and the reference pose, weighted by a constant
4. Choose the rollout with the minimal cost and execute the first step
5. Return to step 1

`mpc2.py`: Similar to mpc but use scan info rather than map. The only thing change is the obstacles cost.

1. Calculate $N$ obstacles pose in the map by scan info
2. For $K*T$ poses, calculate the distance with every obstacles to get $K*T*N$ array
3. Find the minimal distance for every pose to get $K * T$ array
4. Average through timesteps to get $K$ array, then weighted by a constant to get the obstacles cost.

`nonlinear.py`:

1. Use Lyapunov control equation to get the control

# Lab3

Steps for planing the path from one point to another:

1. Random sample points on the map and construct the graph
2. Use planning algorithm (A*) to search the graph for optimal path

## Graph Construction

The graph we are constructing is called [Random geometric graph](https://en.wikipedia.org/wiki/Random_geometric_graph) (RGG)

1. Sample a set of collision free vertices $V$
2. Connect neighboring vertices to get edges $E$

### Sampling

Uniform random sampling tends to clump. We want points to be spread out evenly, which can be achieved by [Halton sequence](https://observablehq.com/@jrus/halton)

### Optimal Radius

We want to choose the radius smaller enough for efficiency while ensuring connectivity. The optimal value can be chosen as
$$
r = \left(\frac{\ln|V|}{\alpha_{p,d} |V|}\right)^{1/n}
$$
where $\alpha_{p,d}$ is a constant. For special case of a two-dimensional space and the euclidean norm ($d=2$, $p=2$), $\alpha_{p,d} = \pi$

### Dubins Path

Since we are considering 2-D car dynamics, we need to connect two points with feasible path instead of straight lines. Mathematically, we need to solve the BVP:
$$
\dot{q}(t) = f(q(t), u(t)) \\
q(0) = q_1,\quad q(t) = q_2
$$
where $q=(x,y,\theta)$ in our case.

Dubins path shows that the solution always exists and has to be one of 6 classes:
$$
\{LRL, RLR, LSL, LSR, RSL, RSR\}
$$
where $L,R,S$ represent turn left, right, straight, respectively.

## Planning Algorithm

Given start node $s_0$, goal $s_1$ and cost $c(s, s')$, create objects:

1. OPEN: **priority queue** of nodes to be processed
2. CLOSED: list of nodes already processed
3. $g(s)$: estimate of the least cost from start to a given node

The pseudocode for **best first search** can be expressed as

1. Push $s_0$ into OPEN
2. While $s_1$ not expanded
   3. Pop *best* from OPEN
   4. Add *best* to CLOSED
   5. For every successor *s'*
      6. If $g(s') > g(s) + c(s, s')$
         7. $g(s') = g(s) + c(s, s')$
         8. Add (update) $s'$ to OPEN

The main problem is how to choose the heuristic function $f(s)$ for step 3.

### Dijkstra's Algorithm

Choose $f(s) = g(s)$. Always pop the node with the smallest cost from the origin first.

### A*

If we can pre-evaluate the cost from the node to the goal $h(s)$, then we can choose a better $f(s) = g(s) + h(s)$.

- If $h(s)$ is admissible $h(s) \le h^*(s)$, $h(\text{goal}) = 0$, then the path return by A* is optimal.
- If $h(s)$ is consistent $h(s) \le c(s, s') + h(s')$, $h(\text{goal}) = 0$, then A* is optimal and efficient (will not re-expand a node).

All consistent heuristics are admissible, not vice versa.

### Weighted A*

Choose $f(s) = g(s) + \epsilon h(s)$, where $\epsilon > 1$. It is more efficient and the solution is $\epsilon$-optimal $c \le \epsilon c^*$

### Lazy A*

Instead of checking edge collision for all neighbors, only check the edge to parent when expanding. <mark>OPEN list will have multiple copies of a node since we haven't collision check.</mark>

### Shortcut

After a path is found, we can randomly pick two nodes and connect them directly if the edge is collision-free.

## Code

`run.py`: main program

1. Load map info
2. Construct graph given environment, sampler, number of vertices, connection radius.
3. Add start and end node
4. Use A\* or lazy A\* algorithm to search the optimal path and visualize

`MapEnvironment.py`: define utility functions associated with planning

1. Check edge collision
2. Compute heuristic and distance function
3. Generate path on the map
4. Visualization of the graph and path

`Sampler.py`: create random samples for graph construction

1. Use [halton sequence](https://gist.github.com/tupui/cea0a91cc127ea3890ac0f002f887bae) to generate 2-D random vertices in $(0,1)$
2. Scale by map info

`graph_maker.py`: construct graph

1. Use python package [NetworkX](https://networkx.github.io/) to easily construct a graph
2. Add sampled valid vertices
3. Connect edges within radius and without collision (do not check collision if using lazy A*)

`astar.py`: A* algorithm

1. Use `heapq` package to create priority queue to store `[f(s), count, node, g(s), parent]` info. `count` is used to prevent comparing node when $f(s)$ are the same.
2. Use dict `enqueued` to store $g(s)$ and $h(s)$ for a node, and another dict `explored` to store the explored node and its parent node.
3. Add start node to the queue.
4. While queue is not empty, pop one node and add it to `explored` if it is not already there.
5. For all the neighbor node $s'$, compute $g(s') = g(s) + c(s,s')$ if $s'$ is not already in `explored`.
6. If $s'$ is in `enqueued`, get its previous $g'(s')$ and $h'(s')$. Continue to next neighbor if $g'(s) \le g(s')$, since it is better. If not in `enqueued`, compute $h(s')$ by the heuristic function.
7. Update $g(s')$ and $h(s')$ in `enqueued` and push it to the queue.
8. If reach target node, continually find parent node by `explored` and return the path.

`lazy_astar.py`: lazy A* algorithm

1. When expanding node, check its edge collision with parent.
2. When checking neighbors, do not need to compare $g'(s')$ and $g(s')$
3. Multiple nodes with different parents can be added to the queue.

`runDubins.py`:  similar to `run.py` but use Dubins environment

`DubinsMapEnvironment.py`: inherited from `MapEnvironment`

1. Compute distances with Dubins path
2. Compute heuristic with Dubins path
3. Generate path by Dubins path planning

`DubinsSampler.py`:

1. Sample $N$ vertices same as in `Sampler.py`
2. Divide angles to $M$ parts, add angles to each vertices to create $M\times N$ samples

`Dubins.py`: utility function for Dubins path

`ROSPlanner.py`: take goal from rviz and do planning. <mark>Be careful of the frame change between map and world</mark>

1. Load map info
2. Construct graph and save for later use
3. Subscribe to pose topic, save current pose
4. Subscribe to goal topic, plan from the current pose to the goal, publish the path using service defined in lab2
5. If there are multiple goals, sequentially planning through them and combine the path

# Reference

1. [Introduction to Mobile Robotics](http://ais.informatik.uni-freiburg.de/teaching/ss13/robotics/)
2. [Probabilistic Robotics](http://www.probabilistic-robotics.org/)
