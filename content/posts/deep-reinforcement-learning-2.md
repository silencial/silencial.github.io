---
title: Deep Reinforcement Learning (Part 2)
date: 2020-02-06
lastmod: 2020-02-12
categories:
- Study
tags:
- CS
- RL
- Open Courses
---

Berkeley [CS 285](http://rail.eecs.berkeley.edu/deeprlcourse/) Review

My [solution](https://github.com/silencial/DeepRL) to the homework

[Deep Reinforcement Learning (Part 1)]({{< ref "deep-reinforcement-learning-1" >}})

[Deep Reinforcement Learning (Part 3)]({{< ref "deep-reinforcement-learning-3" >}})

<!--more-->

---

# Optimal Control and Planning

If we know the dynamics $p(\mathbf{x}_{t+1} | \mathbf{x}_t, \mathbf{u}_t)$

- Games (e.g. Atari games, chess, Go)
- Easily modeled systems (e.g. navigating a car)
- Simulated environment (e.g. simulated robots, video games)

Or we can learn the dynamics:

- System identification - fit unknown parameters of a known model
- Learning - fit a general-purpose model to observed transition data

Often it will make learning easier. Previous methods (policy gradient, value based, actor-critic) do not require $p\left(\mathbf{s}_{t+1} | \mathbf{s}_{t}, \mathbf{a}_{t}\right)$ information to learn, so called **model-free** RL.

## Open-Loop vs. Closed-Loop Planning

![Open-loop vs. Closed-loop](https://i.imgur.com/wvfotEP.png)

For **deterministic** open-loop system, the objective is:
$$
\require{mathtools}
\DeclareMathOperator*{\argmin}{arg\,min\,}
\DeclareMathOperator*{\argmax}{arg\,max\,}
\DeclarePairedDelimiters\norm{\lVert}{\rVert}
\begin{equation}\label{dol}
\begin{gathered}
\mathbf{a}_{1}, \ldots, \mathbf{a}_{T}=\argmax _{\mathbf{a}_{1}, \ldots, \mathbf{a}_{T}} \sum_{t=1}^{T} r\left(\mathbf{s}_{t}, \mathbf{a}_{t}\right) \\
\text {s.t. } \mathbf{s}_{t+1}=f\left(\mathbf{s}_{t}, \mathbf{a}_{t}\right)
\end{gathered}
\end{equation}
$$
For **stochastic** open-loop system, the objective is:
$$
\begin{gather*}
p_{\theta}\left(\mathbf{s}_{1}, \ldots, \mathbf{s}_{T} | \mathbf{a}_{1}, \ldots, \mathbf{a}_{T}\right)=p\left(\mathbf{s}_{1}\right) \prod_{t=1}^{T} p\left(\mathbf{s}_{t+1} | \mathbf{s}_{t}, \mathbf{a}_{t}\right) \\
\mathbf{a}_{1}, \ldots, \mathbf{a}_{T}=\argmax_{\mathbf{a}_{1}, \ldots, \mathbf{a}_{T}} E\left[\sum_{t} r\left(\mathbf{s}_{t}, \mathbf{a}_{t}\right) | \mathbf{a}_{1}, \ldots, \mathbf{a}_{T}\right]
\end{gather*}
$$
For **stochastic** closed-loop system, the objective is:
$$
\begin{gather*}
p_{\theta}\left(\mathbf{s}_{1}, \ldots, \mathbf{s}_{T} | \mathbf{a}_{1}, \ldots, \mathbf{a}_{T}\right)=p\left(\mathbf{s}_{1}\right) \prod_{t=1}^{T} p\left(\mathbf{s}_{t+1} | \mathbf{s}_{t}, \mathbf{a}_{t}\right) \\
\pi=\argmax _{\pi} E_{\tau \sim p(\tau)}\left[\sum_{t} r\left(\mathbf{s}_{t}, \mathbf{a}_{t}\right)\right]
\end{gather*}
$$

## Stochastic Optimization Methods

For open-loop planning, rewrite the objective $\eqref{dol}$ to be:
$$
\mathbf{A} = \argmax_\mathbf{A} J (\mathbf{A})
$$
where $\mathbf{A} = (\mathbf{a}_1, \dots, \mathbf{a}_T)$. $J$ is the general objective.

### Random Shooting Method

1. pick $\mathbf{A}_1, \dots, \mathbf{A}_N$ from some distribution (e.g. uniform)
2. choose $\mathbf{A}_i$ based on the $\argmax_i J(\mathbf{A}_i)$

### Cross-Entropy Method (CEM)

1. sample $\mathbf{A}_1, \dots, \mathbf{A}_N$ from some distribution $p(\mathbf{A})$
2. evaluate $J(\mathbf{A}_i)$ and pick the elites $\mathbf{A}_{i_1}, \dots, \mathbf{A}_{i_M}$ with the highest value
3. refit $p(\mathbf{A})$ to the elites

It is very fast if parallelized and extremely simple. But has very harsh dimensionality limit and only for open-loop planning.

## Monte Carlo Tree Search (MCTS)

For discrete case

1. choose a leaf $s_l$ using Treepolicy($s_1$)
2. evaluate the leaf using DefaultPolicy($s_l$)
3. update all values in tree between $s_1$ and $s_l$

**Upper Confidence Bounds for Trees (UCT) TreePolicy($s_t$)**: if $s_t$ not fully expanded, choose new $a_t$, else choose child with best Score($s_{t+1}$). Where $\text{Score}(s_ { t }) = \frac { Q ( s _{ t } ) } { N ( s_ { t } ) } + 2 C \sqrt { \frac { 2 \ln N ( s _{ t - 1 } ) } { N ( s_ { t } ) } }$

## Trajectory Optimization

There are two different ways to optimize $\eqref{dol}$:

**shooting method**: optimize over actions only. ==Extremely sensitive to the initial action==
$$
\min _{ \mathbf { u }_ { 1 } , \ldots , \mathbf { u } _{ T } } c \left( \mathbf { x }_ { 1 } , \mathbf { u } _{ 1 } \right) + c \big( f \left( \mathbf { x }_ { 1 } , \mathbf { u } _{ 1 } \right) , \mathbf { u }_ { 2 } \big) + \cdots + c \big( f ( f ( \ldots ) \ldots ) , \mathbf { u } _{ T } \big)
$$
**collocation method**: optimize over actions and states, with constraints
$$
\min_ { \mathbf { u } _{ 1 } , \ldots , \mathbf { u }_ { T }, \mathbf { x } _{ 1 } , \ldots , \mathbf { x }_ { T } } \sum _{ t = 1 } ^ { T } c \left( \mathbf { x }_ { t } , \mathbf { u } _{ t } \right)\quad \text{s.t. }\mathbf { x }_ { t } = f \left( \mathbf { x } _{ t - 1 } , \mathbf { u }_ { t - 1 } \right)
$$

## LQR

Now we focus on shooting method but assume $f$ is linear and $c$ is quadratic:
$$
f \left( \mathbf { x } _{ t } , \mathbf { u }_ { t } \right) = \mathbf { F } _{ t } \begin{bmatrix} \mathbf { x }_t \\ \mathbf { u }_t \end{bmatrix} + \mathbf { f }_ { t }, \quad c \left( \mathbf { x } _{ t } , \mathbf { u }_ { t } \right) = \frac { 1 } { 2 } \begin{bmatrix} \mathbf { x }_t \\ \mathbf { u }_t \end{bmatrix} ^ { T } \mathbf { C } _{ t } \begin{bmatrix} \mathbf { x }_t \\ \mathbf { u }_t \end{bmatrix} + \begin{bmatrix} \mathbf { x }_t \\ \mathbf { u }_t \end{bmatrix} ^ { T } \mathbf { c }_ { t }
$$
Then we can solve it by backward recursion and forward recursion ([Proof](#proof-1))

Backward recursion: for $t=T$ to $1$
$$
\begin{align*}
&\mathbf { Q } _{ t } = \mathbf { C }_ { t } + \mathbf { F } _{ t } ^ { T } \mathbf { V }_ { t + 1 } \mathbf { F } _{ t } \\
&\mathbf { q }_ { t } = \mathbf { c } _{ t } + \mathbf { F }_ { t } ^ { T } \mathbf { V } _{ t + 1 } \mathbf { f }_ { t } + \mathbf { F } _{ t } ^ { T } \mathbf { v }_ { t + 1 } \\
&Q \left( \mathbf { x } _{ t } , \mathbf { u }_ { t } \right) = \text {const} + \frac { 1 } { 2 } \begin{bmatrix} { \mathbf { x } _{ t } } \\ { \mathbf { u }_ { t } } \end{bmatrix} ^ { T } \mathbf { Q } _{ t } \begin{bmatrix} { \mathbf { x }_ { t } } \\ { \mathbf { u } _{ t } } \end{bmatrix} + \begin{bmatrix} { \mathbf { x }_ { t } } \\ { \mathbf { u } _{ t } } \end{bmatrix} ^ { T } \mathbf { q }_ { t } \\
&\mathbf { u } _{ t } \gets \argmin_ { \mathbf { u } _{ t } } Q \left( \mathbf { x }_ { t } , \mathbf { u } _{ t } \right) = \mathbf { K }_ { t } \mathbf { x } _{ t } + \mathbf { k }_ { t } \\
&{ \mathbf { K } _{ t } = - \mathbf { Q }_ { \mathbf { u } _{ t } , \mathbf { u }_ { t } } ^ { - 1 } \mathbf { Q } _{ \mathbf { u }_ { t } , \mathbf { x } _{ t } } } \\
&{ \mathbf { k }_ { t } = - \mathbf { Q } _{ \mathbf { u }_ { t } , \mathbf { u } _{ t } } ^ { - 1 } \mathbf { q }_ { \mathbf { u } _{ t } } } \\
&{ \mathbf { V }_ { t } = \mathbf { Q } _{ \mathbf { x }_ { t } , \mathbf { x } _{ t } } + \mathbf { Q }_ { \mathbf { x } _{ t } , \mathbf { u }_ { t } } \mathbf { K } _{ t } + \mathbf { K }_ { t } ^ { T } \mathbf { Q } _{ \mathbf { u }_ { t } , \mathbf { x } _{ t } } + \mathbf { K }_ { t } ^ { T } \mathbf { Q } _{ \mathbf { u }_ { t } , \mathbf { u } _{ t } } \mathbf { K }_ { t } } \\
&{ \mathbf { v } _{ t } = \mathbf { q }_ { \mathbf { x } _{ t } } + \mathbf { Q }_ { \mathbf { x } _{ t } , \mathbf { u }_ { t } } \mathbf { k } _{ t } + \mathbf { K }_ { t } ^ { T } \mathbf { Q } _{ \mathbf { u }_ { t } } + \mathbf { K } _{ t } ^ { T } \mathbf { Q }_ { \mathbf { u } _{ t } , \mathbf { u }_ { t } } \mathbf { k } _{ t } } \\
&{ V \left( \mathbf { x }_ { t } \right) = \text {const} + \frac { 1 } { 2 } \mathbf { x } _{ t } ^ { T } \mathbf { V }_ { t } \mathbf { x } _{ t } + \mathbf { x }_ { t } ^ { T } \mathbf { v } _{ t } }
\end{align*}
$$
Forward recursion: for $t=1$ to $T$
$$
\begin{gather*}
\mathbf{u}_{t} = \mathbf{K}_{t} \mathbf{x}_{t} + \mathbf{k}_{t} \\
\mathbf{x}_{t+1} = f(\mathbf{x}_{t}, \mathbf{u}_{t})
\end{gather*}
$$

### Stochastic Dynamics

For stochastic dynamics, we can use Gaussians to model the dynamics:
$$
p(\mathbf{x}_{t+1} | \mathbf{x}_{t}, \mathbf{u}_{t}) = \mathcal{N}\left( \mathbf { F }_ { t } \begin{bmatrix} { \mathbf { x }_{ t } } \\ { \mathbf { u }_{ t } } \end{bmatrix} + \mathbf { f } _{ t }, \Sigma_t \right)
$$
the algorithm stays the same. $\Sigma_t$ can be ignored due to the symmetry of Gaussians.

## iLQR

For nonlinear case, approximate $f$ and $c$ by first and second order approximation respectively:
$$
\begin{gather*}
f\left(\mathbf{x}_{t}, \mathbf{u}_{t}\right)-f\left(\hat{\mathbf{x}}_{t}, \hat{\mathbf{u}}_{t}\right) \approx \nabla_{\mathbf{x}_{t}, \mathbf{u}_{t}} f\left(\hat{\mathbf{x}}_{t}, \hat{\mathbf{u}}_{t}\right)\begin{bmatrix}
\mathbf{x}_{t}-\hat{\mathbf{x}}_{t} \\
\mathbf{u}_{t}-\hat{\mathbf{u}}_{t}
\end{bmatrix}\\
c\left(\mathbf{x}_{t}, \mathbf{u}_{t}\right)-c\left(\hat{\mathbf{x}}_{t}, \hat{\mathbf{u}}_{t}\right) \approx \nabla_{\mathbf{x}_{t}, \mathbf{u}_{t}} c\left(\hat{\mathbf{x}}_{t}, \hat{\mathbf{u}}_{t}\right)\begin{bmatrix}
\mathbf{x}_{t}-\hat{\mathbf{x}}_{t} \\
\mathbf{u}_{t}-\hat{\mathbf{u}}_{t}
\end{bmatrix}+\frac{1}{2}\begin{bmatrix}
\mathbf{x}_{t}-\hat{\mathbf{x}}_{t} \\
\mathbf{u}_{t}-\hat{\mathbf{u}}_{t}
\end{bmatrix}^{T} \nabla_{\mathbf{x}_{t}, u_{t}}^{2} c\left(\hat{\mathbf{x}}_{t}, \hat{\mathbf{u}}_{t}\right)\begin{bmatrix}
\mathbf{x}_{t}-\hat{\mathbf{x}}_{t} \\
\mathbf{u}_{t}-\hat{\mathbf{u}}_{t}
\end{bmatrix}
\end{gather*}
$$
Let $\delta \mathbf{x}_{t} = \mathbf{x}_{t} - \hat{\mathbf{x}}_{t}$, $\delta \mathbf{u}_{t} = \mathbf{u}_{t} - \hat{\mathbf{u}}_{t}$. Rearrange to get:
$$
\begin{gather*}
\bar{f}\left(\delta \mathbf{x}_{t}, \delta \mathbf{u}_{t}\right)=\mathbf{F}_{t}\begin{bmatrix}
\delta \mathbf{x}_{t} \\
\delta \mathbf{u}_{t}
\end{bmatrix} \\
\bar{c}\left(\delta \mathbf{x}_{t}, \delta \mathbf{u}_{t}\right)=\frac{1}{2}\begin{bmatrix}
\delta \mathbf{x}_{t} \\
\delta \mathbf{u}_{t}
\end{bmatrix}^{T} \mathbf{C}_{t}\begin{bmatrix}
\delta \mathbf{x}_{t} \\
\delta \mathbf{u}_{t}
\end{bmatrix}+\begin{bmatrix}
\delta \mathbf{x}_{t} \\
\delta \mathbf{u}_{t}
\end{bmatrix}_{L}^{T} \mathbf{c}_{t}
\end{gather*}
$$
and run LQR with $\bar{f}, \bar{c}, \delta \mathbf{x}_t, \delta \mathbf{u}_t$.

iLQR may have overshoot problem, **line search** can be applied to correct this problem. During the forward pass, search for the lowest point to avoid overshoot. The final algorithm becomes

1. $\mathbf{F}_{t}=\nabla_{\mathbf{x}_{t}, \mathbf{u}_{t}} f\left(\hat{\mathbf{x}}_{t}, \hat{\mathbf{u}}_{t}\right)$
2. $\mathbf{c}_{t}=\nabla_{\mathbf{x}_{t}, \mathbf{u}_{t}} c\left(\hat{\mathbf{x}}_{t}, \hat{\mathbf{u}}_{t}\right)$
3. $\mathbf{C}_{t}=\nabla_{\mathbf{x}_{t}, \mathbf{u}_{t}}^{2} c\left(\hat{\mathbf{x}}_{t}, \hat{\mathbf{u}}_{t}\right)$
4. Run LQR backward pass on $\delta \mathbf{x}_t, \delta \mathbf{u}_t$
5. Run forward pass with $\mathbf{u}_{t} = \mathbf{K}_{t} \delta\mathbf{x}_{t} + \alpha \mathbf{k}_{t} + \hat{\mathbf{u}}_t$

## DDP

Compare to Newton's method for computing $\min_\mathbf{x} g(\mathbf{x})$:
$$
\begin{gather*}
\mathbf{g}=\nabla_{\mathbf{x}} g(\hat{\mathbf{x}}) \\
\mathbf{H}=\nabla_{\mathbf{x}}^{2} g(\hat{\mathbf{x}}) \\
\hat{\mathbf{x}} \gets \argmin_{\mathbf{x}} \frac{1}{2}(\mathbf{x}-\hat{\mathbf{x}})^{T} \mathbf{H}(\mathbf{x}-\hat{\mathbf{x}})+\mathbf{g}^{T}(\mathbf{x}-\hat{\mathbf{x}})
\end{gather*}
$$
If we use second order dynamics approximation, the method is called differential dynamic programming (DDP)

# Model-Based RL

## Basic Model

### Naive Model

1. run base policy $\pi_0(\mathbf{a}_t | \mathbf{s}_t)$ to collect $\mathcal { D } = \left\{ \left( \mathbf { s } , \mathbf { a } , \mathbf { s }' \right)_ { i } \right\}$
2. learn dynamics model $f(\mathbf{s}, \mathbf{a})$ to minimize $\sum _{ i } \norm{f \left( \mathbf { s }_ { i } , \mathbf { a } _{ i } \right) - \mathbf { s }_ { i }' } ^ { 2 }$
3. plan through $f(\mathbf{s}, \mathbf{a})$ to choose actions

This is how system identification works in classical robotics and particularly effective if we can hand-engineer a dynamics representation using our knowledge of physics, and fit just a few parameters.

### Improvements

Similar to imitation learning, naive model can suffer from distribution mismatch problem, which can be solved by **DAgger** for model $f$ instead of policy $\pi$.

Instead of doing open-loop planning in step 3, which can cause cumulative error, we do replanning every time.

Combine these two strategies:

1. run base policy $\pi_0(\mathbf{a}_t | \mathbf{s}_t)$ to collect $\mathcal { D } = \left\{ \left( \mathbf { s } , \mathbf { a } , \mathbf { s }' \right)_{ i } \right\}$
   2. learn dynamics model $f(\mathbf{s}, \mathbf{a})$ to minimize $\sum_i \norm{f \left( \mathbf { s }_i , \mathbf { a } _{ i } \right) - \mathbf { s }_ { i }' } ^ { 2 }$
      3. plan through $f(\mathbf{s}, \mathbf{a})$ to choose actions
      4. execute the first planned action, observe resulting state $\mathbf{s}'$ (MPC)
      5. append $\left( \mathbf { s } , \mathbf { a } , \mathbf { s }' \right)$ to $\mathcal{D}$

## Uncertainty-Aware Models

Basic model-based RL suffers from overfitting problem and can easily stuck in local minimum since in step 3 we only take actions for which we think the expectation reward is high.

To solve this problem we must consider uncertainty in the model:

- Use **output entropy** of $p(\mathbf{s}_{t+1} | \mathbf{s}_t, \mathbf{a}_t)$. This is not enough because the model might be uncertain itself even if it is certain about the data.
- estimate model uncertainty $p(\theta | \mathcal{D})$ and combine it with statistical uncertainty to get $\int p\left(\mathbf{s}_{t+1} | \mathbf{s}_{t}, \mathbf{a}_{t}, \theta\right) p(\theta | \mathcal{D}) d \theta$

### Models

**Bayesian neural network (BNN)**: In BNN, nodes are connected by distributions instead of weights.
$$
\begin{gather*}
p ( \theta | \mathcal { D } ) = \prod _{ i } p \left( \theta_ { i } | \mathcal { D } \right) \\
p \left( \theta _{ i } | \mathcal { D } \right) = \mathcal { N } \left( \mu_ { i }, \sigma _{ i } \right)
\end{gather*}
$$
**Bootstrap ensembles**: Train multiple models and see if they agree.
$$
\int p\left(\mathbf{s}_{t+1} | \mathbf{s}_{t}, \mathbf{a}_{t}, \theta\right) p(\theta | \mathcal{D}) d \theta \approx \frac{1}{N} \sum_{i} p\left(\mathbf{s}_{t+1} | \mathbf{s}_{t}, \mathbf{a}_{t}, \theta_{i}\right)
$$
Need to generate **independent** datasets to get independent models. However, resampling with replacement is usually unnecessary, because SGD and random initialization usually makes the models sufficiently independent.

## Latent Space Models

For standard (fully observed) model, the goal is
$$
\max _{ \phi } \frac { 1 } { N } \sum_ { i = 1 } ^ { N } \sum _{ t = 1 } ^ { T } \log p_ { \phi } \left( \mathbf { s } _{ t + 1 , i } | \mathbf { s }_ { t , i } , \mathbf { a } _ { t , i } \right)
$$
For **complex** observations (high dimensionality, redundancy, partial observability), we have to separately learn

- $p(\mathbf{o}_t | \mathbf{s}_t)$: high-dimension but not dynamic
- $p(\mathbf{s}_{t+1} | \mathbf{s}_t, \mathbf{a}_t)$: low-dimension but dynamic

For latent space model, the goal is
$$
\max _{ \phi } \frac { 1 } { N } \sum_ { i = 1 } ^ { N } \sum _{ t = 1 } ^ { T } \mathbb{E}_{\left( \mathbf { s } _{ t } , \mathbf { s }_ { t + 1 } \right) \sim p \left( \mathbf { s } _{ t } , \mathbf { s }_ { t + 1 } | \mathbf { o } _{ 1 : T } , \mathbf { a }_ { 1 : T } \right)} \big[ \log p _ { \phi } \left( \mathbf { s } _ { t + 1 , i } | \mathbf { s } _ { t , i } , \mathbf { a } _ { t , i } \right) + \log p _ { \phi } \left( \mathbf { o } _ { t , i } | \mathbf { s } _ { t , i } \right) \big]
$$
We have many choices to approximate $p \left( \mathbf { s } _{ t } , \mathbf { s }_ { t + 1 } | \mathbf { o } _{ 1 : T } , \mathbf { a }_ { 1 : T } \right)$:

- $q_\psi ( \mathbf { s }_ { t } | \mathbf { o } _{ 1 : T } , \mathbf { a }_ { 1 : T } )$: encoder
- $q_\psi ( \mathbf { s }_ { t }, \mathbf { s } _{ t+1} | \mathbf { o }_ { 1 : T } , \mathbf { a } _ { 1 : T } )$: full smoothing posterior. Most accurate but complicated
- $q_\psi ( \mathbf { s }_ { t } | \mathbf { o }_t )$: single-step encoder. Simplest but least accurate

Assume $q(\mathbf{s}_t | \mathbf{o}_t)$ is **deterministic**, we can get single-step deterministic encoder: $q_\psi ( \mathbf { s }_ { t } | \mathbf { o } _{ t } ) = \delta(\mathbf{s}_t = g_\psi(\mathbf{o}_t)) \implies \mathbf { s } _{ t } = g_\psi(\mathbf { o } _{ t })$. Add the reward model, the latent space model can be written as:
$$
\max_ { \phi , \psi } \frac { 1 } { N } \sum _{ i = 1 } ^ { N } \sum_ { t = 1 } ^ { T } \log p _{ \phi } \big( g_ { \psi } \left( \mathbf { o } _{ t + 1 , i } \right) | g_ { \psi } \left( \mathbf { o } _{ t , i } \right) , \mathbf { a }_ { t , i } \big) + \log p _{ \phi } \big( \mathbf { o }_ { t , i } | g _{ \psi } \left( \mathbf { o }_ { t , i } \right) \big) + \log p _{ \phi } \big( r_ { t , i } | g _{ \psi } \left( \mathbf { o }_ { t , i } \right) \big)
$$
Everything is differentiable here so can be trained with backprop.

1. run base policy $\pi_0(\mathbf{a}_t | \mathbf{o}_t)$ to collect $\mathcal { D } = \left\{ \left( \mathbf { o } , \mathbf { a } , \mathbf { o }' \right)_i \right\}$
   2. learn $p_{ \phi } \left( \mathbf { s }_{ t + 1} | \mathbf { s }_t , \mathbf { a }_t \right)$, $p_{ \phi } \left( \mathbf { o }_t | \mathbf { s }_t \right)$, $p_{ \phi } \left( r_t | \mathbf { s }_t \right)$, $g_{ \psi } \left( \mathbf { o }_ { t } \right)$
      3. plan through the model to choose actions
      4. execute the first planned action, observe resulting $\mathbf{o}'$ (MPC)
      5. append $\left( \mathbf { o } , \mathbf { a } , \mathbf { o }' \right)$ to $\mathcal{D}$

# Model-Based Policy Learning

## Backprop Into Policy

![Backpropagation](https://i.imgur.com/WbAVsak.png)

1. run base policy $\pi_0(\mathbf{a}_t | \mathbf{s}_t)$ to collect $\mathcal { D } = \left\{ \left( \mathbf { s } , \mathbf { a } , \mathbf { s }' \right)_{ i } \right\}$
   2. learn dynamics model $f(\mathbf{s}, \mathbf{a})$ to minimize $\sum_{ i } \norm{f \left( \mathbf { s }_{ i } , \mathbf { a }_{ i } \right) - \mathbf { s }_{ i }' } ^ { 2 }$
   3. backprop through $f(\mathbf{s}, \mathbf{a})$ into the policy to optimize $\pi_\theta(\mathbf{a}_t | \mathbf{s}_t)$
   4. run $\pi_\theta(\mathbf{a}_t | \mathbf{s}_t)$, appending $\left( \mathbf { s } , \mathbf { a } , \mathbf { s }' \right)$ to $\mathcal{D}$

Problems:

- Similar parameter sensitivity problems as shooting methods. Policy parameters couple all the time steps, dynamic programming unavailable.
- Similar problems to training long RNNs with BPTT
  - vanishing and exploding gradients.
  - Unlike LSTM, we can't choose a simple dynamics.

Solutions:

- Use derivative-free (**model-free**) RL algorithms, with the model used to generate synthetic samples. Works well in practice; essentially "model-based acceleration" for model-free RL.
- Use **simpler policies** than neural nets.
  - LQR with learned models (**LQR-FLM**)
  - Train **local policies** to solve simple tasks
  - Combine them into **global policies** via supervised learning

## Model-Free RL With Model

$$
\nabla _ { \theta } J ( \theta ) = \sum _ { t = 1 } ^ { T } \frac { d r_t } { d \mathbf { s }_t } \prod _ { t' = 2 } ^ { t } \frac { d \mathbf { s } _ { t' } } { d \mathbf { a } _ { t' - 1 } } \frac { d \mathbf { a } _ { t' - 1 } } { d \mathbf { s } _ { t' - 1 } }
$$

Policy gradient might be more stable because it does not require multiplying many Jacobians.

### Dyna

Online $Q$-learning algorithm that performs model-free RL with a model:

1. given state $\mathbf{s}$, pick action $\mathbf{a}$ using exploration policy
2. observe $\mathbf{s}'$ and $r$, to get transition $(\mathbf{s}, \mathbf{a}, \mathbf{s}', r)$
3. update model $\hat{p}(\mathbf{s}' | \mathbf{s}, \mathbf{a})$ and $\hat{r}(\mathbf{s}, \mathbf{a})$ using $(\mathbf{s}, \mathbf{a}, \mathbf{s}')$
4. $Q$-update: $Q(\mathbf{s},\mathbf{a}) \gets Q(\mathbf{s},\mathbf{a}) + \alpha \mathbb{E}_{\mathbf{s}', r} [r + \max_{\mathbf{a}'} Q(\mathbf{s}',\mathbf{a}') - Q(\mathbf{s},\mathbf{a})]$
   5. sample $(\mathbf{s},\mathbf{a}) \sim \mathcal{B}$ from buffer of past states and actions
   6. $Q$-update: $Q(\mathbf{s},\mathbf{a}) \gets Q(\mathbf{s}, \mathbf{a}) + \alpha \mathbb{E}_{\mathbf{s}' ,r} [r + \max_{\mathbf{a}'} Q(\mathbf{s}', \mathbf{a}') - Q(\mathbf{s},\mathbf{a})]$

Model is used in $Q$-update to calculate the expectation instead of using samples as in $Q$-learning.

**General Dyna-style model-based RL**:

Generate samples from model for RL to learn. (step 5)

1. collect some data, consisting of transitions $(\mathbf{s}, \mathbf{a}, \mathbf{s}', r)$
2. learn model $\hat{p}(\mathbf{s}' | \mathbf{s}, \mathbf{a})$ and $\hat{r}(\mathbf{s}, \mathbf{a})$
   3. sample $\mathbf{s} \sim \mathcal{B}$ from buffer
   4. choose action $\mathbf{a}$ (from $\mathcal{B}$, $\pi$, or random)
   5. simulate $\mathbf{s}' \sim \hat{p}(\mathbf{s}' | \mathbf{s}, \mathbf{a})$ and $r = \hat{r}(\mathbf{s}, \mathbf{a})$
   6. train on $(\mathbf{s}, \mathbf{a}, \mathbf{s}', r)$ with model-free RL
   7. (optional) take $N$ more model-based steps

## Local Models

Remember [LQR](#lqr) gives a linear feedback controller:
$$
\begin{gather*}
p \left( \mathbf { x } _{ t + 1 } | \mathbf { x }_ { t } , \mathbf { u } _{ t } \right) = \mathcal { N } \big( f \left( \mathbf { x }_ { t } , \mathbf { u } _{ t } \right) , \Sigma \big) \\
f \left( \mathbf { x }_ { t } , \mathbf { u } _{ t } \right) \approx \mathbf { A }_ { t } \mathbf { x } _{ t } + \mathbf { B }_ { t } \mathbf { u } _{ t } \\
\mathbf { A }_ { t } = \frac { d f } { d \mathbf { x } _{ t } } \quad \mathbf { B }_ { t } = \frac { d f } { d \mathbf { u } _{ t } }
\end{gather*}
$$
And $\mathbf{A}_t, \mathbf{B}_t$ can be learned instead of learning $f$. The whole algorithm can be seen below:

![iLQR](https://i.imgur.com/W6HfTcy.png)

Choices to update controller with iLQR output $\hat{\mathbf{x}}_t, \hat{\mathbf{u}}_t, \mathbf{K}_t, \mathbf{k}_t$:

- $p \left( \mathbf { u } _{ t } | \mathbf { x }_ { t } \right) = \delta(\mathbf { u } _{ t } = \hat{\mathbf { u } }_t)$ : doesn't correct deviations or drift.
- $p \left( \mathbf { u } _{ t } | \mathbf { x }_ { t } \right) = \delta(\mathbf { u } _{ t } = \mathbf{K}_t (\mathbf{x}_t - \hat{\mathbf{x}}_t) + \mathbf{k}_t +  \hat{\mathbf { u } }_t)$: too strict.
- $p \left( \mathbf { u } _{ t } | \mathbf { x }_ { t } \right) = \mathcal{N}\big( \mathbf{K}_t (\mathbf{x}_t - \hat{\mathbf{x}}_t) + \mathbf{k}_t +  \hat{\mathbf { u } }_t, \Sigma_t\big)$ where $\Sigma_t = \mathbf{Q}_{\mathbf{u}_t, \mathbf{u}_t}^{-1}$: add noise so that samples don't look the same.

Choices to fit the dynamics:

- Linear regression: $p \left( \mathbf { x } _{ t + 1 } | \mathbf { x }_ { t } , \mathbf { u } _{ t } \right) = \mathcal { N } \left( \mathbf { A }_ { t } \mathbf { x } _{ t } + \mathbf { B }_ { t } \mathbf { u } _{ t } + \mathbf { c } , \mathbf { N }_ { t } \right)$
- Bayesian linear regression: use favorite global model as prior.

Since we assume the model is locally linear, the updated controller is only good when it is close to old controller. Use trajectories generated from two controllers to measure their closeness by  KL divergence $D_{\text{KL}}\big(p(\tau) \| \bar{p}(\tau)\big) \le \epsilon$.

## Global Policy

**Guided policy search**:

1. optimize each local policy $\pi_{\text{LQR}, i}(\mathbf{u}_t | \mathbf{x}_t)$ on initial state $\mathbf{x}_{0, i}$ w.r.t. $\tilde{c}_{k, i}(\mathbf{x}_t, \mathbf{u}_t)$
2. use samples from step 1 to train $\pi_\theta(\mathbf{u}_t | \mathbf{x}_t)$ to mimic each $\pi_{\text{LQR}, i}(\mathbf{u}_t | \mathbf{x}_t)$
3. update cost function $\tilde{c}_{k+1, i}(\mathbf{x}_t, \mathbf{u}_t) = c(\mathbf{x}_t, \mathbf{u}_t) - \lambda_{k+1, i} \log \pi_\theta(\mathbf{u}_t | \mathbf{x}_t)$

**Distillation**: make a single model as good as ensemble. Train on the ensemble's predictions as **soft targets**
$$
p_i = \frac{\exp(z_i / T)}{\sum_j \exp(z_j / T)}
$$
For multi-task transfer, train independent model for different task $\pi_i$, then use supervised learning/distillation:
$$
\mathcal{L} = \sum_{\mathbf{a}} \pi_i(\mathbf{a} | \mathbf{s}) \log \pi_\text{AMN}(\mathbf{a} | \mathbf{s})
$$

# Proof

## Proof 1

From the relation between $Q$ and $V$ we can get
$$
\begin{equation}\label{lqrqv}
Q\left(\mathbf{x}_{T-1}, \mathbf{u}_{T-1}\right)=\text{const} + \frac{1}{2}\begin{bmatrix}
\mathbf{x}_{T-1} \\
\mathbf{u}_{T-1}
\end{bmatrix}^{T} \mathbf{C}_{T-1} \begin{bmatrix}
\mathbf{x}_{T-1} \\
\mathbf{u}_{T-1}
\end{bmatrix}+\begin{bmatrix}
\mathbf{x}_{T-1} \\
\mathbf{u}_{T-1}
\end{bmatrix}^{T} \mathbf{c}_{T-1}+V\left(\mathbf{x}_{T}\right)
\end{equation}
$$
At step $T$, $V = 0$. Take the gradient of $\eqref{lqrqv}$ w.r.t. $\mathbf{u}_T$:
$$
\begin{equation}\label{lqrg}
\nabla_{\mathbf{u}_{T}} Q\left(\mathbf{x}_{T}, \mathbf{u}_{T}\right)=\mathbf{C}_{\mathbf{u}_{T}, \mathbf{x}_{T}} \mathbf{x}_{T}+\mathbf{C}_{\mathbf{u}_{T}, \mathbf{u}_{T}} \mathbf{u}_{T}+\mathbf{c}_{\mathbf{u}_{T}}^{T}
\end{equation}
$$
Here we have expanded $\mathbf{C}_T$ and $\mathbf{c}_T$ to sub-matrices as follows:
$$
\mathbf{C}_{T} =\begin{bmatrix}
\mathbf{C}_{\mathbf{x}_{T}, \mathbf{x}_{T}} & \mathbf{C}_{\mathbf{x}_{T}, \mathbf{u}_{T}} \\
\mathbf{C}_{\mathbf{u}_{T}, \mathbf{x}_{T}} & \mathbf{C}_{\mathbf{u}_{T}, \mathbf{u}_{T}}
\end{bmatrix} \qquad
\mathbf{c}_{T} =\begin{bmatrix}
\mathbf{c}_{\mathbf{x}_{T}} \\
\mathbf{c}_{\mathbf{u}_{T}}
\end{bmatrix}
$$
Set the gradient $\eqref{lqrg}$ to $0$ and solve for $\mathbf{u}_T$:
$$
\begin{split}
\mathbf{u}_{T} &= -\mathbf{C}_{\mathbf{u}_{T}, \mathbf{u}_{T}}^{-1}\left(\mathbf{C}_{\mathbf{u}_{T}, \mathbf{x}_{T}} \mathbf{x}_{T}+\mathbf{c}_{\mathbf{u}_{T}}\right) \\
&= \mathbf{K}_T\mathbf{x}_T + \mathbf{k}_T
\end{split}
$$
At step $T-1$, first compute $V(\mathbf{x}_T)$:
$$
\begin{align*}
V(\mathbf{x}_T) &= \text {const} + \frac{1}{2}\begin{bmatrix}
\mathbf{x}_{T} \\
\mathbf{K}_T\mathbf{x}_T + \mathbf{k}_T
\end{bmatrix}^{T} \mathbf{C}_{T} \begin{bmatrix}
\mathbf{x}_{T} \\
\mathbf{K}_T\mathbf{x}_T + \mathbf{k}_T
\end{bmatrix}+\begin{bmatrix}
\mathbf{x}_{T} \\
\mathbf{K}_T\mathbf{x}_T + \mathbf{k}_T
\end{bmatrix}^{T} \mathbf{c}_{T} \\
&= \frac{1}{2} \mathbf{x}_T^T \mathbf{V}_T \mathbf{x}_T + \mathbf{x}_T^T \mathbf{v}_{T} \\
\mathbf{V}_{T} &= \mathbf{C}_{\mathbf{x}_{T}, \mathbf{x}_{T}}+\mathbf{C}_{\mathbf{x}_{T}, \mathbf{u}_{T}} \mathbf{K}_{T}+\mathbf{K}_{T}^{T} \mathbf{C}_{\mathbf{u}_{T}, \mathbf{x}_{T}}+\mathbf{K}_{T}^{T} \mathbf{C}_{\mathbf{u}_{T}, \mathbf{u}_{T}} \mathbf{K}_{T} \\
\mathbf{v}_{T} &= \mathbf{c}_{\mathbf{x}_{T}}+\mathbf{C}_{\mathbf{x}_{T}, \mathbf{u}_{T}} \mathbf{k}_{T}+\mathbf{K}_{T}^{T} \mathbf{C}_{\mathbf{u}_{T}}+\mathbf{K}_{T}^{T} \mathbf{C}_{\mathbf{u}_{T}, \mathbf{u}_{T}} \mathbf{k}_{T}
\end{align*}
$$
Plug in the model $\mathbf{x}_{T}=f\left(\mathbf{x}_{T-1}, \mathbf{u}_{T-1}\right)$ in $V$ and then plug in $V$ in $Q$ to get
$$
\begin{align*}
Q\left(\mathbf{x}_{T-1}, \mathbf{u}_{T-1}\right) &= \text {const}+\frac{1}{2}\begin{bmatrix}
{\mathbf{x}_{T-1}} \\
{\mathbf{u}_{T-1}}
\end{bmatrix}^{T} \mathbf{Q}_{T-1}\begin{bmatrix}
{\mathbf{x}_{T-1}} \\
{\mathbf{u}_{T-1}}
\end{bmatrix}+\begin{bmatrix}
{\mathbf{x}_{T-1}} \\
{\mathbf{u}_{T-1}}
\end{bmatrix}^{T} \mathbf{q}_{T-1} \\
\mathbf{Q}_{T-1} &= \mathbf{C}_{T-1}+\mathbf{F}_{T-1}^{T} \mathbf{V}_{T} \mathbf{F}_{T-1} \\
\mathbf{q}_{T-1} &= \mathbf{c}_{T-1}+\mathbf{F}_{T-1}^{T} \mathbf{V}_{T} \mathbf{f}_{T-1}+\mathbf{F}_{T-1}^{T} \mathbf{v}_{T}
\end{align*}
$$
Take the gradient and set it to $0$ to get
$$
\begin{split}
\mathbf{u}_{T-1} &= -\mathbf{Q}_{\mathbf{u}_{T-1}, \mathbf{u}_{T-1}}^{-1}\left(\mathbf{Q}_{\mathbf{u}_{T-1}, \mathbf{x}_{T-1}} \mathbf{x}_{T-1}+\mathbf{q}_{\mathbf{u}_{T-1}}\right) \\
&= \mathbf{K}_{T-1}\mathbf{x}_{T-1} + \mathbf{k}_{T-1}
\end{split}
$$
Continue this process we can get the backward pass.

# References

1. [RL — LQR & iLQR](https://medium.com/@jonathan_hui/rl-lqr-ilqr-linear-quadratic-regulator-a5de5104c750)
