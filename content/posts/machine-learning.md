---
title: Machine Learning
date: 2019-07-26
lastmod: 2019-09-14
categories:
- Study
tags:
- CS
- ML
- Open Courses
---

Machine Learning notes.

- Coursera Andrew Ng [Machine Learning](https://www.coursera.org/learn/machine-learning/home/welcome). My [solution](https://github.com/silencial/Machine-Learning)
- Stanford CS231n [Convolutional Neural Networks for Visual Recognition](http://cs231n.stanford.edu/index.html). My [solution](https://github.com/silencial/CNN-for-Visual-Recognition)

<!--more-->

---

Learning = Representation + Evaluation + Optimization

![ML Algorithms](https://i.imgur.com/xMHZRZZ.png)

# Linear Regression

## Cost Function

Hypothesis:
$$
h_{\theta}(x)=\theta_{0}+\theta_{1} x
$$
Parameters:
$$
\theta_0,\quad \theta_1
$$
Cost function:
$$
J\left(\theta_{0}, \theta_{1}\right)=\frac{1}{2 m} \sum_{i=1}^{m}\left(h_{\theta}\left(x^{(i)}\right)-y^{(i)}\right)^{2}
$$
Goal:
$$
\min_{\theta_{0}, \theta_{1}} J\left(\theta_{0}, \theta_{1}\right)
$$

## Multiple Variables

$$
h_{\theta}(x)=\theta^{T} x=\theta_{0} x_{0}+\theta_{1} x_{1}+\theta_{2} x_{2}+\dots+\theta_{n} x_{n}
$$

Make sure to scale every feature into approximately $-1 \le x_i \le 1$ range. ==(Mean normalization)==

Be carful of the model interpretation when **Multicollinearity** (multiple variables are correlated to each other) is present.

## Gradient Descent

Repeat until convergence:
$$
\theta_j := \theta_j - \alpha \frac{\partial}{\partial \theta_j} J(\theta_0, \theta_1)
$$
where $\alpha$ is the learning rate.

- If $\alpha$ is too small, gradient descent can be slow.
- If $\alpha$ is too large, gradient descent can overshoot. It may fail to converge, or even diverge.

Gradient descent can get stuck in a local minimum if the cost function is not convex.

For linear/logistic regression the formula are the same:
$$
\theta_j := \theta_j - \alpha \frac{1}{m} \sum_{i=1}^m \big(h_\theta(x^{(i)}) - y^{(i)}\big)x_j^{(i)}
$$

## Normal Equation

Solve for $\theta$ analytically.
$$
\begin{align*}
&\frac{\partial}{\partial \theta_j} J(\theta) = 0 \\
\implies & \theta = (X^T X)^{-1} X^T y
\end{align*}
$$

# Regularization

Overfitting: If we have too many features, the learned hypothesis may fit the training set very well, but fail to generalize to new examples.

## L1 Regularization

$$
\require{mathtools}
\DeclarePairedDelimiters\norm{\lVert}{\rVert}
\DeclareMathOperator*{\argmin}{arg\,min\,}
\DeclareMathOperator*{\argmax}{arg\,max\,}
\norm{\theta}_1 = \sum_i |\theta_i|
$$

In model relying on sparse features, L1 regularization helps drive the weights of irrelevant or barely relevant features to **exactly 0**.

## L2 Regularization

$$
\norm{\theta}_2 = \sum_i \theta_i^2
$$

L2 regularization helps drive outlier weights **closer to 0** but not quite to 0

### Gradient Descent

Modify the cost function to be:
$$
J(\theta)=\frac{1}{2 m}\left[\sum_{i=1}^{m}\left(h_{\theta}(x^{(i)})-y^{(i)}\right)^{2}+\lambda \sum_{j=1}^{n} \theta_{j}^{2}\right]
$$
then
$$
\begin{align*}
\theta_0 &:= \theta_0 - \alpha \frac{1}{m} \sum_{i=1}^m (h_{\theta}(x^{(i)})-y^{(i)}) x_0^{(i)} \\
\theta_j &:= \theta_j \left( 1 - \alpha \frac{\lambda}{m} \right) - \alpha \frac{1}{m} \sum_{i=1}^m (h_{\theta}(x^{(i)})-y^{(i)}) x_j^{(i)}
\end{align*}
$$

### Normal Equation

$$
\theta =
\left(
X^T X + \lambda
\left[\begin{smallmatrix}
0 & & & \\
& 1 & & \\
& & \ddots & \\
& & & 1
\end{smallmatrix}\right]^{-1}
\right)
X^T y
$$

# Logistic Regression

## Classification

With $y \in \{0, 1\}$. Use $h_\theta(x)$ as the estimated probability that $y=1$ on input $x$. We want $0 \le h_\theta(x)\le 1$.
$$
\begin{split}
h_\theta(x) &= g(\theta^T x) \\
&= \frac{1}{1 + e^{-\theta^T x}}
\end{split}
$$
where $g$ is called the **Sigmoid function**.

Cost function:
$$
\operatorname{Cost}(h_\theta(x), y) =
\begin{dcases}
-\log(h_\theta(x)) &\text{if } y=1\\
-\log(1 - h_\theta(x)) &\text{if } y=0
\end{dcases}
$$
And
$$
\begin{split}
J(\theta) &=\frac{1}{m} \sum_{i=1}^{m} \operatorname{Cost}(h_{\theta}(x^{(i)}), y^{(i)}) \\
&= -\frac{1}{m}\left[\sum_{i=1}^{m} y^{(i)} \log h_{\theta}(x^{(i)})+(1-y^{(i)}) \log (1-h_{\theta}(x^{(i)}))\right]
\end{split}
$$

## Multi-Class Classification

Train a logistic regression classifier $h_\theta^{(i)}(x)$ for each class $i$ to predict the probability that $y = i$.

On a new input $x$ to make a prediction, pick the class $i$ that maximizes $\max_i h_\theta^{(i)}(x)$.

# Neural Network

1. Initialize weights.
2. Choose the activation function.
3. Implement forward propagation to get $h_\Theta(x^{(i)})$ for any $x^{(i)}$.
4. Implement code to compute cost function $J(\Theta).$
5. Implement backprop to compute partial derivatives.
6. Use gradient checking to see whether backprop is correct. Then disable the code.
7. Use gradient descent or other optimization method with backpropagation to minimize $J(\Theta)$.

## Weight Initialization

Assign the weights from a Gaussian distribution with zero mean and small variance is okay for small networks, but problems with deeper network.

Since the goal is to keep variance stays the same through each layer, we can use Xavier initialization.
$$
\operatorname{var}(w_i) = \frac{1}{N_{avg}} = \frac{2}{N_{in} + N_{out}}
$$
==Xavier assumes zero centered activation function, double the variance if using ReLU==

## Activation Function

### Hidden Layer

==ReLU is a good default choice for most problems==

Consider when choosing activation function:

- Vanishing/Exploding gradients: When local gradient is very small/large, it will kill/blow the gradient during backprop.
- Zero-centered: Could introduce undesirable zig-zagging dynamics in gradient updates.

![Activation Functions](https://i.imgur.com/3w1EOxp.png)

### Output Layer

- Use SoftMax function for classification problem.
- Use Linear function for regression problem.

## Cost Function

$h_\Theta (x) \in \mathbb{R}^K, \quad (h_\Theta(x))_i = i^{th}$ output.
$$
J(\Theta)=-\frac{1}{m}\left[\sum_{i=1}^{m} \sum_{k=1}^{K} y_{k}^{(i)} \log \left(h_{\Theta}(x^{(i)})\right)_{k}+(1-y_{k}^{(i)}) \log \left(1-\big(h_{\Theta}(x^{(i)})\big)_{k}\right)\right] +\frac{\lambda}{2 m} \sum_{l=1}^{L-1} \sum_{i=1}^{s_{l}} \sum_{j=1}^{s_{l+1}}\left(\Theta_{j i}^{(l)}\right)^{2}
$$

## Backpropagation

Given one training example $(x, y)$, use the Forward propagation to compute the cost:
$$
\begin{align*}
a^{(1)} &= x \\
z^{(2)} &= \Theta^{(1)} a^{(1)} \\
a^{(2)} &= g(z^{(2)}) \quad (\text{add } a_0^{(2)}) \\
z^{(3)} &= \Theta^{(2)} a^{(2)} \\
a^{(3)} &= g(z^{(3)}) \quad (\text{add } a_0^{(3)}) \\
z^{(4)} &= \Theta^{(3)} a^{(3)} \\
a^{(4)} &= h_\Theta(x) =  g(z^{(4)})
\end{align*}
$$
In Backpropagation, first compute $\delta_j^{(l)} = \frac{\partial}{\partial z_j^{(l)}} J(\Theta)$, which is the "error" of node $j$ in layer $l$.
$$
\begin{align*}
\delta^{(4)} &= a^{(4)} - y \\
\delta^{(3)} &= (\Theta^{(3)})^T \delta^{(4)} .*g'(z^{(3)}) \\
\delta^{(2)} &= (\Theta^{(2)})^T \delta^{(3)} .*g'(z^{(2)}) \\
\end{align*}
$$
Then the gradient:
$$
\begin{align*}
\frac{\partial}{\partial \Theta_{ij}^{(l)}} J(\Theta) &= \frac{1}{m} a_j^{(l)} \delta_i^{(l + 1)} + \lambda \Theta_{ij}^{(l)}  & &\text{if } j \ne 0 \\
\frac{\partial}{\partial \Theta_{ij}^{(l)}} J(\Theta) &= \frac{1}{m} a_j^{(l)} \delta_i^{(l + 1)} & &\text{if } j = 0 \\
\end{align*}
$$

## Gradient Check

A numerical method to check the backprop is correct. Useful in implementation.
$$
\frac{\partial}{\partial \theta_j} J(\theta) \approx \frac{J(\theta_1,\dots, \theta_j + \epsilon, \dots, \theta_n) - J(\theta_1,\dots, \theta_j - \epsilon, \dots, \theta_n)}{2\epsilon}
$$

## Batch Normalization

Apply feature scaling not just to the input layer, but also to the hidden units.

**Make it possible to use significantly higher learning rates, and reduces  the sensitivity to initialization.**

Before activation function, normalize $z$ to $z_\text{norm}$ and then scale and shift to $\tilde{z}$:
$$
\tilde{z} = \gamma z_\text{norm} + \beta
$$
where $\beta$ and $\gamma$ are learned during training.

## Dropout Regularization

Besides using early stopping and L1/L2 regularization, dropout regularization is another popular approach to prevent overfitting in neural network.

- Each time an example is read, some hidden units are removed with probability.
- During testing there is no dropout applied, but with averaging.
- Different hidden layer can have different dropout probability.
- It can be viewed as a form of model averaging.

# Convolutional Neural Network

Useful in computer vision:

- Classification
- Segmentation
- Localization
- Detection

Since using regular neural network on large images requires huge number of parameters, CNN use **convolution** + **pooling** layers to first perform feature extraction before passing it to a fully connected hidden layers.

Two main advantages of CNN over just fully connected layers:

- Parameter sharing: feature detector is likely invariant to locations.
- Sparsity of connection: only connect each neuron to only a local region of the input.
- Translation invariant.

## Convolution

A convolution layer is made up of some predetermined number of filters. **Each filter acts as a detector for a particular feature**.

Always use **multiple filters** at the same time and stack all these feature maps together.

Treat convolution matrix as parameter and **learn** them through backprop.

Use **activation function** to introduce nonlinearity to output, e.g. ReLU.

==Since all neurons in a feature map share the same parameters, CNN can recognize a pattern in any location once it is learned.==

## Pooling

Pooling layers are in charge of **downsampling** the input. It can decrease feature map size while at the same time keeping the important information.

The most common type of pooling is **max pooling**.

- Given pooling window size, stride size and padding size.
- Take the max value in the pooling window.

Pooling also helps to make the representation become approximately **invariant to small translations**. This property is useful if we care more about whether some feature is present than exactly where it is.

Padding (zero-padding) is used in CNN to preserve the size of the feature maps, otherwise they would shrink at each layer.

## Transfer Learning

Transfer learning in CNN is common. When your interested dataset is small, train the CNN on a large dataset with similar data, then transfer learn to your dataset.

Usually only the FC layers needs to be relearned during transfer. The number of layers needs to be relearned depends on the size of your dataset.

# Recurrent Neural Network

More flexible in architecture design:

- Image Captioning
- Sentiment Classification
- Machine Translation
- Video Classification on frame level

## Vanilla RNN

We can process a sequence of vectors $x$ by applying a recurrence formula at every time step:
$$
\begin{align*}
h_t &= f_W (h_{t-1}, x_t) \\
&= \tanh(W_{hh} h_{t-1} + W_{xh} x_t) \\
y_t &=W_{hy} h_t
\end{align*}
$$
==Computing gradient of $h_0$ involves many factors of $W$ and $\tanh$, easily lead to gradient exploding/vanishing.==

## LSTM

Long Short Term Memory (LSTM)
$$
\begin{gather*}
\begin{pmatrix}
{i} \\ {f} \\ {o} \\ {g}
\end{pmatrix}
=\begin{pmatrix}
{\sigma} \\ {\sigma} \\ {\sigma} \\ {\tanh }
\end{pmatrix} W
\begin{pmatrix}
{h_{t-1}} \\ {x_{t}}
\end{pmatrix} \\
c_{t} = f \odot c_{t-1}+i \odot g \\
h_{t} = o \odot \tanh \left(c_{t}\right)
\end{gather*}
$$

- $f$: Forget gate, whether to erase cell
- $i$: Input gate, whether to write to cell
- $g$: Gate gate, how much to write to cell
- $o$: Output gate, how much to reveal cell

# Generative Adversarial Network

- Generator network: try to fool the discriminator by generating real-looking images.
- Discriminator network: try to distinguish between real and fake images.

Train jointly with the minimax objective function:
$$
\min _{\theta_{g}} \max _{\theta_{d}}\Big[\mathbb{E}_{x \sim p_\text{data}} \log D_{\theta_{d}}(x)+\mathbb{E}_{z \sim p(z)} \log \Big(1-D_{\theta_{d}}\big(G_{\theta_{g}}(z)\big)\Big)\Big]
$$
In practice, alternate between:

1. Gradient ascent on discriminator
   $$
   \max _{\theta_{d}}\Big[\mathbb{E}_{x \sim p_\text{data}} \log D_{\theta_{d}}(x)+\mathbb{E}_{z \sim p(z)} \log \Big(1-D_{\theta_{d}}\big(G_{\theta_{g}}(z)\big)\Big)\Big]
   $$

2. Gradient ascent on generator
   $$
   \min _{\theta_{g}} \mathbb{E}_{z \sim p(z)} \log \Big(D_{\theta_{d}}\big(G_{\theta_{g}}(z)\big)\Big)
   $$
   ==The reason to use gradient ascent instead of gradient descent for the generator is to put more gradient signal on the region where samples are bad.==

# Reinforcement Learning

Problems involving an **agent** interacting with an **environment**, which provides numeric **reward** signals. The goal is to learn how to take actions in order to maximize reward.

## Markov Decision Process

- $\mathcal{S}$: set of possible states
- $\mathcal{A}$: set of possible actions
- $\mathcal{R}$: distribution of reward given state and action
- $\mathbb{P}$: transition probability
- $\gamma$: discount factor

1. At time step $t=0$, environment samples initial state $s_0 \sim p(s_0)$
2. For $t=0$ until done:
   1. Agent selects action $a_t$
   2. Environment samples reward $r_t \sim R(\cdot \mid s_t, a_t)$
   3. Environment samples next state $s_{t+1} \sim P(\cdot \mid s_t, a_t)$
   4. Agent receives reward $r_t$ and next state $s_{t+1}$

A policy $\pi: \mathcal{S} \to \mathcal{A}$ specifies what action to take in each state.

Objective: find policy $\pi^* = \argmax_\pi \mathbb{E}[\sum_t \gamma^t r_t]$

## Basics

### Value Function

Measure how good is a state.

The value function at state $s$, is the expected cumulative reward from following the policy from state $s$:
$$
V^{\pi}(s)=\mathbb{E}\left[\sum_{t \ge 0} \gamma^{t} r_{t} | s_{0}=s, \pi\right]
$$

### Q-Value Function

Measure how good is a state-action pair.

The Q-value function at state $s$ and action $a$, is the cumulative reward from taking action $a$ in state $s$ and then following the policy:
$$
Q^{\pi}(s, a)=\mathbb{E}\left[\sum_{t \ge 0} \gamma^{t} r_{t} \Bigm\vert s_{0}=s, a_{0}=a, \pi\right]
$$

### Bellman Equation

The optimal Q-value function $Q^*$ is the maximum expected cumulative reward achievable
from a given state-action pair:
$$
Q^*(s, a) = \max_\pi \mathbb{E}\left[\sum_{t \ge 0} \gamma^{t} r_{t} \Bigm\vert s_{0}=s, a_{0}=a, \pi\right]
$$
$Q^*$ satisfies the Bellman equation:
$$
Q^{*}(s, a)=\mathbb{E}_{s^{\prime} \sim \mathcal{E}}\left[r+\gamma \max _{a^{\prime}} Q^{*}\left(s^{\prime}, a^{\prime}\right) \Bigm\vert s, a\right]
$$

## Solver

### Value Iteration

Use Bellman equation as an iterative update:
$$
Q_{i+1}(s, a)=\mathbb{E}\left[r+\gamma \max _{a^{\prime}} Q_i\left(s^{\prime}, a^{\prime}\right) \Bigm\vert s, a\right]
$$
$Q_i$ will converge to $Q^*$ as $i\to \infty$

==Problem: Must compute $Q(s,a)$ for every state-action pair.==

### Q-Learning

Use a function approximator to estimate $Q(s,a)$, e.g. a neural network. $Q(s,a; \theta) \approx Q^*(s,a)$. Loss function is
$$
L_{i}\left(\theta_{i}\right)=\mathbb{E}_{s, a \sim \rho(\cdot)}\left[\left(y_{i}-Q\left(s, a ; \theta_{i}\right)\right)^{2}\right]
$$
where $y_i = \mathbb{E}_{s'\sim \mathcal{E}}\left[r+\gamma \max _{a^{\prime}} Q_i\left(s^{\prime}, a^{\prime}\right) \mid s, a\right]$

### Policy Gradients

Sometimes the $Q$-function can be very complicated while the policy are much simpler.

Gradient ascent on policy parameters with rewards:
$$
\begin{split}
J(\theta) &= \mathbb{E}\left[ \sum_{t\ge 0} \gamma^t r_t \Bigm\vert \pi_\theta \right] \\
&= \mathbb{E}_{\tau\sim p(\tau; \theta)} [r(\tau)] \\
&= \int_\tau r(\tau) p(\tau; \theta) d\tau
\end{split}
$$
and
$$
\begin{split}
\nabla_\theta J(\theta) &= \int_\tau r(\tau) \nabla_\theta p(\tau; \theta) d\tau \\
&= \int_\tau \big(r(\tau) \nabla_\theta \log p(\tau; \theta)\big) p(\tau; \theta) d\tau \\
&= \mathbb{E}_{\tau\sim p(\tau; \theta)} [r(\tau) \nabla_\theta \log p(\tau; \theta)]
\end{split}
$$
plug in $p(\tau ; \theta)=\prod_{t \ge 0} p\left(s_{t+1} | s_{t}, a_{t}\right) \pi_{\theta}\left(a_{t} | s_{t}\right)$ to get
$$
\nabla_\theta J(\theta) \approx \sum_{t\ge 0} r(\tau) \nabla_\theta \log \pi_\theta (a_t | s_t)
$$

### Variance Reduction

The basic of policy gradients is that if a trajectory is good then all its actions were good. In expectation, it averages out. But it also suffers from high variance because credit assignment is really hard.

- Push up probabilities of an action seen, only by the cumulative future reward from that state
  $$
  \nabla_\theta J(\theta) \approx \sum_{t\ge 0} \left( \sum_{t'\ge t} r_{t'} \right) \nabla_\theta \log \pi_\theta (a_t | s_t)
  $$

- Use discount factor $\gamma$ to ignore delayed effects
  $$
  \nabla_\theta J(\theta) \approx \sum_{t\ge 0} \left( \sum_{t'\ge t} \gamma^{t' - t} r_{t'} \right) \nabla_\theta \log \pi_\theta (a_t | s_t)
  $$

- Introduce a baseline function dependent on the state
  $$
  \nabla_\theta J(\theta) \approx \sum_{t\ge 0} \left( \sum_{t'\ge t} \gamma^{t' - t} r_{t'} - b(s_t) \right) \nabla_\theta \log \pi_\theta (a_t | s_t)
  $$

- Baseline
  $$
  \nabla_\theta J(\theta) \approx \sum_{t\ge 0} \big( Q^{\pi_\theta}(s_t, a_t) - V^{\pi_\theta}(s_t) \big) \nabla_\theta \log \pi_\theta (a_t | s_t)
  $$

# Naive Bayes

Assumption: **independence** between the features.

It simplifies the classification task dramatically and work well in document classification and spam filtering.

Given training data $X = (X_1, X_2, \dots, X_n)$, the probability of $X$ belonging to class $C_k$ is given by
$$
\begin{split}
P\left(C_{k} | X_{1}, \ldots, X_{\mathrm{n}}\right) &= \frac{P\left(X_{1}, \ldots, X_{\mathrm{n}} | C_{k}\right) P\left(C_{k}\right)}{P\left(X_{1}, \ldots, X_{\mathrm{n}}\right)} \\
&=\frac{P\left(X_{1} | C_{k}\right) \ldots P\left(X_{\mathrm{n}} | C_{k}\right) P\left(C_{k}\right)}{P\left(X_{1}, \ldots,X_{\mathrm{n}}\right)}
\end{split}
$$
Ignore the normalize term and use **Maximum A Posteriori (MAP)** classification rule to get the class number
$$
\hat{y} = \argmax_x p(C_k) \prod_{i=1}^n p(x_i | C_k)
$$

## Distribution

For discrete value, the Bayes approach is intuitive.

For continuous value, we can either

- Use **binning** to discretize the feature values to obtain a new set of Bernoulli-distributed features.
- Or assuming it has **Gaussian distribution**.

To avoid unseen features $p(\mathbf{x} | C_k) = 0$ wipe out all information in the other probabilities, use **Laplacian correction** to add 1 to each case.

# Support Vector Machine

Alternative view of logistic regression:
$$
\min _{\theta} C \sum_{i=1}^{m}\left[y^{(i)} \text{cost}_{1}\left(\theta^{T} x^{(i)}\right)+\left(1-y^{(i)}\right) \text{cost}_{0}\left(\theta^{T} x^{(i)}\right)\right]+\frac{1}{2} \sum_{i=1}^{n} \theta_{j}^{2}
$$
An intuitive choice for the cost function is **Hinge loss**. It can be represented as
$$
\begin{align*}
&\text{minimize} & &\frac{1}{2} \norm{a}_{2} \\
&\text{subject to} & &a^{T} x_{i}+b \ge 1, \quad i=1, \dots, N \\
& & &a^{T} y_{i}+b \le-1, \quad i=1, \dots, M
\end{align*}
$$

## Kernels

Using kernel allows the algorithm to fit the maximum-margin hyperplane in a transformed feature space.

Given $x$, compute new feature $f_i$ depending on proximity to landmarks $l^{(1)}, \dots, l^{(n)}$:
$$
f_i = \text{sim}(x, l^{(i)})
$$
Then predict $y = 1$ if $\theta^T f \ge 0$.

==To make valid kernels, similarity function need to satisfy Mercer's condition.==

Gaussian kernel:
$$
f_{i}=\exp \left(-\frac{\norm{x-l^{(i)}}^{2}}{2 \sigma^{2}}\right)
$$
where $l^{(i)} = x^{(i)}$. ==Do perform feature scaling before using the Gaussian kernel.==

## Logistic Regression vs. SVM

$n=$ number of features, $m=$ number of training examples.

If $n \gg m$: Use logistic regression, or SVM without a kernel.

If $n$ is small, $m$ is intermediate: Use SVM with Gaussian kernel.

If $n$ is small, $m$ is large: Add more features, then use logistic regression or SVM without a kernel.

# Clustering

## Hierarchical Clustering

- Agglomerative (bottom up): Each point is a cluster at first and then repeatedly combine the two "nearest" clusters into one.
- Divisive (top down): Start with one cluster and recursively split it.

To represent a cluster, for Euclidean case, we can simply use the average of points as the centroid. For non-Euclidean case, we can define clustroid to be the point "closest" to other points, where the "closest" can be measured in different ways.

To find nearest clusters, we can use the distance from the centroid/clustroid, or other measures like the minimum distance between two points from each cluster, the diameter of the merged cluster, or the average distance between points in the cluster.

Stop merging clusters when $k$ clusters are found (if we know the number of clusters), or criterion is met based on the merging criterion, or there is only 1 cluster left.

==The best choice depends on the shape of clusters.==

## $k$-Means Algorithm

Assumes Euclidean space.

- Randomly initialize $K$ cluster centroids $\mu_1, \dots, \mu_K$.
- Find the index $c^{(i)}$ of cluster centroid closest to point $x^{(i)}$.
- Update cluster centroids by averaging points assigned to each cluster.

Optimization objective:
$$
J = \frac{1}{m} \sum_{i=1}^m \norm{ x^{(i)} - \mu_{c^{(i)}} }^2
$$

To choose the value of $k$:

- Elbow method: try different $k$ and look at the changes in the average distance to centroid. As $k$ increases, the average falls rapidly until right $k$, then changes little. **A well-defined elbow is rarely seen in practice**.
- Silhouette method: the silhouette value is a measure of how similar an object is to its own cluster compared to other clusters.

Weakness:

- Sensitive to outliers and noise. Discover and eliminate them beforehand.
- Can only handle numerical data.
- Difficult to detect clusters with non-spherical shapes or widely different sizes/densities.

# Decision Tree

Decision trees can be used for classification or regression with a tree structure.

- Select attribute for root node.
- Split instances into subsets
- Repeat recursively for each branch, using only instances that reach the branch.

## Purity

To decide which attribute to split on, use **Information Gain** or **Gini Index** to measure the purity of the split.

Entropy = $-\sum_i p_i \log p_i$ measures the disorder or uncertainty.

### Information Gain

The difference of entropy after splitting. The higher, the better.
$$
\operatorname{IG}(S, A) = H(S) - \sum_{v\in A} \frac{|S_v|}{|S|} H(S_v)
$$
Use **Information Gain Ratio** instead to prevent "super attributes" being selected as root.
$$
\operatorname{IGR}(A) = \frac{\operatorname{IG}(A)}{\operatorname{IV}(A)} = \operatorname{IG}(A) \bigg/ -\sum_{v} \frac{|S_v|}{|S|} \log\left(\frac{|S_v|}{|S|}\right)
$$

### Gini Index

The smaller, the better.
$$
G_i = 1 - \sum_{k=1}^n p_{ik}^2
$$
where $p_{ik}$ is the ratio of class $k$ instances among the training instances in the $i$-th node

## Pruning

Change the model by deleting the child nodes of a branch node to prevent overfitting.

- Pre-pruning: stop the growth early if a split would result in the purity below a threshold.
- Post-pruning: remove non-significant branches form a fully grown tree. Replace subtree by a leaf node labeled with the most frequent class.

==Post-pruning is more successful in practice because it is not easy to precisely estimate when to stop growing the tree.==

# Ensemble Learning

Multiple learners are trained and combined to solve the same problem.

- Bagging (bootstrap aggregating): build several learners independently and then to average their predictions.
- Boosting: base learners are built sequentially and one tries to reduce the bias of the
  combined learner.

## Bagging

- Sampling **with replacement** from the training dataset.
- Train base learners on each sample separately.
- Average predictions from multiple base learners.
  - Majority voting for classification
  - Averaging for regression

Often used with tree, an extension is **random forest**.

## Boosting

Extremely useful in **computer vision**.

- Train a base learner on the entire dataset.
- Find the data that are incorrectly predicted and assigned it with more weight.
- Train the next learner on the weighted dataset.
- Repeat the process to sequentially train base learners.
- Combine the base learners using a weighted average. More weight to those with better performance.

**AdaBoost** computes the weight by
$$
\alpha_t = \frac{1}{2} \ln \left(\frac{1-\epsilon_t}{\epsilon_t}\right)
$$
where $\epsilon_t$ is the error rate.

# PCA

PCA (Principal Component Analysis) is the most popular algorithm in **dimensionality reduction**. It is widely used for

1. Data compression: Reduce memory. Speed up learning algorithm.
2. Data visualization.
3. Feature extraction.

==Before you implement PCA, first try running whatever you want to do with the original data/raw data. Only if that doesn't do what you want, then implement PCA.==

There are two points of view leading to the same result of PCA, one is to **minimize the reconstruction error** with SVD:

- Perform feature scaling/mean normalization to get $X$
- SVD: $X = U \Sigma V^T$
- The reduced PCA projections will be given by $Z = X V_{:, 1:k}$
- Reconstruct data by $\hat{X} = V_{:, 1:k}^T Z$

Here $V$ are **principal directions** and $XV = US$ are **principal components**.

The other is to **maximize the variance** of the projected data.

- Perform feature scaling/mean normalization to get $X$
- Compute the covariance matrix: $S = X^T X$
- Eigen decomposition: $S = V \Sigma^2 V$
- Same as SVD

# Gradient Descent

## Variants

- Batch gradient descent: Use all examples in each update. **Not suitable for huge datasets**.
- Stochastic gradient descent: Use 1 example in each update. **The randomness is good to escape from local optima**. **But it can never settle at the minimum**. One solution is to gradually reduce the learning rate.
- Mini-batch gradient descent: Take the best of both batch GD and SGD.

## Momentum

GD can get trapped in local minima or saddle points. Momentum helps accelerate SGD in the relevant direction and dampens oscillations.
$$
\begin{align*}
v_t &= \beta v_{t-1} + \eta \nabla_\theta J(\theta) \\
\theta_t &= \theta_{t-1} - v_t
\end{align*}
$$

- $v$: plays the role of velocity.
- $\beta$: plays the role of friction. Must be between 0 to 1, typical choice is about 0.9

## Adam

Adam (Adaptive Moment Estimation) automatically computes adaptive learning rates for each parameter.

- Compute gradient $g_t$

- Update biased 1st moment estimate
  $$
  m_{t} = \beta_{1} m_{t-1}+\left(1-\beta_{1}\right) g_{t}
  $$

- Update biased 2nd raw moment estimate
  $$
  v_{t} = \beta_{2} v_{t-1}+\left(1-\beta_{2}\right) g_{t}^{2}
  $$

- Update bias-corrected 1st moment estimate
  $$
  \hat{m}_{t} = \frac{m_{t}}{1-\beta_{1}^t}
  $$

- Update bias-corrected 2nd raw moment estimate
  $$
  \hat{v}_{t} = \frac{v_{t}}{1-\beta_{2}^t}
  $$

- Update parameters
  $$
  w_{t} =w_{t-1}-\eta \frac{\hat{m}_{t}}{\sqrt{\hat{v}_{t}}+\epsilon}
  $$

==Default choice to use, especially for sparse data sets.==

## Second-Order

- Quasi-Newton methods (BGFS most popular): Approximate inverse Hessian with rank 1 updates over time.
- L-BFGS: Does not form the full inverse Hessian. Usually works very well in full batch, deterministic mode.

# Evaluation

## Validation Set

Separate the dataset to training/validation/test sets, use the validation set for model selection and hyperparameter tuning.

### Hold-Out Validation

For relatively small dataset, split the data to $6:2:2$.

For big data set, split the data to $98:1:1$.

### K-Fold Cross Validation

For small dataset, divide the training set into $k$ equal size subsets. Each time, one of the $k$ subsets is used as the validation set. Repeat this process for $k$ times and average the errors.

## Hyperparameter Searching

- Grid search: Brutal force to search every combination of hyperparameters.
- Random search: Randomly sample and narrow the range. **More effective in high-dimensional spaces.**
- Bayesian Optimization:

## Over/Under Fitting

Plot the learning curves for test and validation sets for debugging.

- High Bias: Underfit. Both training/validation error will be high.
  - Get additional features
  - More complex model
  - Better optimization algorithm such as Adam
- Use ensemble learning — Boosting
  - Decrease $\lambda$

- High Variance: Overfit. Low training error, high validation error.
  - Get more training examples
  - Try smaller sets of features
  - Use ensemble learning — Bagging & Random Forest
  - Increase $\lambda$

## Metrics

### Classification

The **confusion matrix**:

![Confusion Matrix](https://i.imgur.com/A2oVjho.png)

- Accuracy:
  $$
  \frac{\text{TP + TN}}{\text{TP + FN + FP + TN}}
  $$

- Precision:
  $$
  \frac{\text{TP}}{\text{TP + FP}}
  $$

- Recall:
  $$
  \frac{\text{TP}}{\text{TP + FN}}
  $$

How to choose the trade-off between precision and recall depends on the actual problem.

Single metric:

- F score:
  $$
  F_1 = 2\frac{\text{Precision} \times \text{Recall}}{\text{Precision + Recall}}
  $$

- AUC (area under the curve): ROC curve plots False Positive Rate TP / (TP + FN) vs. True Positive Rate FP / (FP + TN) at different classification thresholds.

### Regression

- MAE (Mean Absolute Error): $\displaystyle \frac{1}{n} \sum_{i=1}^n |y_i - \hat{y}_i|$
- MSE (Mean Squared Error): $\displaystyle \frac{1}{n} \sum_{i=1}^n (y_i - \hat{y}_i)^2$
- RMSE (Root Mean Squared Error): $\displaystyle \frac{1}{n} \sqrt{\sum_{i=1}^n (y_i - \hat{y}_i)^2}$
- MAPE (Mean Absolute Percentage Error): $\displaystyle \frac{1}{n} \sum_{i=1}^n \left| \frac{y_i - \hat{y}_i}{y_i} \right|$
- $R^2$ (coefficient of determination): $\displaystyle \frac{\big( \sum (x_i - \bar{x}) (y - \bar{y})\big)^2}{\sum (x_i - \bar{x})^2 (y - \bar{y})^2} = \frac{\sum_i (\hat{y}_i - \bar{y})^2}{\sum_i (\hat{y}_i - \bar{y})^2 + \sum_i (\hat{y}_i - y_i)^2}$
- Adjusted $R^2$ (Prevent overfitting): $\displaystyle R_\text{adj}^2 = 1-\frac{(1-R^2) (n-1)}{n-k-1}$ where $n$ is the sample size and $k$ the total number of explanatory variables (not including the constant term).

lstm
