---
title: SLAM Basics
date: 2021-08-12
categories:
- Study
tags:
- Robotics
---

SLAM 笔记，基于[视觉 SLAM 十四讲](https://github.com/gaoxiang12/slambook2)

<!--more-->

---

SLAM (Simultaneous Localization and Mapping) 是指搭载特定传感器的主体，在没有环境先验信息的情况下，于运动过程中建立环境的模型，同时估计自己的运动。如果这里的传感器主要为相机，则称为「视觉 SLAM」

# 视觉 SLAM 基础

## 相机

- 单目：依靠相机的运动造成的视差来估计物体**相对**大小，与真实尺度相差一个因子
- 双目：能够探测的深度范围与基线（两个相机之间的距离）相关。但视差的计算非常消耗计算资源
- RGB-D：红外结构光或 ToF（Time-of-Flight），不需要通过软件计算来得到深度。但存在测量范围窄、噪声大、易受日光干扰、无法测量透射材质等问题，在 SLAM 中主要用于室内

## 框架

1. 传感器信息读取：主要为相机图像的读取和预处理，还可能包括 Odometer 和 IMU
2. 视觉里程计（Visual Odometry）：估算相邻图像间的运动，以及局部地图。又称为前端
3. 后端优化（Optimization）：接受不同时刻 VO 测量的相机位姿，以及回环检测的信息，对它们进行优化，得到全局一致的轨迹和地图
4. 回环检测（Loop Closing）：判断机器人是否到达过先前的位置，检测信息发送至后端进行处理
5. 建图（Mapping）：根据估计的轨迹，建立与任务要求对应的地图

Visual Odometry 一般被称为前端，它只关注局部运动信息，因此会造成 Accumulating Drift。前端给后端提供待优化的数据，后端的任务即是从这些带噪声的数据中估计整个系统的状态。在视觉 SLAM 中，前端和 Compute Vision 更加相关，比如**图像的特征提取和匹配**等，后端则主要是**滤波与非线性优化算法**。

地图的形式随 SLAM 的应用场合而定。大体上可分为**度量地图**和**拓扑地图**

- 度量地图（Metric Map）：精确表示地图中物体的位置关系，通常用稀疏和稠密进行分类。稀疏地图使用路标建模，可以满足定位需要。导航则需要稠密地图，通过分辨率对空间做划分，2D 时为 Grid，3D 时为 Voxel，比较耗费存储空间。
- 拓扑地图（Topological Map）：强调地图元素之间的关系。是一个由节点和边组成的 Graph。不擅长表达具有复杂结构的地图。

## 数学模型

假设机器人在 $k$ 时刻的位置为 $x_k$，运动传感器的读数为 $u_k$，地图由 $n$ 个路标表示 $y_1, \dots, y_n$，在 $x_k$ 位置观测到路标 $y_j$ 的数据记为 $z_{k,j}$。建立如下的**运动方程**和**观测方程**：
$$
\begin{gather*}
x_k = f(x_{k-1}, u_k, w_k) \\
z_{k,j} = h(y_j, x_k, v_{k,j})
\end{gather*}
$$
其中 $w_k$ 和 $v_{k,j}$ 表示噪声。则 SLAM 问题可以表示为在已知运动测量的读数 $u$ 及传感器读数 $z$ 时，如何求解定位问题 $x$ 和建图问题 $y$？

根据运动和观测方程是否线性，噪声是否服从高斯分布，该系统可以分为**线性/非线性**和**高斯/非高斯**。其中最简单的是线性高斯系统，它的无偏最优估计可以由 Kalman Filter 给出。在非线性非高斯系统中，主要有 Extended Kalman Filter 和非线性优化两大类方法。当今主流视觉 SLAM 使用以 Graph Optimization 为代表的优化技术进行状态估计，优化技术已经明显优于滤波器技术。

# 三维空间刚体运动

参考 [Robotic Manipulation](https://silencial.github.io/robotic-manipulation) Rigid Body Motion 一章，主要包括「旋转矩阵」、「欧拉角」、「四元数」等概念。

# 李群与李代数

考虑如下问题：机器人观测到了一个世界坐标位于 $p$ 的点，则观测方程为
$$
z = Tp + w
$$
其中 $w$ 为噪声。假设一共有 $N$ 个路标，则对机器人的位姿估计可以表达为
$$
\require{mathtools}
\DeclarePairedDelimiters\norm{\lVert}{\rVert}
\min_{T} \sum_{i=1}^N \norm*{z_i - Tp_i}_2^2
$$
为了求解这样的优化问题，我们需要对位姿进行求导。但 $SO(3), SE(3)$ 只是群，并没有良好定义的加法。如果我们将 $T$ 当成普通矩阵来处理，则必须要添加约束。而使用李代数则可以完美解决这一问题。

## BCH 近似公式

令 $\phi_1, \phi_2 \in so(3)$，由 BCH 近似公式给出：
$$
\ln \Big(\exp \left(\phi_{1}^{\wedge}\right) \exp \left(\phi_{2}^{\wedge}\right)\Big)^{\vee} \approx
\begin{cases}
J_{l}\left(\phi_{2}\right)^{-1} \phi_{1}+\phi_{2} & \text {当 } \phi_{1} \text { 为小量} \\
J_{r}\left(\phi_{1}\right)^{-1} \phi_{2}+\phi_{1} & \text {当 } \phi_{2} \text { 为小量}
\end{cases}
$$
其中
$$
\begin{gather*}
J_{l}(\phi) = \frac{\sin \theta}{\theta} I+\left(1-\frac{\sin \theta}{\theta}\right) a a^{\mathrm{T}}+\frac{1-\cos \theta}{\theta} a^{\wedge} \\
J_r(\phi) = J_l(-\phi)
\end{gather*}
$$
$\phi = \theta a$，$\norm{a} = 1$

## 李代数求导

为了计算旋转之后的坐标 $Rp$ 相对于旋转 $R$ 的导数，设 $R$ 对应的李代数为 $\phi$ 并计算：
$$
\begin{split}
\frac{\partial (\exp(\phi^\wedge)p)}{\partial \phi} &= \lim_{\delta\phi \to 0} \frac{\exp((\phi + \delta\phi)^\wedge)p - \exp(\phi^\wedge)p}{\delta \phi} \\
&= \lim_{\delta\phi \to 0} \frac{\exp((J_l \delta\phi)^\wedge) \exp(\phi^\wedge)p - \exp(\phi^\wedge)p}{\delta \phi} \\
&= \lim_{\delta\phi \to 0} \frac{(I +(J_l \delta\phi)^\wedge) \exp(\phi^\wedge)p - \exp(\phi^\wedge)p}{\delta \phi} \\
&= \lim_{\delta\phi \to 0} \frac{-(\exp(\phi^\wedge)p)^\wedge J_l \delta\phi}{\delta \phi} \\
&= -(Rp)^\wedge J_l
\end{split}
$$

## 扰动模型

另一种求导方式是对 $R$ 进行一次扰动 $\Delta R$，看结果相对于扰动的变化率。这个扰动可以乘在左边或右边，以左扰动为例，设 $\Delta R$ 对应的李代数为 $\varphi$，则有
$$
\begin{split}
\frac{\partial (Rp)}{\partial \varphi} &= \lim_{\varphi \to 0} \frac{\exp(\varphi^\wedge) \exp(\phi^\wedge)p - \exp(\phi^\wedge)p}{\varphi} \\
&= \lim_{\varphi \to 0} \frac{(I + \varphi^\wedge) \exp(\phi^\wedge)p - \exp(\phi^\wedge)p}{\varphi} \\
&= \lim_{\varphi \to 0} \frac{-(Rp)^\wedge \varphi}{\varphi} \\
&= -(Rp)^\wedge
\end{split}
$$
相比于李代数求导，省去了 $J_l$ 的计算，使得扰动模型更加实用。

下面直接给出 $SE(3)$ 上的扰动模型：
$$
\begin{equation}\label{dlie}
\begin{split}
\frac{\partial(T p)}{\partial \xi} &= \lim_{\delta \xi \to 0} \frac{\exp (\delta \xi^{\wedge}) \exp (\xi^{\wedge}) p - \exp (\xi^{\wedge}) p}{\delta \xi} \\
&= \lim_{\delta \xi \to 0} \frac{\delta \xi^{\wedge} \exp (\xi^{\wedge}) p}{\delta \xi} \\
&= \lim_{\delta \xi \to 0} \frac{\begin{bmatrix} \delta\phi^\wedge & \delta \rho \\ 0 & 0 \end{bmatrix} \begin{bmatrix} Rp + t \\ 1 \end{bmatrix}}{\delta \xi} \\
&= \lim_{\delta \xi \to 0} \frac{\begin{bmatrix} \delta\phi^\wedge (Rp + t) + \delta \rho \\ 0 \end{bmatrix}}{[\delta \rho, \delta\phi]^T} \\
&= \begin{bmatrix} I & -(Rp + t)^\wedge \\ 0 & 0 \end{bmatrix} \\
&\triangleq (T p)^{\odot}
\end{split}
\end{equation}
$$

$\odot$ 将一个齐次坐标的空间点变换成一个 $4\times 6$ 的矩阵

## 轨迹误差

绝对轨迹误差：每个位姿李代数的 RMSE
$$
\mathrm{ATE}_{\mathrm{all}}=\sqrt{\frac{1}{N} \sum_{i=1}^{N}\norm*{\log (T_{\text{gt}, i}^{-1} T_{\text{esti}, i})^{\vee} }_{2}^{2}}
$$
绝对平移误差：只考虑平移部分
$$
\mathrm{ATE}_{\text{trans}}=\sqrt{\frac{1}{N} \sum_{i=1}^{N}\norm*{\operatorname{trans} (T_{\mathrm{gt}, i}^{-1} T_{\mathrm{esti}, i}) }_{2}^{2}}
$$

# 相机与图像

## 针孔相机模型

世界坐标 -> 相机坐标 -> 成像坐标 -> 像素坐标

### 相机坐标 -> 成像坐标

![针孔相机模型](https://i.imgur.com/AUFBQNy.png)

根据针孔模型：
$$
\frac{Z}{f} = -\frac{X}{X'} = -\frac{Y}{Y'}
$$
其中 $f$ 为平面到小孔的距离（焦距），负号表示成像倒立。为方便起见，去掉负号，整理得
$$
\begin{gather*}
X' = f \frac{X}{Z} \\
Y' = f \frac{Y}{Z}
\end{gather*}
$$

### 相机坐标 -> 像素坐标

像素坐标 $[u, v]$ 定义方式为：原点 $o'$ 位于图像的左上角，$u$ 轴向右与 $x$ 轴平行，$v$ 轴向下与 $y$ 轴平行。因此像素坐标系和成像坐标之间，相差了一个缩放和平移。设像素坐标在 $u$ 轴和 $v$ 轴上分别缩放了 $\alpha$，$\beta$ 倍，原点平移了 $[c_x, c_y]$，则
$$
\begin{gather*}
u = \alpha X' + c_x \\
v = \beta Y' + c_y
\end{gather*}
$$
联系 相机坐标 -> 成像坐标 的变换，令 $f_x = \alpha f$，$f_y = \beta f$，并使用齐次坐标，则可以得到 相机坐标 -> 像素坐标 的变换：
$$
\begin{pmatrix}
u \\
v \\
1
\end{pmatrix}=\frac{1}{Z}\begin{pmatrix}
f_{x} & 0 & c_{x} \\
0 & f_{y} & c_{y} \\
0 & 0 & 1
\end{pmatrix}\begin{pmatrix}
X \\
Y \\
Z
\end{pmatrix} \triangleq \frac{1}{Z} KP
$$
其中 $K$ 称为**相机内参**

### 世界坐标 -> 像素坐标

在上式中，$P$ 是在相机坐标系下坐标，应该由它的世界坐标 $P_w$ 根据相机的位姿 $R, t$ 变换得到。综合上式可得 世界坐标 -> 像素坐标 的变换：
$$
P_{uv} = \begin{pmatrix}
u \\ v \\ 1
\end{pmatrix} = \frac{1}{Z}K(RP_w + t)
$$

## 畸变

由相机透镜的形状引起的畸变称为**径向畸变**。在实际拍摄的照片中，摄像机的透镜往往使得真实环境中的一条直线在图片中变成了曲线。越靠近图像的边缘，这种现象越明显。由于实际加工制作的透镜往往是中心对称的，因此不规则的畸变通常径向对称。它们主要分为两大类：**桶形畸变**和**枕形畸变**。通常假设这些畸变呈多项式关系：
$$
\begin{gather*}
x_{\text{distorted}}=x\left(1+k_{1} r^{2}+k_{2} r^{4}+k_{3} r^{6}\right) \\
y_{\text{distorted}}=y\left(1+k_{1} r^{2}+k_{2} r^{4}+k_{3} r^{6}\right)
\end{gather*}
$$
由相机组装过程中透镜不能和成像面严格平行引起的畸变称为**切向畸变**。由下式纠正：
$$
\begin{gather*}
x_{\text{distorted}}=x+2 p_{1} x y+p_{2}\left(r^{2}+2 x^{2}\right) \\
y_{\text{distorted}}=y+p_{1}\left(r^{2}+2 y^{2}\right)+2 p_{2} x y
\end{gather*}
$$

其中 $r=\sqrt{x^2 + y^2}$

## 单目相机总结

1. 世界座标系下有一个固定点 $P$，世界坐标为 $P_w$
2. 相机的运动由 $R,t$ 描述。$P$ 的相机坐标为 $\tilde{P}_c = RP_w +t$
3. 令 $\tilde{P}_c = [X, Y, Z]$，投影至归一化平面 $Z=1$ 上，得到 $P$ 的归一化坐标 $P_c = [X/Z, Y/Z, 1]$（需要检查 $Z > 0$）
4. 有畸变时，根据畸变参数计算 $P_c$ 发生畸变后的坐标
5. 最后，$P$ 的归一化坐标经过内参后，对应到它的像素坐标：$P_{uv} = KP_c$

单目相机无法确定空间点的具体位置，因为从相机光心到归一化平面连线上的所有点，都可以投影至该像素上。只有当 $P$ 的深度确定时（通过双目或 RGB-D 相机），才能确定它的空间位置。

## 双目相机模型

通过同步采集不同相机的图像，计算图像视差，来估计每一个像素的深度。

考虑两个水平放置的相机：

![双目相机模型](https://i.imgur.com/FaR8Gpg.png)

其中两个光圈的距离称为双目相机的**基线**，记为 $b$。考虑一个空间点 $P$，它在左右相机成像坐标分别为 $P_L, P_R$。理想情况下，由于左右相机只在 $x$ 轴上有位移，因此 $P$ 的像也只在 $x$ 轴上有差异。记它的左右侧坐标分别为 $u_L, u_R$，则有
$$
\frac{z-f}{z}=\frac{b-u_{L}+u_{R}}{b}
$$

整理可得
$$
z=\frac{f b}{d}, \quad d \triangleq u_{L}-u_{R}
$$
其中 $d$ 称为**视差**。由于视差最小为一个像素，因此双目的深度存在一个理论上的最大值，由 $fb$ 确定。同时可以看到基线 $b$ 越大，双目能测到的最大距离就会越远。

实际运用中，视差 $d$ 的计算需要知道某个像素在左右图像的对应位置，计算量较大，一般需要 GPU 或 FPGA 来实现实时计算。

## RGB-D 相机模型

根据原理可分为两大类：

1. Structured Light：根据返回的结构光图案计算物体与自身的距离
2. Time-of-Flight：发射脉冲光，根据发送到返回的时间计算物体与自身的距离

RGB-D 相机通常自己会完成深度与彩色图像素之间的配对，输出一一对应的彩色图和深度图。我们计算图像的 3D 相机坐标并生成点云。

# 非线性优化

## 状态估计问题

回顾 SLAM 模型中的运动方程和观测方程：
$$
\begin{gather*}
x_k = f(x_{k-1}, u_k) + w_k \\
z_{k,j} = h(y_j, x_k) + v_{k,j}
\end{gather*}
$$
其中 $x_k$ 为相机位姿，运动方程在 SLAM 中没有特殊性，观测方程由上一章的相机模型给出，$w_k, v_{k,j}$ 为噪声项。

通常假设 $w_k, v_{k,j}$ 满足如下高斯分布：
$$
w_k \sim \mathcal{N}(0, R_k), \quad v_k \sim \mathcal{N}(0, Q_{k,j})
$$
状态估计问题即是求如下的概率分布：
$$
P(x,y | z,u)
$$
处理状态估计问题的方法大致分为

1. 增量法/滤波器：由于在 SLAM 中数据是随时间逐渐到来的，因此我们应该有一个当前时刻的估计状态，并用新的数据来更新它
2. 批量法/优化：将大量数据收集起来一并处理

由于批量法可以在更大的范围达到最优化，被认为优于传统的滤波器，是当前视觉 SLAM 的主流方法。由于 SLAM 对实时性的要求，通常仅对当前时刻附近的一些轨迹进行优化，称为**滑动窗口估计法**。

## 最大似然估计

对状态估计问题使用贝叶斯法则：
$$
\underbrace{P(x, y | z, u)}_{\text{后验}}= \frac{P(z, u | x, y) P(x, y)}{P(z, u)} \propto \underbrace{P(z, u | x, y)}_{\text{似然}} \ \underbrace{P(x, y)}_{\text{先验}}
$$

**MAP** (Maximize a Posterior)：求一个最优状态估计，使得后验概率最大
$$
\DeclareMathOperator*{\argmin}{arg\,min\,}
\DeclareMathOperator*{\argmax}{arg\,max\,}
(x, y)^*_{\text{MAP}} = \argmax P(x,y | z, u) = \argmax P(z,u | x,y) P(x,y)
$$
**MLE** (Maximize Likelihood Estimation)：在没有先验的情况下求最优状态估计，使得似然最大
$$
(x, y)^*_{\text{MLE}} = \argmax P(z,u | x,y)
$$
假设各个时刻的输入和观测相互独立，则联合分布可以被分解为
$$
P(z,u|x,y) = \prod_k P(u_k|x_{k-1}, x_k) \prod_{k,j} P(z_{k,j}|x_k, y_j)
$$
定义误差项：
$$
\begin{gather*}
e_{u,k} = x_k - f(x_{k-1}, u_k) \\
e_{z,j,k} = z_{k,j} - h(x_k, y_j)
\end{gather*}
$$
则最大似然估计变为如下的最小二乘问题
$$
\min_{x,y} \left( \sum_k e_{u,k}^T R_k^{-1} e_{u,k} + \sum_{k,j} e_{z,k,j}^T Q_{k,j}^{-1} e_{z,k,j} \right)
$$

# 视觉里程计

## 特征点法

VO 的核心问题是如何根据图像来估计相机运动。比较方便的做法是：首先从图像中选取比较有代表性的点。这些点在相机视角发生少量变化后会保持不变，于是我们能在不同的图像中定位到相同的点。然后在此基础上讨论相机位姿估计问题。

由于识别的精确性，通常选用图像的角点作为特征点。但朴素的角点提取算法不能满足 SLAM 的需求，所以在此基础上设计了很多更加稳定的局部图像特征，例如 SIFT，SURF，ORB 等等。它们主要有如下性质：

1. Repeatability：相同的特征可以在不同的图像中找到
2. Distinctiveness：不同的特征有不同的表达
3. Efficiency：同一图像中，特征点的数量应远小于像素的数量
4. Locality：特征仅与一小片图像区域有关

特征点由**关键点**和**描述子**两部分组成：

- 关键点：指特征点在图像中的位置，也可以具有朝向、大小等信息
- 描述子：用来描述关键点周围像素信息的向量。当两个特征点的描述子距离相近，则可以认为它们是同一特征点

特征点法的缺点：

1. 关键点的提取和描述子的计算非常耗时
2. 只使用特征点丢弃了大部分可能有用的图像信息
3. 在特征确实的地方可能找不到足够的匹配点来计算相机运动

## ORB

ORB (Oriented FAST and Rotated BRIEF) 是一种特征点，提取 ORB 特征有如下两个步骤：

1. FAST 角点提取：ORB 在 FAST 角点基础上计算了特征点的主方向，为后续的 BRIEF 描述子增加了旋转不变性
2. BRIEF 描述子：对前一步提取出特征点的周围图像区域进行描述

### FAST 角点

**FAST** 是一种角点，主要检测局部像素灰度变化明显的地方。由于只需要比较像素亮度的大小，因此速度较快。检测流程如下：

1. 在图像中选取像素 $p$，假设亮度为 $I_p$
2. 设置亮度阈值 $T$，例如 $I_p$ 的 $20\%$
3. 以 $p$ 为中心，选取半径为 $3$ 的圆上的 $16$ 个像素点
4. 如果其中有 $N$ 个点的亮度大于 $I_p + T$ 或小于 $I_p - T$，则 $p$ 可以被认为是特征点（$N$ 通常取 $12$）
5. 对每一个像素执行上述操作

在 FAST-12 算法中，可以添加预测试操作来快速排除不是角点的像素。即对于每个像素，检测领域圆上的第 $1$, $5$, $9$, $13$ 像素的亮度，只有当其中至少 $3$ 个像素同时大于 $I_p + T$ 或小于 $I_p - T$ 时，才继续检测，否则直接排除。此外，为了避免角点集中问题，需要在第一遍检测后使用 **Non-maximal suppression**，在一定区域内仅保留响应极大值的角点。

ORB 在 FAST 基础上添加了**尺度不变性**和**旋转不变性**：

- 尺度不变性：构建 Image Pyramid（对图像进行不同层次的降采样，得到不同分辨率的图片），并在每一层上进行角点检测
- 旋转不变性：使用 Intensity Centroid

Intensity Centroid 的具体实现：

1. 定义图像块 $B$ 的矩为 $m_{pq} = \sum_{x,y\in B} x^p y^q I(x,y), \quad p,q=\{0, 1\}$
2. 则图像块的质心为 $C= (m_{10}/m_{00}, m_{01}/m_{00})$
3. 连接图像块的几何中心和质心，得到一个方向向量，则特征点的方向可以定义为 $\theta = \arctan(m_{01}/m_{10})$

### BRIEF 描述子

BRIEF 是一种**二进制**描述子，其描述向量由许多 $0$ 和 $1$ 组成，编码了关键点附近两个随机像素 $p, q$ 的大小关系。由于 ORB 在 FAST 特征点提取阶段计算了特征点的方向，因此可以利用这一信息计算旋转之后的 BRIEF 描述子。

对于二进制描述子，一般使用 Hamming distance 计算距离，来进行特征点在不同图像中的匹配。当特征点数量很大时，暴力匹配的运算量会变得过大，此时一般使用 **FLANN** (Fast Library for Approximate Nearest Neighbors) 算法。

## 相机运动

在不同图像中有了匹配好的点对之后，需要根据这一信息来估计相机的运动。根据相机的种类，匹配有以下几种：

- 单目相机：根据两组 2D 点估计运动
- 双目/RGBD：有距离信息，根据两组 3D 点估计运动
- 混合：一组 3D，一组 2D

### 2D-2D：对极几何

![对极几何约束](https://i.imgur.com/Y59ilXL.png)

如图，空间一点 $P$ 在 $I_1, I_2$ 相机平面成像位置分别为 $p_1, p_2$。两个相机中心点分别为 $O_1, O_2$。设 $I_1$ 到 $I_2$ 的运动为 $R, t$。

在 $I_1$ 坐标系下，设 $P$ 的空间位置为 $[X, Y, Z]^T$，根据针孔模型我们有（使用齐次坐标）
$$
\begin{gather*}
p_1 \simeq KP \\
p_2 \simeq K(RP + t)
\end{gather*}
$$
其中 $\simeq$ 表示在齐次坐标下的相等。取 $x_1=K^{-1}p_1$，$x_2 = K^{-1}p_2$，则有
$$
x_2 \simeq Rx_1 + t
$$
两边同时左乘 $x_2^T t^\wedge$：
$$
x_2^T t^\wedge x_2 \simeq x_2^T t^\wedge R x_1
$$
由于左式为 $0$，$\simeq$ 可以变为 $=$。重新代入 $p_1, p_2$：
$$
p_2^T K^{-T} t^\wedge R K^{-1} p_1 = 0
$$
该式称为**对极约束**，几何意义为 $O_1, P, O_2$ 三点共面。

令本质矩阵 $E = t^\wedge R$，则相机位姿求解可分解为以下两步：

1. 根据配对点的像素位置求 $E$
2. 根据 $E$ 求出 $R, t$

#### 本质矩阵

- 根据 $E=t^\wedge R$，可以证明，$E$ 的奇异值必定是 $[\sigma, \sigma, 0]$ 的形式
- 由于平移和旋转各有三个自由度，$E$ 共有 $6$ 个自由度。但由于尺度等价性，$E$ 实际上有 $5$ 个自由度

我们最少可以通过 5 对点来求解 $E$，但这样会引入非线性方程。由于实际中得到的点对数量很多，我们可以只考虑 $E$ 的尺度等价性，使用 **8 点法**来估计 $E$。

考虑一对匹配点的归一化坐标为 $x_1 = [u_1, v_1, 1]^T$，$x_2 = [u_2, v_2, 1]^T$。根据对极约束：
$$
\begin{bmatrix}
u_{2} \\ v_{2} \\ 1
\end{bmatrix}^T
\begin{bmatrix}
e_{1} & e_{2} & e_{3} \\
e_{4} & e_{5} & e_{6} \\
e_{7} & e_{8} & e_{9}
\end{bmatrix}
\begin{bmatrix}
u_{1} \\
v_{1} \\
1
\end{bmatrix}
= 0
$$
将 $E$ 展开为列向量 $e$，则对极约束可以表示为
$$
\left[u_{2} u_{1}, u_{2} v_{1}, u_{2}, v_{2} u_{1}, v_{2} v_{1}, v_{2}, u_{1}, v_{1}, 1\right] \cdot e=0
$$
$8$ 对点即对应 $8$ 个线性方程，求解该方程即可以得到 $E$。由于该方法得到的矩阵可能不满足奇异值为 $[\sigma, \sigma, 0]$ 的性质，一般会将其进行 SVD 分解后投影至 $E$ 所在的流形上。设投影前 $E=U \operatorname{diag}(\sigma_1, \sigma_2, \sigma_3) V^T$，则投影为（由于 $E$ 的尺度等价性，可以将奇异值矩阵直接取为 $\operatorname{diag}(1, 1, 0)$）
$$
E = U \operatorname{diag}(1, 1, 0) V^T
$$
求解出 $E$ 后，需要恢复出 $R, t$。对于任意 $E=U\Sigma V^T$，存在两个可能的 $R,t$ 对应（假设 $\norm{t} = 1$）：
$$
\begin{gather*}
t_1^\wedge = U R_Z \left(\frac{\pi}{2}\right) \Sigma U^T, \quad R_1 = U R_Z^T \left(\frac{\pi}{2}\right) V^T \\
t_2^\wedge = U R_Z \left(-\frac{\pi}{2}\right) \Sigma U^T, \quad R_2 = U R_Z^T \left(-\frac{\pi}{2}\right) V^T
\end{gather*}
$$
其中 $R_Z(\frac{\pi}{2})$ 表示沿 $Z$ 轴旋转 $\pi/2$ 得到的旋转矩阵。同时对任意 $t$ 取负号也满足。因此可以得到四个可能的解，但只有其中一个解能够满足 $P$ 点在两个相机坐标系下深度都为正。

#### 三角测量

在求得 $R, t$ 后，还需要估计特征点的空间位置，即已知 $x_1, x_2, R, t$ 时，通过
$$
s_1 x_2 = s_2 Rx_2 + t
$$
求解两个特征点的深度 $s_1, s_2$。对上式左乘 $x_1^\wedge$：
$$
s_2x_1^\wedge Rx_2 + x_1^\wedge t = 0
$$
可以求得 $s_2$，代回原式即可得 $s_1$。由于噪声的存在，一般会求最小二乘解。

#### 实际问题

**尺度不确定性**：在从 $E$ 恢复 $R, t$ 的过程中，对 $t$ 的长度进行了归一化，这表明了单目视觉的尺度不确定性。

**初始化**：单目相机需要在初始化时固定尺度，例如对 $t$ 的归一化。另一种方法是令初始化时所有特征点的平均深度为 $1$，可以控制场景的规模大小，使计算在数值上更稳定。单目初始化不能只有纯旋转。

**多于八对点**：计算一个最小二乘解，即 $\min_e \norm{Ae}_2^2$。不过因为可能存在误匹配的情况，通常会使用**随机采样一致性（RANSAC）**

### 2D-3D：PnP

PnP (Perspective-n-Point) 是求解 3D 到 2D 点对运动的方法：当知道了 $n$ 个 3D 空间点及其投影位置时，如何估计相机的位姿。

#### 直接线性变换

考虑空间一点 $P=(X, Y, Z, 1)$，在图像 $I_1$ 中投影到特征点 $x_1=(u_1, v_1, 1)$。定义增广矩阵 $T=[R|t]$，则有
$$
s\begin{pmatrix}
u_{1} \\
v_{1} \\
1
\end{pmatrix}=T\begin{pmatrix}
X \\
Y \\
Z \\
1
\end{pmatrix}
$$
定义 $t_i$ 为 $T$ 的行向量，消去 $s$ 后可得到两个关于 $t$ 的线性约束：
$$
\begin{gather*}
t_1^T P - t_3^T P u_1 = 0 \\
t_2^T P - t_3^T P v_1 = 0
\end{gather*}
$$
因此最少可以通过 $6$ 对点实现 $T$ 的线性求解，这种方法称为 DLT (Direct Linear Transform)。当匹配点大于 $6$ 对时，也可以使用 SVD 等方法对超定方程求最小二乘解。

在 DLT 求解中，我们直接将 $T$ 矩阵看成了 $12$ 个未知数，因此求出的矩阵需要投影至 $SO(3)$ 流形上：
$$
R \gets (RR^T)^{-\frac{1}{2}} R
$$

#### P3P

![P3P](https://i.imgur.com/mIlh5MR.png)

如图，ABC 为 3D 点，abc 为对应相机成像平面上的投影，O 为相机光心。注意我们知道的是 ABC 在世界坐标系中的坐标，一旦 3D 点在相机坐标系下的坐标能够算出，我们就得到了 3D-3D 的对应点，将 PnP 问题转换为了 ICP 问题。

考虑三角形 $Oab-OAB$，$Obc-OBC$，$Oac-OAC$，有如下关系：
$$
\begin{gather*}
O A^{2}+O B^{2}-2 O A \cdot O B \cdot \cos \langle a, b\rangle=A B^{2} \\
O B^{2}+O C^{2}-2 O B \cdot O C \cdot \cos \langle b, c\rangle=B C^{2} \\
O A^{2}+O C^{2}-2 O A \cdot O C \cdot \cos \langle a, c\rangle=A C^{2}
\end{gather*}
$$
对以上三式全体除以 $OC^2$，并记 $x=OA/OC$，$y=OB/OC$，$u=BC^2/AB^2$，$w=AC^2/AB^2$，则有
$$
\begin{gather*}
(1-u) y^{2}-u x^{2}-\cos \langle b, c\rangle y+2 u x y \cos \langle a, b\rangle+1=0 \\
(1-w) x^{2}-w y^{2}-\cos \langle a, c\rangle x+2 w x y \cos \langle a, b\rangle+1=0
\end{gather*}
$$
其中 $\cos\langle a, b \rangle, \cos\langle b, c \rangle, \cos\langle a, c \rangle$ 可以根据 2D 图像的坐标算出。$u, v$ 可以根据世界坐标系下的坐标算出，变换到相机坐标系下后，该比值不改变。而 $x, y$ 为未知量。因此该方程组是一个关于 $x, y$ 的一个二元二次方程，该方程最多可能得到 $4$ 个解，可以使用验证点来计算最可能的解。

P3P 只利用 $3$ 个点的信息，难以利用更多的信息，并且容易受噪声影响。在 SLAM 中，通常的做法是先使用 P3P 估计相机位姿，然后构建最小二乘优化问题对估计值进行调整。

#### 最小化重投影误差

使用非线性优化法，将相机位姿和空间点位置同时看成优化变量。从空间点 $P_i$ 到投影坐标 $u_i$ 的关系为：
$$
s_i u_i = KTP_i
$$
最小二乘问题构建如下：
$$
T^* = \argmin_T \frac{1}{2} \sum_{i=1}^n \norm*{ u_i - \frac{1}{s_i} KTP_i }^2
$$
为了使用高斯牛顿法等优化算法进行求解，需要知道误差项对位姿和空间点的导数，即
$$
\begin{gather*}
\frac{\partial e}{\partial \xi} = \frac{\partial e}{\partial P'} \frac{\partial P'}{\partial \xi} \\
\frac{\partial e}{\partial P} = \frac{\partial e}{\partial P'} \frac{\partial P'}{\partial P}
\end{gather*}
$$
其中 $P' = TP$。根据相机投影模型可得
$$
\frac{\partial e}{\partial P'}=-\begin{bmatrix}
\frac{\partial u}{\partial X'} & \frac{\partial u}{\partial Y'} & \frac{\partial u}{\partial Z'} \\
\frac{\partial v}{\partial X'} & \frac{\partial v}{\partial Y'} & \frac{\partial v}{\partial Z'}
\end{bmatrix}=-\begin{bmatrix}
\frac{f_{x}}{Z'} & 0 & -\frac{f_{x} X'}{ {Z'}^2} \\
0 & \frac{f_{y}}{Z'} & -\frac{f_{y} Y'}{ {Z'}^2}
\end{bmatrix}
$$
取 $\eqref{dlie}$ 前三行可得
$$
\frac{\partial P'}{\partial \xi} = \begin{bmatrix}
I & -{P'}^\wedge
\end{bmatrix}
$$
最后 $\frac{\partial P'}{\partial P} = R$

### 3D-3D：ICP

假设有一组匹配好的 3D 点：
$$
P = \{p_1, \dots, p_n\}, \quad P' = \{p_1', \dots, p_n'\}
$$
需要寻找 $R, t$ 使得
$$
p_i = Rp_i' + t \quad \forall i
$$

#### SVD

构建最小二乘问题：
$$
\min_{R, t}\frac{1}{2} \sum_{i=1}^n \norm{p_i - (Rp_i + t)}^2
$$
定义两组点的质心：
$$
p = \frac{1}{n}\sum_{i=1}^n p_i, \quad p' = \frac{1}{n}\sum_{i=1}^n p_i'
$$
则有
$$
\begin{split}
\frac{1}{2} \sum_{i=1}^{n}\norm*{p_{i}-\left(R p_{i}^{\prime}+t\right)}^{2}=& \frac{1}{2} \sum_{i=1}^{n}\norm*{\left(p_{i}-p-R\left(p_{i}^{\prime}-p^{\prime}\right)\right)+\left(p-R p^{\prime}-t\right)}^{2} \\
=& \frac{1}{2} \sum_{i=1}^{n}\left(\norm*{p_{i}-p-R\left(p_{i}^{\prime}-p^{\prime}\right)}^{2}+\norm*{p-R p^{\prime}-t}^{2}\right)
\end{split}
$$

求解步骤如下：

1. 计算两组点的质心位置 $p, p'$，然后计算每个点的去质心坐标：$q_i = p_i - p$，$q_i'=p_i'-p'$
2. 计算旋转矩阵：$R^*=\argmin_R \frac{1}{2} \sum_{i=1}^n \norm{q_i - Rq_i'}^2$
3. 计算 $t$：$t^* = p-Rp'$

展开关于旋转矩阵的计算：
$$
\sum_{i=1}^n \norm{q_i - Rq_i'}^2 = \sum_{i=1}^n (q_i^T q_i + q_i'^T R^T R q_i' - 2q_i^T R q_i')
$$

第一、二项与 $R$ 无关，因此优化目标函数变为
$$
-\sum_{i=1}^n q_i^T R q_i' = -\sum_{i=1}^n \operatorname{tr}(R q_i' q_i^T) = -\operatorname{tr}\left(R \sum_{i=1}^n q_i' q_i^T \right)
$$

定义 $W = \sum_{i=1}^n q_i q_i'^T$，对其进行 SVD 分解 $W=U\Sigma V^T$，当 $W$ 满秩时，$R$ 为
$$
R=UV^T
$$

若此时 $R$ 行列式为负，则取 $-R$ 作为最优值

#### 非线性优化

与 PnP 中的最小化重投影误差方法类似：
$$
\min_\xi \frac{1}{2} \sum_{i=1}^n \norm{p_i - \exp(\xi^\wedge) p_i'}^2
$$

## 直接法

直接法是视觉里程计另一主要分支，与特征点法有很大不同。

特点：

1. 只要求有像素梯度，不需要特征点
2. 由于优化算法依赖于像素的灰度梯度，有强烈的非凸性，只有当运动很小时才能成功。可以引入图像金字塔减小影响
3. 灰度不变假设是很强的假设，实际使用中会同时估计相机的曝光参数

### 光流法

光流是一种描述像素随时间在图像之间运动的方法。和特征点法相比，只是将匹配描述子替换为了光流追踪。

**灰度不变假设**：记 $t$ 时刻位于 $(x,y)$ 处的像素灰度值为 $I(x,y,t)$，设 $t+dt$ 时刻像素运动至 $(x+dx, y+dy)$，则有 $I(x+dx, y+dy, t+dt) = I(x, y, t)$

泰勒展开并保留一阶项，记 $I_x =\partial I / \partial x$，$u=dx/dt$，$v=dy/dt$ 可得：
$$
\begin{bmatrix}
I_x & I_y
\end{bmatrix}
\begin{bmatrix}
u \\ v
\end{bmatrix}
= -I_t
$$
在此基础上，Lucas-Kanade 光流假设某一个窗口内的像素具有相同的运动。因此对于窗口内每一个像素，我们都有上面一组线性方程。最后求最小二乘解即可算出 $u, v$。

光流法也可以看成一个优化问题：
$$
\min_{dx, dy} \norm{I_1(x,y) - I_2(x+dx, y+dy)}^2
$$
对应的 Jacobian 可以用第一个图像的梯度来代替，因此每次迭代只需要更新残差。

当相机运动较快，图像差异明显时，该优化问题容易达到一个局部最小值。可以通过引入**图像金字塔**来解决这一问题。即对同一个图像进行缩放，得到不同分辨率下的图像。计算光流时由粗至静，并将上一层得到的解作为下一层的初始值。

### 直接法

与光流法相比，直接法依然基于灰度不变假设，但直接对相机位姿进行优化：
$$
\begin{gather*}
\min_T e^2 \\
e = I_1(p_1) - I_2(p_2)
\end{gather*}
$$
其中 $p_2 = \frac{1}{Z_2}KTP$，则有
$$
\frac{\partial e}{\partial T} = -\frac{\partial I_2}{\partial p_2} \frac{\partial p_2}{\partial \xi}
$$
其中第一项为像素梯度，第二项参考[最小化重投影误差](#最小化重投影误差)。

# 优化后端

视觉里程计只能估计短时间内的运动轨迹，而在优化后端中需要考虑长时间内的状态估计问题。

1. 仅使用过去的信息更新现在的状态，称为渐进法
2. 也会用未来的信息更新现在的状态，称为批量法

「非线性优化」一章中介绍了批量状态估计问题可以转化为最大似然估计问题，并使用最小二乘法进行求解。本节介绍渐进法。由运动方程和观测方程：
$$
\begin{cases}
x_k = f(x_{k-1}, u_k) + w_k \\
z_k = h(x_k) + v_k
\end{cases}
$$
状态估计问题：$P(x_k | x_0, u_{1:k}, z_{1:k})$。使用贝叶斯法则：
$$
\begin{split}
P(x_k | x_0, u_{1:k}, z_{1:k}) &= \frac{P(z_k | x_k, x_0, u_{1:k}, z_{1:k-1}) P(x_k, x_0, u_{1:k}, z_{1:k-1})}{P(x_0, u_{1:k}, z_{1:k})} \\
&= \frac{P(z_k | x_k) P(x_k | x_0, u_{1:k}, z_{1:k-1})}{P(z_k | x_0, u_{1:k}, z_{1:k-1})} \\
&\propto \underbrace{P(z_k | x_k)}_{\text{似然}} \ \underbrace{P(x_k | x_0, u_{1:k}, z_{1:k-1})}_{\text{先验}}
\end{split}
$$
似然由观测方程给定，先验部分的 $x_k$ 基于过去所有的状态估计得来。按照 $x_{k-1}$ 时刻为条件概率展开：
$$
P(x_k | x_0, u_{1:k}, z_{1:k-1}) = \int P(x_k | x_{k-1}, x_0, u_{1:k}, z_{1:k-1}) P(x_{k-1} | x_0, u_{1:k}, z_{1:k-1}) d x_{k-1}
$$
从这里开始有不同选择：

1. 假设 Markov 性质：$k$ 时刻状态只与 $k-1$ 时刻有关。由此出发可以得到 EKF (Extended Kalman Filter) 为代表的滤波器方法
2. 考虑 $k$ 时刻状态与之前所有状态的关系，可得到以非线性优化为主体的优化框架

## Kalman Filter

参考 [Mobile Robots](https://silencial.github.io/mobile-robots/#kalman-filter)

在线性高斯系统中，Kalman Filter 构成了该系统中的最大后验概率估计，同时也是最优无偏估计。

在非线性系统中，可以对方程进行一阶泰勒展开后使用 Kalman Filter，称为 Extended Kalman Filter。

EKF 的缺点：

1. 马尔可夫性假设
2. 一阶线性化存在误差
3. 需要维护和更新状态量的均值和方差，不适用于大型场景
4. 没有异常检测机制

## Bundle Adjustment

令相机位姿为 $\xi$，路标即三维点为 $p$，观测数据即像素坐标为 $z$，可以定义误差项：
$$
e = z - h(\xi, p)
$$

令 $z_{ij}$ 表示在位姿 $\xi_i$ 处观察路标 $p_j$ 产生的数据，同时对位姿和路标进行优化：
$$
\min \frac{1}{2} \sum_{i=1}^m \sum_{j=1}^n \norm{z_{ij} - h(\xi_i, p_j)}^2
$$

对该优化问题求解需要知道 $e$ 分别对 $\xi$ 和 $p$ 的导数。由于这里的观测模型 $h$ 即为相机投影模型，求导可参考[最小化重投影误差](#最小化重投影误差)。

### 稀疏性

由于该优化问题考虑了所有的变量，因此 Jacobian 矩阵的维度将会非常大，但存在稀疏性，考虑 $e_{ij}$ 对应的 Jacobian：
$$
J_{i j}=\left(0_{2 \times 6}, \ldots 0_{2 \times 6}, \frac{\partial e_{i j}}{\partial \xi_{i}}, 0_{2 \times 6}, \ldots 0_{2 \times 3}, \ldots 0_{2 \times 3}, \frac{\partial e_{i j}}{\partial p_{j}}, 0_{2 \times 3}, \ldots 0_{2 \times 3}\right)
$$
因此 $H=J^TJ$ 会有如下结构：
$$
H = \begin{bmatrix}
B & E \\
E^T & C
\end{bmatrix}
$$
其中 $B, C$ 是对角块矩阵。$B$ 中的每个对角块维度和相机参数的维度相同，个数为相机变量的个数。$C$ 中的每个对角块维度为 $3\times 3$，个数为路标数量，因此 $C$ 的维度会远远大于 $B$。

由于矩阵存在这样的稀疏结构，求解时可以使用 Schur 消元大大减少运算量。

# 回环检测

VO 仅考虑相邻时间上的关键帧，误差会随时间累积。回环检测可以检测出相机是否经过同一个地方，从而添加时隔更加久远的约束，得到全局一致的估计。

预检测方法：

1. 基于里程计的几何关系：无法在累积误差较大时工作
2. 基于图像的相似性：独立于前端和后端，主流方法

## 词袋模型

1. 抽取图像特征，组成「字典」
2. 计算图像特征，将图像转换为向量
3. 通过向量距离计算图像相似度

### 字典

特征：与物体的空间位置和排列顺序无关

1. 对大量图像提取 $N$ 个特征点
2. 使用 K-means 算法将 $N$ 个特征点聚类为 $k$ 类

为了增加查找效率，可以用 $k$ 叉树来表示字典。即将样本聚成 $k$​ 类后再对每一个节点继续聚类，以此类推，最后得到的叶节点即是「字典」中的「单词」

### 相似度计算

在字典中，由于不同单词的重要性不同，我们希望给它们加上不同的权重，从而提高区分性。

TF-IDF (Term Frequency-Inverse Document Frequency) 是文本检索中常用的一种加权方式。

- TF: 某单词在一幅图像中出现的频率越高，区分度就高
- IDF: 某单词在字典中出现的频率越低，区分度就越高

建立字典时计算 IDF：假设所有特征数量为 $n$，某个叶节点 $w_i$ 中的特征数量为 $n_i$，则该单词的 IDF 为
$$
\text{IDF}_i = \log\frac{n}{n_i}
$$
计算 TF：假设某图像中单词 $w_i$ 出现了 $n_i$ 次，而一共出现的单词次数为 $n$，则 TF 为
$$
\text{TF}_i = \frac{n_i}{n}
$$
于是单词 $w_i$ 的权重为 $\eta_i = \text{TF}_i \times \text{IDF}_i$

于是，对于某幅图像 $A$，它的特征点可以对应到许多单词和相应的权重：
$$
A = \{(w_1, \eta_1), \dots, (w_N, \eta_N)\}\triangleq v_A
$$
实际中 $v_A$ 是一个稀疏向量。给定 $v_A, v_B$，有不同的方法可以计算它们的差异，例如 $L_1$ 范数形式：
$$
2 \sum_{i=1}^N (|v_{Ai}| + |v_{Bi}| - |v_{Ai} - v_{Bi}|)
$$

# Reference

1. [Sophus](https://github.com/strasdat/Sophus)：C++ 李代数库
2. [Ceres](http://ceres-solver.org/)：C++ 优化库，主要求解最小二乘问题
3. [g2o](https://github.com/RainerKuemmerle/g2o)：C++ 图优化库
