---
title: SLAM Basics
date: 2021-08-12
updated: 2026-06-02
categories:
- Study
tags:
- Robotics
---

SLAM 笔记，基于[《视觉 SLAM 十四讲》](https://github.com/gaoxiang12/slambook2)

<!--more-->

---

SLAM (Simultaneous Localization and Mapping) 指搭载特定传感器的主体，在无环境先验信息的情况下，于运动过程中建立环境的模型并估计自己运动。若传感器以相机为主，则称为「视觉 SLAM」。

# 视觉 SLAM 基础

## 传感器：相机模型

- 单目相机 (Monocular)：依靠相机运动产生的视差估计物体**相对**大小，存在尺度不确定性（与真实尺度相差一个比例因子）。
- 双目 (Stereo)：探测深度范围与基线（即两相机间距）成正比。视差计算极度消耗计算资源。
- RGB-D 相机：采用红外结构光 (Structured Light) 或飞行时间 (ToF, Time-of-Flight) 原理，物理直测深度，无需软件计算。局限性包括：测量范围窄、噪声大、易受日光干扰、无法测量透射材质。在 SLAM 中主要用于室内场景。

## 系统框架

标准视觉 SLAM 框架包含以下五个核心模块：

1. 传感器信息读取：相机图像的读取与预处理，可选融合里程计 (Odometer) 与惯性测量单元 (IMU) 数据。
2. 视觉里程计 (Visual Odometry, VO)：即「前端」。估算相邻图像间的相机运动并构建局部地图。由于仅关注局部运动信息，不可避免会产生累积漂移 (Accumulating Drift)。
3. 后端优化 (Optimization)：即「后端」。接收前端输出的带噪声的相机位姿与回环检测信息，进行全局状态估计与优化，输出全局一致的轨迹和地图。
4. 回环检测 (Loop Closing)：判断机器人是否到达过历史位置，将闭环信息传递至后端以消除累积误差。
5. 建图 (Mapping)：根据估计轨迹，建立满足特定任务需求的地图。

技术栈划分：前端与计算机视觉 (Computer Vision) 强相关（如图像特征提取与匹配）；后端核心为**滤波理论与非线性优化算法**。

地图分类（依应用场景而定）：

- 度量地图 (Metric Map)：精确定量表示物理空间的位置关系。
    - 稀疏地图：由路标建模，满足基本定位需求。
    - 稠密地图：对空间进行高分辨率划分（2D 采用 Grid，3D 采用 Voxel），适用于导航避障，但存储开销庞大。
- 拓扑地图 (Topological Map)：强调地图元素间的连通关系，由节点和边构成的图 (Graph)，不擅长表达具备复杂几何结构的物理地图。

## 数学模型

设机器人在 $k$ 时刻的真实位姿为 $x_k$，运动传感器的读数为 $u_k$。环境由 $n$ 个路标集合 $\{y_1, \dots, y_n\}$ 表示。机器人在 $x_k$ 处观测到路标 $y_j$ 的数据记为 $z_{k,j}$。系统状态方程可建模为：

$$
\begin{cases}
x_k = f(x_{k-1}, u_k, w_k) \quad &\text{(运动方程)}\\
z_{k,j} = h(y_j, x_k, v_{k,j}) \quad &\text{(观测方程)}
\end{cases}
$$

其中 $w_k$ 和 $v_{k,j}$ 为系统噪声。

SLAM 问题的本质：在已知运动读数 $u$ 及观测读数 $z$ 的条件下，求解定位序列 $x$ 和地图结构 $y$ 的状态估计问题。

根据方程的线性度与噪声分布，系统求解可分为以下几类：

- 线性高斯系统：存在无偏最优估计，可通过 Kalman Filter 精确求解。
- 非线性非高斯系统：主流方案分为 Extended Kalman Filter 与非线性优化两大类。

目前视觉 SLAM 领域，以图优化 (Graph Optimization) 为代表的非线性优化技术在性能上已显著优于传统滤波技术，成为绝对的主流。

# 三维空间刚体运动

机器人在三维空间中的运动由旋转和平移组成。主要涉及以下核心数学概念（详细推导可参考 [Robotic Manipulation -> Rigid Body Motion]({{< ref "robotic-manipulation#rigid-body-motion" >}})：

- 旋转矩阵：正交且行列式为 1，属于特殊正交群 $SO(3)$。
- 欧拉角：直观描述三轴旋转，但存在万向锁 (Gimbal Lock) 奇异性问题。
- 四元数：紧凑且无奇异性，常用于程序中的实际姿态运算。

# 李群与李代数

设机器人观测到世界坐标系下的点 $p$，观测方程为 $z = Tp + w$（$w$ 为噪声）。若有 $N$ 个路标，位姿估计可转化为如下最小二乘优化问题：

$$
\min_{T} \sum_{i=1}^N \norm{z_i - Tp_i}_2^2
$$

求解此问题需对变换矩阵 $T$ 求导。但 $SO(3)$ 和 $SE(3)$ 是群，**没有良好定义的加法**。若将其视为普通矩阵，优化时必须引入复杂的正交性约束。李代数通过指数映射将李群映射到向量空间，使我们能用常规加法对位姿进行求导与更新。

## BCH 近似公式

对于李代数 $\phi_1, \phi_2 \in so(3)$，BCH 近似公式近似公式给出了李群乘法对应的李代数加法近似：

$$
\ln \Big(\exp \left(\phi_{1}^{\wedge}\right) \exp \left(\phi_{2}^{\wedge}\right)\Big)^{\vee} \approx
\begin{cases}
J_{l}\left(\phi_{2}\right)^{-1} \phi_{1}+\phi_{2} & \text {当 } \phi_{1} \text { 为小量时} \\
J_{r}\left(\phi_{1}\right)^{-1} \phi_{2}+\phi_{1} & \text {当 } \phi_{2} \text { 为小量时}
\end{cases}
$$

其中左 Jacobian $J_l$ 与右 Jacobian $J_r$ 的形式为

$$
\begin{dcases}
J_{l}(\phi) = \frac{\sin \theta}{\theta} I+\left(1-\frac{\sin \theta}{\theta}\right) a a^{T}+\frac{1-\cos \theta}{\theta} a^{\wedge} \\
J_r(\phi) = J_l(-\phi)
\end{dcases}
$$

（注：$\phi = \theta a$，$\norm{a} = 1$）

## 位姿求导模型

在 SLAM 优化中，通常有两种求导策略：李代数求导与扰动模型。

**李代数求导**：直接对 $R$ 对应的李代数 $\phi$ 求导

$$
\begin{split}
\frac{\partial (\exp(\phi^\wedge)p)}{\partial \phi} &= \lim_{\delta\phi \to 0} \frac{\exp((\phi + \delta\phi)^\wedge)p - \exp(\phi^\wedge)p}{\delta \phi} \\
&= \lim_{\delta\phi \to 0} \frac{\exp((J_l \delta\phi)^\wedge) \exp(\phi^\wedge)p - \exp(\phi^\wedge)p}{\delta \phi} \\
&= \lim_{\delta\phi \to 0} \frac{(I +(J_l \delta\phi)^\wedge) \exp(\phi^\wedge)p - \exp(\phi^\wedge)p}{\delta \phi} \\
&= \lim_{\delta\phi \to 0} \frac{-(\exp(\phi^\wedge)p)^\wedge J_l \delta\phi}{\delta \phi} \\
&= -(Rp)^\wedge J_l
\end{split}
$$

由于必须计算复杂的 Jacobian $J_l$，该方法在实际应用中不够高效。

**扰动模型（主流方案）**：对 $R$ 引入一个微小扰动 $\Delta R$（设对应李代数为 $\varphi$），求结果对扰动量的导数。以左扰动为例

$$
\begin{split}
\frac{\partial (Rp)}{\partial \varphi} &= \lim_{\varphi \to 0} \frac{\exp(\varphi^\wedge) \exp(\phi^\wedge)p - \exp(\phi^\wedge)p}{\varphi} \\
&= \lim_{\varphi \to 0} \frac{(I + \varphi^\wedge) \exp(\phi^\wedge)p - \exp(\phi^\wedge)p}{\varphi} \\
&= \lim_{\varphi \to 0} \frac{-(Rp)^\wedge \varphi}{\varphi} \\
&= -(Rp)^\wedge
\end{split}
$$

同理，直接给出 $SE(3)$ 上的左扰动模型（设扰动李代数为 $\delta \xi = [\delta \rho, \delta\phi]^T$）：

$$
\begin{split}
\frac{\partial(T p)}{\partial \xi} &= \lim_{\delta \xi \to 0} \frac{\exp (\delta \xi^{\wedge}) \exp (\xi^{\wedge}) p - \exp (\xi^{\wedge}) p}{\delta \xi} \\
&= \lim_{\delta \xi \to 0} \frac{\delta \xi^{\wedge} \exp (\xi^{\wedge}) p}{\delta \xi} \\
&= \lim_{\delta \xi \to 0} \frac{\begin{bmatrix} \delta\phi^\wedge & \delta \rho \\ 0 & 0 \end{bmatrix} \begin{bmatrix} Rp + t \\ 1 \end{bmatrix}}{\delta \xi} \\
&= \lim_{\delta \xi \to 0} \frac{\begin{bmatrix} \delta\phi^\wedge (Rp + t) + \delta \rho \\ 0 \end{bmatrix}}{[\delta \rho, \delta\phi]^T} \\
&= \begin{bmatrix} I & -(Rp + t)^\wedge \\ 0 & 0 \end{bmatrix} \\
&\triangleq (T p)^{\odot}
\end{split}
$$

注：算子 $\odot$ 将齐次空间点变换为 $4\times 6$ 矩阵。扰动模型避开了 $J_l$ 的计算，更加简洁实用。

## 轨迹误差评估

评估 SLAM 算法精度通常使用以下指标（设 $N$ 为位姿数，$T_{\text{gt}}$ 为真实位姿，$T_{\text{esti}}$ 为估计位姿）：

绝对轨迹误差：计算每个位姿李代数偏差的 RMSE

$$
\mathrm{ATE}_{\mathrm{all}}=\sqrt{\frac{1}{N} \sum_{i=1}^{N} \norm*{\log (T_{\text{gt}, i}^{-1} T_{\text{esti}, i})^{\vee}}_{2}^{2}}
$$

绝对平移误差：仅考虑轨迹的平移部分

$$
\mathrm{ATE}_{\text{trans}}=\sqrt{\frac{1}{N} \sum_{i=1}^{N} \norm*{\operatorname{trans} (T_{\mathrm{gt}, i}^{-1} T_{\mathrm{esti}, i})}_{2}^{2}}
$$

# 相机与图像

## 针孔相机模型

描述了空间点从世界坐标 -> 相机坐标 -> 成像坐标 -> 像素坐标的投影过程。

![针孔相机模型](https://i.imgur.com/AUFBQNy.png)

**相机坐标 -> 成像坐标**：设相机焦距为 $f$，空间点在相机坐标系下为 $P = [X, Y, Z]^T$。根据针孔模型（去掉导致倒立的负号）：

$$
X' = f \frac{X}{Z}, \quad Y' = f \frac{Y}{Z}
$$

**相机坐标 -> 像素坐标**：像素坐标系原点位于图像左上角。设像素坐标在 $u, v$ 轴上分别缩放了 $\alpha, \beta$ 倍，原点平移了 $[c_x, c_y]$，则

$$
u = \alpha X' + c_x, \quad v = \beta Y' + c_y
$$

令 $f_x = \alpha f, f_y = \beta f$，并引入齐次坐标，可得相机坐标 -> 像素坐标的矩阵表达：

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

其中矩阵 $K$ 称为**相机内参 (Intrinsic Matrix)**。

**世界坐标 -> 像素坐标**：设点在世界坐标系下为 $P_{w}$，相机的外参（位姿）为 $R,t$，则最终投影变换为

$$
P_{uv} = \begin{pmatrix}
u \\ v \\ 1
\end{pmatrix} = \frac{1}{Z}K(RP_w + t)
$$

## 畸变模型

相机透镜在物理制造和组装过程中会产生畸变，主要分为两类：

- 径向畸变：由透镜形状引起（桶形畸变、枕形畸变）。通常假设这些畸变呈多项式关系：

    $$
    \begin{gather*}
    x_{\text{corrected}}=x\left(1+k_{1} r^{2}+k_{2} r^{4}+k_{3} r^{6}\right) \\
    y_{\text{corrected}}=y\left(1+k_{1} r^{2}+k_{2} r^{4}+k_{3} r^{6}\right)
    \end{gather*}
    $$

- 切向畸变：由透镜与成像面不平行引起。可以由下式纠正：

    $$
    \begin{gather*}
    x_{\text{corrected}}=x+2 p_{1} x y+p_{2}\left(r^{2}+2 x^{2}\right) \\
    y_{\text{corrected}}=y+p_{1}\left(r^{2}+2 y^{2}\right)+2 p_{2} x y
    \end{gather*}
    $$

其中 $r$ 表示点 $[x,y]$ 离坐标系原点的距离，$\theta$ 表示和水平轴的夹角。

## 双目相机模型

![双目相机模型](https://i.imgur.com/FaR8Gpg.png)

通过匹配左右图的对应像素计算视差 (disparity) $d=u_{L}-u_{R}$。根据相似三角形可得深度：

$$
z=\frac{f b}{d}
$$

其中 $b$ 为基线。视差与深度成反比，基线越长，双目能测量的有效距离越远。

在实际应用中，视差 $d$ 的计算需要寻找像素在左右图像的对应位置，这种极线搜索和立体匹配的计算量极大，通常需要借助 GPU 或 FPGA 来实现实时计算。

## RGB-D 相机模型

根据物理直接获取深度图，主要分为两大类：

- 结构光 (Structured Light)：向目标投射预设的红外结构光图案，根据返回图案的变形程度来计算物体与自身的距离
- 飞行时间 (ToF, Time-of-Flight)：向目标发射脉冲光，根据光脉冲发送到返回的时间差计算物体与自身的距离。

RGB-D 相机在硬件内部通常会自行完成深度与彩色图像素的对齐处理，直接输出一一对应的彩色图和深度图。我们可以借此直接计算出图像的 3D 相机坐标并生成点云。

# 非线性优化

## 状态估计问题

回顾 SLAM 的运动方程和观测方程，通常假设噪声项满足零均值高斯分布：

$$
w_k \sim \mathcal{N}(0, R_k), \quad v_k \sim \mathcal{N}(0, Q_{k,j})
$$

状态估计问题本质上是求已知输入 $u$ 和观测 $z$ 时，状态 $x, y$ 的条件概率分布：$P(x,y | z,u)$。

处理状态估计的方法大致分为两类：

- 增量法/滤波器：假设马尔可夫性，仅用当前观测更新当前状态（如 EKF）。适用于计算资源受限的早期系统。
- 批量法/优化：将过去一段时间内收集的大量数据统一处理。

由于批量法可以在更大范围内达到最优化，目前被认为显著优于滤波器方法，是视觉 SLAM 的绝对主流。考虑到 SLAM 对实时性的要求，工程中通常仅对当前时刻附近的一段局部轨迹和局部特征进行优化，这被称为**滑动窗口估计 (Sliding Window Estimation)**。

## 最大似然估计

对状态估计问题使用贝叶斯法则：

$$
\underbrace{P(x, y | z, u)}_{\text{后验}}= \frac{P(z, u | x, y) P(x, y)}{P(z, u)} \propto \underbrace{P(z, u | x, y)}_{\text{似然}} \ \underbrace{P(x, y)}_{\text{先验}}
$$

**最大后验估计 (Maximize a Posterior, MAP)**：求状态最优估计，使后验概率最大化

$$
(x, y)^*_{\text{MAP}} = \operatorname*{arg\,max}_{x,y} P(x,y | z, u) = \operatorname*{arg\,max}_{x,y} P(z,u | x,y) P(x,y)
$$

**最大似然估计 (Maximize Likelihood Estimation, MLE)**：在没有先验知识（或假设先验为均匀分布）时，问题退化为使似然概率最大化

$$
(x, y)^*_{\text{MLE}} = \operatorname*{arg\,max}_{x,y} P(z,u | x,y)
$$

## 最小二乘问题

假设各个时刻的输入和观测相互独立，联合分布可分解为边缘分布的乘积：

$$
P(z,u|x,y) = \prod_k P(u_k|x_{k-1}, x_k) \prod_{k,j} P(z_{k,j}|x_k, y_j)
$$

定义运动误差项 $e_{u,k}$ 与观测误差项 $e_{z,j,k}$：

$$
\begin{gather*}
e_{u,k} = x_k - f(x_{k-1}, u_k) \\
e_{z,j,k} = z_{k,j} - h(x_k, y_j)
\end{gather*}
$$

对高斯分布的概率密度函数取负对数，最大似然估计等价于求解如下最小二乘问题：

$$
\min_{x,y} \left( \sum_k e_{u,k}^T R_k^{-1} e_{u,k} + \sum_{k,j} e_{z,k,j}^T Q_{k,j}^{-1} e_{z,k,j} \right)
$$

# 视觉里程计

视觉里程计的核心任务是根据相邻图像估计相机运动。主要分为**特征点法**和**直接法**两大派系。

## 特征点法

首先从图像中提取具有代表性的特征点，在不同图像间进行匹配，进而估计相机位姿。为了保证识别精度，通常选用稳定的局部图像特征（如 SIFT, SURF, ORB 等）。

优秀特征点应具备的性质：

- 可重复性 (Repeatability)：同一特征能在不同视角/图像中被稳定找到。
- 可区分性 (Distinctiveness)：不同特征有差异明显的数学表达。
- 高效率 (Efficiency)：特征点数量远小于像素总量，计算速度快。
- 局部性 (Locality)：特征仅与一小片图像区域相关。

特征点通常由两部分组成：

- 关键点 (Keypoint)：记录特征点在图像中的 2D 位置信息，也可附带方向、大小等属性。
- 描述子 (Descriptor)：一个向量，用于量化描述关键点周围像素的统计信息。描述子距离相近则认为是同一特征点。

特征点法的缺点：

- 提取与匹配极端耗时。
- 丢弃了图像中绝大部分的有纹理或结构信息。
- 在缺乏纹理（如白墙）的环境中，无法提取足够匹配点，容易导致跟踪丢失。

## ORB 特征

ORB (Oriented FAST and Rotated BRIEF) 是一种计算效率极高的特征，提取步骤分为两步：

1. FAST 角点提取：ORB 在 FAST 角点基础上计算了特征点的主方向，为后续的 BRIEF 描述子增加了旋转不变性。
2. BRIEF 描述子：对前一步提取出特征点的周围图像区域进行描述。

### FAST 角点

FAST 是一种通过局部像素灰度比较来快速检测角点的方法。检测流程：

1. 选取像素 $p$，假设亮度为 $I_p$
2. 设置亮度阈值 $T$（如 $I_p$ 的 $20\%$）
3. 考察以 $p$ 为中心，半径为 $3$ 的圆上的 $16$ 个像素点
4. 若圆上存在连续 $N$ 个点的亮度均 $> I_p + T$ 或 $< I_p - T$，则认为 $p$ 是角点（通常 $N=12$）
5. **预测试优化**：仅检测圆上第 $1, 5, 9, 13$ 号像素，若其中至少 $3$ 个满足条件，才进行完整测试，否则直接排除。
6. **非极大值抑制 (Non-maximal suppression)**：在局部区域内仅保留响应值极大的角点，避免角点扎堆。

ORB 在标准 FAST 基础上增加了两大特性：

- 尺度不变性：构建图像金字塔 (Image Pyramid)，对不同分辨率的图层分别进行角点检测。
- 旋转不变性：引入灰度质心法 (Intensity Centroid)。
    1. 定义图像块 $B$ 的矩：$m_{pq} = \sum_{x,y\in B} x^p y^q I(x,y), \quad p,q=\{0, 1\}$
    2. 计算图像块的质心坐标：$C= (m_{10}/m_{00}, m_{01}/m_{00})$
    3. 连接几何中心与质心构成方向向量，特征点的方向定义为：$\theta = \arctan(m_{01}/m_{10})$

### BRIEF 描述子

BRIEF 是一种**二进制**描述子，其向量由 $0$ 和 $1$ 组成，编码了关键点附近多组随机采样像素对 $p, q$ 的灰度大小关系。由于提取阶段计算了方向 $\theta$，ORB 能够计算出具有旋转不变性的描述子。

匹配二进制描述子通常使用汉明距离 (Hamming distance)。对于海量特征点，暴力匹配开销巨大，实际工程中常采用 **FLANN (Fast Library for Approximate Nearest Neighbors)** 算法进行近似最近邻加速。

## 相机运动估计

在不同图像间提取并匹配好特征点对之后，下一步便是根据这些配对信息来估计相机的运动。根据相机的种类，运动估计方法主要分为以下三类：

- 单目相机 (2D-2D)：根据两组 2D 像素点估计运动（主要使用对极几何）。
- 双目 / RGB-D 相机 (3D-3D)：包含深度距离信息，根据两组 3D 空间点估计运动（主要使用 ICP）。
- 混合情况 (3D-2D)：已知一组 3D 空间点及其在另一视角的 2D 投影点估计运动（主要使用 PnP）。

### 2D-2D: 对极几何

![对极几何约束](https://i.imgur.com/Y59ilXL.png)

设空间点 $P$ 在两帧图像 $I_1, I_2$ 中的成像点分别为 $p_1, p_2$。两帧相机的光心分别为 $O_1, O_2$。设相机从 $I_1$ 到 $I_2$ 的运动为 $R, t$。

在 $I_1$ 坐标系下，设 $P$ 的空间位置为 $[X, Y, Z]^T$，根据针孔模型（采用齐次坐标和等价符号 $\simeq$）：

$$
p_1 \simeq KP, \quad p_2 \simeq K(RP + t)
$$

转换为归一化坐标 $x_1=K^{-1}p_1, x_2 = K^{-1}p_2$，代入可得：

$$
x_2 \simeq Rx_1 + t
$$

等式两边同时左乘 $x_2^T t^\wedge$。由于 $t^\wedge x_2$ 垂直于 $x_2$ 和 $t$，故 $x_2^T t^\wedge x_2 = 0$，且 $x_2^T t^\wedge t = 0$。于是等号成立：

$$
x_2^T t^\wedge R x_1 = 0 \implies p_2^T K^{-T} t^\wedge R K^{-1} p_1 = 0
$$

该式即为**对极约束**。其几何意义是 $O_1, P, O_2$ 三点共面。

定义**本质矩阵 (Essential Matrix)** $E = t^\wedge R$，则 $x_2^T E x_1 = 0$。

- $E$ 具有 5 个自由度（平移 3 + 旋转 3 - 尺度等价性 1）
- $E$ 的内在性质：其奇异值必定是 $[\sigma, \sigma, 0]$ 的形式

理论上，$E$ 只有 5 个自由度，最少可以通过 5 对点求解。但该方法会引入非线性方程。我们可以只考虑 $E$ 的尺度等价性，使用 8 对点来构建 $A e = 0$ 线性方程组求得 $E$ 的基础解。

由于求出的 $E$ 可能不满足其内在的奇异值约束，需对其进行奇异值分解 $E=U \Sigma V^T$，并强制将奇异值矩阵 $\Sigma$ 投影至 $\operatorname{diag}(1, 1, 0)$：

$$
E_{\text{projected}} = U \operatorname{diag}(1, 1, 0) V^T
$$

随后从 $E$ 中恢复 $R, t$。存在 4 组可能的解，需通过**三角测量 (Triangulation)** 验证，保留使点深度在两个相机中均 $>0$ 的唯一正确解。

实际问题：

- **尺度不确定性**：推导时 $t$ 的尺度被归一化，导致单目轨迹存在无法消除的缩放。
- **初始化**：必须有足够平移，纯旋转无法完成单目初始化。
- **多于八对点**：通常匹配点远多于 8 对，需配合**随机采样一致性 (RANSAC)** 剔除误匹配，并求解超定方程。

### 3D-2D: PnP

已知一组 3D 空间点及其在图像上的 2D 投影，求解相机位姿。

**直接线性变换 (DLT)**：设空间点 $P=(X, Y, Z, 1)^T$，投影点 $x_1=(u_1, v_1, 1)^T$。定义增广矩阵 $T=[R|t]$，则有

$$
s\begin{pmatrix}
u_{1} \\
v_{1} \\
1
\end{pmatrix}=\begin{pmatrix}
t_1^T  \\
t_2^T \\
t_3^T
\end{pmatrix}\begin{pmatrix}
X \\
Y \\
Z \\
1
\end{pmatrix}
$$

消去深度 $s$ 后，每个点对可提供 2 个关于 $T$ 行向量 $t_{i}$ 的线性约束。求解 12 个未知数至少需要 6 对匹配点。最后需将左侧 $3 \times 3$ 矩阵正交化投影回 $SO(3)$ 流形。

**P3P 算法**：仅利用 3 对点。设 3D 空间点 $A,B,C$，相机光心 $O$。根据余弦定理：

![P3P](https://i.imgur.com/mIlh5MR.png)

$$
\begin{cases}
O A^{2}+O B^{2}-2 O A \cdot O B \cdot \cos \langle a, b\rangle=A B^{2} \\
O B^{2}+O C^{2}-2 O B \cdot O C \cdot \cos \langle b, c\rangle=B C^{2} \\
O A^{2}+O C^{2}-2 O A \cdot O C \cdot \cos \langle a, c\rangle=A C^{2}
\end{cases}
$$

记 $x=OA/OC, y=OB/OC, u=BC^2/AB^2, w=AC^2/AB^2$，整理得到关于 $x,y$ 的二元二次方程组：

$$
\begin{cases}
(1-u) y^{2}-u x^{2}-\cos \langle b, c\rangle y+2 u x y \cos \langle a, b\rangle+1=0 \\
(1-w) x^{2}-w y^{2}-\cos \langle a, c\rangle x+2 w x y \cos \langle a, b\rangle+1=0
\end{cases}
$$

解出 $x,y$ 即可获取 3D 点在相机坐标系下的具体坐标，从而将 PnP 转化为 3D-3D (ICP) 问题。该方程最多可能有 4 个解，需用第 4 个点验证。

P3P 只利用 $3$ 个点的信息，并且容易受噪声影响。在 SLAM 中，通常的做法是先使用 P3P 估计相机位姿，然后构建最小二乘优化问题对估计值进行调整。

**最小化重投影误差 (Bundle Adjustment, BA)**：目前的主流与最优做法。将位姿与空间点看作优化变量，构建非线性最小二乘问题：

$$
T^* = \operatorname*{arg\,min}_T \frac{1}{2} \sum_{i=1}^n \norm*{ u_i - \frac{1}{s_i} KTP_i }^2
$$

使用高斯-牛顿等算法优化时，需计算误差 $e$ 对位姿增量 $\delta \xi$ 的 Jacobian（利用链式法则与李代数扰动模型）：

$$
\frac{\partial e}{\partial \delta\xi} = \frac{\partial e}{\partial P'} \frac{\partial P'}{\partial \delta\xi} = -\begin{bmatrix}
\frac{f_{x}}{Z'} & 0 & -\frac{f_{x} X'}{ {Z'}^2} \\
0 & \frac{f_{y}}{Z'} & -\frac{f_{y} Y'}{ {Z'}^2}
\end{bmatrix} \begin{bmatrix}
I & -{P'}^\wedge
\end{bmatrix}
$$

（其中 $P' = TP_i = [X', Y', Z']^T$ 为点在相机系下坐标）

### 3D-3D: ICP

已知两组配对好的 3D 点云 $P = \{p_i\}, P' = \{p_i'\}$，求解旋转 $R$ 和平移 $t$ 使得 $p_i = R p_i' + t$。

**SVD 方法**：构建最小二乘目标 $\min_{R, t} \frac{1}{2} \sum \norm{p_i - (Rp_i' + t)}^2$。求解步骤：

1. 计算两组点的质心 $p, p'$，以及去质心坐标 $q_i = p_i - p, q_i' = p_i' - p'$
2. 分离平移项，优化目标转化为纯旋转：$\operatorname{arg\,min}_R \sum \norm{q_i - Rq_i'}^2$
3. 展开上式并利用迹的性质，等价于最大化：$\operatorname{tr}(R \sum q_i' q_i^T)$
4. 定义矩阵 $W = \sum q_i q_i'^T$。对其进行奇异值分解 $W=U\Sigma V^T$
5. 当 $W$ 满秩时，最优旋转矩阵为 $R=UV^T$（若 $|R|<0$ 则取 $-R$）
6. 代回质心计算平移：$t^* = p - Rp'$

**非线性优化方法**：类似于 PnP 中的最小化重投影误差方法，采用李代数构建无约束优化

$$
\min_\xi \frac{1}{2} \sum_{i=1}^n \norm{p_i - \exp(\xi^\wedge) p_i'}^2
$$

## 直接法

直接法摒弃了特征提取与匹配，直接通过图像的像素灰度进行追踪与姿态估计。

核心特征：

- 只需要场景存在像素梯度（无需明显的角点），在弱纹理区域表现更好。
- 强依赖**光度不变假设**：同一空间点在不同视角下的像素灰度值不变。
- 目标函数高度非凸，优化极易陷入局部极小值。必须引入图像金字塔，并确保相机运动足够小（高帧率）。

**光流法 (Optical Flow)**：描述像素随时间在图像间的运动场。以 Lucas-Kanade (LK) 光流为例，根据光度不变假设，记 $t$ 时刻位于 $(x,y)$ 处的像素灰度值为 $I(x,y,t)$，设 $t+dt$ 时刻像素运动至 $(x+dx, y+dy)$，则有 $I(x+dx, y+dy, t+dt) = I(x, y, t)$

对 $I(x+dx, y+dy, t+dt)$ 进行一阶泰勒展开。记像素速度 $u=dx/dt, v=dy/dt$，得到光流基本方程：

$$
\begin{bmatrix}
I_x & I_y
\end{bmatrix}
\begin{bmatrix}
u \\ v
\end{bmatrix}
= -I_t
$$

LK 算法进一步假设某一个局部窗口内的所有像素具备相同的运动 $[u, v]^T$，从而构成超定方程组，通过最小二乘法求解。

光流法也可以看成一个优化问题：

$$
\min_{dx, dy} \norm{I_1(x,y) - I_2(x+dx, y+dy)}^2
$$

对应的 Jacobian 可以用第一个图像的梯度来代替，因此每次迭代只需要更新残差。

当相机运动较快，图像差异明显时，该优化问题容易陷入局部极小值。可以通过引入**图像金字塔**来解决这一问题。即对原始图像进行逐层下采样，构建出一组分辨率由高到低的金字塔图像集。计算光流时由低分辨率到高分辨率，并将上一层得到的解作为下一层的初始值。

**直接法姿态估计**：不计算光流，直接将相机位姿 $\xi$ 作为优化变量，最小化光度误差

$$
\min_T e^2, \quad \text{其中 } e = I_1(p_1) - I_2(p_2)
$$

且 $p_2 = \frac{1}{Z_2}KTP$。使用链式法则求导：

$$
\frac{\partial e}{\partial T} = -\frac{\partial I_2}{\partial p_2} \frac{\partial p_2}{\partial \delta\xi}
$$

Jacobian 的第一项为目标图像的**像素梯度**，第二项为像素坐标对位姿的导数（与重投影误差推导类似）。

# 优化后端

视觉里程计仅关注短时间内的局部运动估计，误差不可避免地会随时间不断累积。优化后端的任务是考虑长时间内的状态估计问题，接收前端输出的带噪声的位姿和回环信息，求取全局一致的位姿与地图。

回顾运动和观测方程：

$$
\begin{cases}
x_k = f(x_{k-1}, u_k) + w_k \\
z_k = h(x_k) + v_k
\end{cases}
$$

状态估计问题本质上是求取条件后验概率 $P(x_k | x_0, u_{1:k}, z_{1:k})$。根据贝叶斯法则，展开为

$$
\begin{split}
P(x_k | x_0, u_{1:k}, z_{1:k}) &= \frac{P(z_k | x_k, x_0, u_{1:k}, z_{1:k-1}) P(x_k, x_0, u_{1:k}, z_{1:k-1})}{P(x_0, u_{1:k}, z_{1:k})} \\
&= \frac{P(z_k | x_k) P(x_k | x_0, u_{1:k}, z_{1:k-1})}{P(z_k | x_0, u_{1:k}, z_{1:k-1})} \\
&\propto \underbrace{P(z_k | x_k)}_{\text{似然}} \ \underbrace{P(x_k | x_0, u_{1:k}, z_{1:k-1})}_{\text{先验}}
\end{split}
$$

对先验部分按照 $x_{k-1}$ 时刻展开（全概率公式）：：

$$
P(x_k | x_0, u_{1:k}, z_{1:k-1}) = \int P(x_k | x_{k-1}, x_0, u_{1:k}, z_{1:k-1}) P(x_{k-1} | x_0, u_{1:k}, z_{1:k-1}) d x_{k-1}
$$

根据对以上积分步骤处理方式的不同，演化出了两大派系：

- 假设**马尔可夫性**（$k$ 时刻的状态仅与 $k-1$ 时刻相关）：演化出以**扩展卡尔曼滤波 (Extended Kalman Filter, EKF)** 为代表的滤波器方法。
- 保留所有状态变量：演化出以**非线性优化**为主的批量法，是目前 SLAM 后端的绝对主流方案。

> EKF 的缺点：一阶泰勒展开线性化带来的误差；计算并维护协方差矩阵对大尺度建图不现实；对异常值缺乏检测机制。

## Bundle Adjustment

BA 的核心思想是将所有时刻的相机位姿 $\xi$ 和所有的三维路标点 $p$ 一起纳入优化变量。设 $z_{ij}$ 为在位姿 $\xi_i$ 处观测路标 $p_j$ 的实际像素坐标。构建重投影误差项 $e_{ij} = z_{ij} - h(\xi_i, p_j)$，目标函数为

$$
\min_{\xi,p} \frac{1}{2} \sum_{i=1}^m \sum_{j=1}^n \norm{z_{ij} - h(\xi_i, p_j)}^2
$$

其中观测模型 $h$ 即为相机投影模型，求导可参考最小化重投影误差。

**矩阵稀疏性**：这是使得大规模 BA 能够实时计算的关键数学结构。由于每个误差项 $e_{ij}$ 仅与它自身对应的位姿 $\xi_i$ 和路标 $p_j$ 相关，对其他变量的偏导均为 $0$。因此 $e_{ij}$ 的整体 Jacobian $J_{ij}$ 极其稀疏：

$$
J_{i j}=\left(0_{2 \times 6}, \ldots , \frac{\partial e_{i j}}{\partial \xi_{i}}, \ldots, 0_{2 \times 3}, \ldots, \frac{\partial e_{i j}}{\partial p_{j}}, \ldots 0_{2 \times 3}\right)
$$

在使用高斯牛顿法进行优化时，增量方程中的信息矩阵 $H=J^TJ$ 会呈现出非常典型的「箭头状」稀疏分块结构：

$$
H = \begin{bmatrix}
B & E \\
E^T & C
\end{bmatrix}
$$

其中：

- $B, C$ 是对角块矩阵
- $B$ 对应相机变量的维度，$C$ 对应路标变量的维度。

因为路标数量往往远远大于位姿数量，$C$ 块占据了矩阵的绝大部分。 利用这种稀疏结构，可以通过 **Schur 消元**极大减少运算量。

# 回环检测

VO 仅依靠相邻帧递推，累积误差在所难免。回环检测的任务是识别相机是否回到了曾经到达过的地点，从而在位姿图中加入长距离、时隔久远的观测约束，强制消除全局误差。 主流的回环检测方案是独立于前端的基于图像相似性（外观匹配）的方法。

## 词袋模型 (Bag-of-Words, BoW)

核心思想：

1. 提取图像中的特征点，将其量化映射为字典中的「单词」
2. 统计图像包含的单词及频率，将图像转换为稀疏向量
3. 通过向量距离计算图像相似度

### 构建字典

- 特征点与物体的空间位置及排列顺序无关
- 对大量代表性图像提取共 $N$ 个特征点
- 使用 **K-means 聚类算法**将这 $N$ 个特征点聚类为 $k$ 个大类（即 $k$ 个单词）

为进一步提升后续检索匹配的速度，通常采用分层聚类的 $k$ 叉树 (K-ary Tree) 结构：每一层都聚类为 $k$ 类，直到预设的叶子节点层级。树的叶子节点即构成最终的字典。

### 相似度计算

在比较两幅图像相似度时，不同的单词应该具备不同的区分度权重：越是普遍常见的特征，权重应越低；越是罕见的特征，权重应越高。这由 TF-IDF 算法实现：

- IDF (Inverse Document Frequency, 逆文档频率)：衡量单词的罕见程度。设特征总数量为 $n$，叶节点 $w_i$ 中的特征数量为 $n_i$，其 IDF 定义为 $\text{IDF}_i = \log(n / n_i)$
- TF (Term Frequency, 词频)：衡量单词在一幅图像中的密集度。设某图像中单词 $w_i$ 出现了 $n_i$ 次，该图总单词数为 $n$，其 TF 定义为 $\text{TF}_i = n_i / n$

单词 $w_i$ 在这幅图像中的最终权重为 $\eta_i = \text{TF}_i \times \text{IDF}_i$

利用 TF-IDF 权重，任意一幅图像 $A$ 都可以被表示为「单词-权重」对构成的稀疏向量 $v_A$：

$$
A = \{(w_1, \eta_1), \dots, (w_N, \eta_N)\}\triangleq v_A
$$

对于两幅图像的向量 $v_A, v_B$，通常使用 $L_1$ 范数的形式计算它们的距离：

$$
\text{Distance}(A, B) = \sum_{i=1}^N |v_{Ai} - v_{Bi}|
$$

若计算出的距离小于某一阈值，则可认为系统检测到了回环。

# Reference

1. [Sophus](https://github.com/strasdat/Sophus): 基于 C++ 编写的李代数库，为 SLAM 专门优化了李群参数化。
2. [Ceres](http://ceres-solver.org/): Google 开源的强大 C++ 优化库，内置自动求导，专解非线性最小二乘问题。
3. [g2o](https://github.com/RainerKuemmerle/g2o): 基于图优化的 C++ 通用框架库，BA 求解的首选工具之一。
