
## 0. 过程噪声Q的结果是：

$$Q = \sigma_a^2 \begin{bmatrix} \frac{T^4}{4} & \frac{T^3}{2} \\ \frac{T^3}{2} & T^2 \end{bmatrix}$$

下面解释这个推导的完整过程，以及它背后的物理假设。

1. **把加速度当作离散随机输入**，方差 $\sigma_a^2$；
2. **通过运动学公式**得到它对位置/速度的影响矩阵 $G$；
3. **协方差传播**：$Q = \sigma_a^2 G G^T$。

---

## 1. 物理假设：分段离散恒定加速度（Piecewise Constant Acceleration）

图片中的这种方法假设：**在每个采样周期 $T$ 内，加速度是一个随机常量**，其均值为 0、方差为 $\sigma_a^2$，只在采样点处跳变。也就是说，第 $n$ 个周期内的加速度 $a_n$ 是一个离散随机变量，而不是连续时间的白噪声。

这相当于把加速度当作一个**控制输入噪声**，通过控制矩阵 $G$（也叫输入转移矩阵 $\Gamma$）映射到状态增量。

---

## 2. 为什么 $G$ 长这样？

状态向量是 $\mathbf{x} = \begin{bmatrix} x \\ \dot{x} \end{bmatrix}$。

如果在一个周期 $T$ 内加速度恒定为 $a$，根据高中运动学：

- 速度增量：$\Delta \dot{x} = a \cdot T$
- 位置增量：$\Delta x = \frac{1}{2} a T^2$

所以加速度 $a$ 对状态的影响可以写成：

$$\Delta \mathbf{x} = \begin{bmatrix} \frac{T^2}{2} \\ T \end{bmatrix} a = G \cdot a$$

这就是控制矩阵 $G$ 的来源。

---

## 3. 矩阵乘法推导

过程噪声在状态空间中的协方差定义为：

$$Q = \mathbb{E}\left[ (G a)(G a)^T \right] = G \cdot \mathbb{E}[a^2] \cdot G^T = G \sigma_a^2 G^T = \sigma_a^2 (G G^T)$$

计算外积 $G G^T$：

$$G G^T = \begin{bmatrix} \frac{T^2}{2} \\ T \end{bmatrix} \begin{bmatrix} \frac{T^2}{2} & T \end{bmatrix} = \begin{bmatrix} \frac{T^2}{2} \cdot \frac{T^2}{2} & \frac{T^2}{2} \cdot T \\ T \cdot \frac{T^2}{2} & T \cdot T \end{bmatrix} = \begin{bmatrix} \frac{T^4}{4} & \frac{T^3}{2} \\ \frac{T^3}{2} & T^2 \end{bmatrix}$$

因此：

$$\boxed{Q = \sigma_a^2 \begin{bmatrix} \frac{T^4}{4} & \frac{T^3}{2} \\ \frac{T^3}{2} & T^2 \end{bmatrix}}$$

---

## 4. 这种方法 vs. 严格连续时间积分

工程上连续和离散的 $\sigma_a^2$ 经常被混用，但**物理意义和单位完全不同**：

| 语境 | 符号含义 | 单位 | 关系 |
|------|---------|------|------|
| **离散白噪声** | 每个周期内加速度的**方差** | $(\mathrm{m/s^2})^2$ | — |
| **连续白噪声** | 加速度白噪声的**功率谱密度 (PSD)** | $(\mathrm{m/s^2})^2 \cdot \mathrm{s}$ 或 $(\mathrm{m/s^2})^2/\mathrm{Hz}$ | $S_w = \sigma_a^2 \cdot T$ |

**统一约定**：下面推导中，我直接用 $\sigma_a^2$ 代替 $S_w$，但明确它是**连续时间功率谱密度**，记作：

$$E[\eta(t)\eta(\tau)] = \sigma_a^2 \delta(t-\tau)$$

如果你手上有的是"离散加速度方差"（第一节的 $\sigma_a^2$），换算关系为：

$$\boxed{\sigma_{a,\text{cont}}^2 = \sigma_{a,\text{disc}}^2 \cdot T}$$

---

## 5. 连续时间 Q 的完整推导

### 5.1 系统模型

连续时间状态空间（Constant Velocity 模型）：

$$\dot{\mathbf{x}}(t) = A \mathbf{x}(t) + G \eta(t)$$

其中：
- 状态 $\mathbf{x} = \begin{bmatrix} x \\ \dot{x} \end{bmatrix}$
- 系统矩阵 $A = \begin{bmatrix} 0 & 1 \\ 0 & 0 \end{bmatrix}$
- 噪声输入矩阵 $G = \begin{bmatrix} 0 \\ 1 \end{bmatrix}$（噪声直接作用在加速度层）
- $\eta(t)$ 是加速度白噪声，功率谱密度为 $\sigma_a^2$

### 5.2 离散化后的等效噪声

从 $t$ 到 $t+T$ 积分，离散化后的状态转移为：

$$\mathbf{x}_{k+1} = \underbrace{e^{AT}}_{F} \mathbf{x}_k + \underbrace{\int_0^T e^{A\tau} G \eta(\tau) d\tau}_{w_k}$$

过程噪声协方差矩阵定义为：

$$Q = E[w_k w_k^T] = E\left[ \left(\int_0^T e^{A\tau} G \eta(\tau) d\tau\right) \left(\int_0^T e^{A\gamma} G \eta(\gamma) d\gamma\right)^T \right]$$

### 5.3 利用白噪声性质化简

把期望移入积分，利用 $E[\eta(\tau)\eta(\gamma)] = \sigma_a^2 \delta(\tau-\gamma)$：

$$Q = \int_0^T \int_0^T e^{A\tau} G \cdot E[\eta(\tau)\eta(\gamma)] \cdot G^T e^{A^T\gamma} \, d\tau d\gamma$$

$$Q = \int_0^T \int_0^T e^{A\tau} G \sigma_a^2 \delta(\tau-\gamma) G^T e^{A^T\gamma} \, d\tau d\gamma$$

对 $\gamma$ 积分（利用 $\delta$ 函数筛选性质）：

$$\boxed{Q = \sigma_a^2 \int_0^T e^{A\tau} G G^T e^{A^T\tau} \, d\tau}$$

这就是基于**连续时间白噪声**假设（加速度是功率谱密度为 $\sigma_a^2$ 的白噪声）**连续到离散协方差转换的核心公式**

### 5.4 计算矩阵指数 $e^{A\tau}$

对 $A = \begin{bmatrix} 0 & 1 \\ 0 & 0 \end{bmatrix}$，注意到 $A^2 = \begin{bmatrix} 0 & 0 \\ 0 & 0 \end{bmatrix}$，所以：

$$e^{A\tau} = I + A\tau + \frac{A^2\tau^2}{2!} = \begin{bmatrix} 1 & \tau \\ 0 & 1 \end{bmatrix}$$


### 5.5 计算被积函数

先算 $e^{A\tau} G$：

$$e^{A\tau} G = \begin{bmatrix} 1 & \tau \\ 0 & 1 \end{bmatrix} \begin{bmatrix} 0 \\ 1 \end{bmatrix} = \begin{bmatrix} \tau \\ 1 \end{bmatrix}$$

再算外积：

$$e^{A\tau} G G^T e^{A^T\tau} = \begin{bmatrix} \tau \\ 1 \end{bmatrix} \begin{bmatrix} \tau & 1 \end{bmatrix} = \begin{bmatrix} \tau^2 & \tau \\ \tau & 1 \end{bmatrix}$$

### 2.6 逐项积分

$$Q = \sigma_a^2 \int_0^T \begin{bmatrix} \tau^2 & \tau \\ \tau & 1 \end{bmatrix} d\tau = \sigma_a^2 \begin{bmatrix} \int_0^T \tau^2 d\tau & \int_0^T \tau d\tau \\ \int_0^T \tau d\tau & \int_0^T 1 d\tau \end{bmatrix}$$

计算定积分：

- $\int_0^T \tau^2 d\tau = \frac{T^3}{3}$
- $\int_0^T \tau d\tau= \frac{T^2}{2}$ 
- $\int_0^T 1 d\tau= T$ 

得到最终结果：

$$\boxed{Q = \sigma_a^2 \begin{bmatrix} \frac{T^3}{3} & \frac{T^2}{2} \\ \frac{T^2}{2} & T \end{bmatrix}}$$

---

## 6. 物理意义解读

| 元素 | 推导来源 | 物理意义 |
|----|---|---|
| $Q_{11} = \sigma_a^2 \frac{T^3}{3}$ | 加速度噪声 → 速度 → 位置的**双重积分** | 一个周期内，纯随机加速度导致的位置方差增长。积分两次，时间幂次为 $T^3$ |
| $Q_{22} = \sigma_a^2 T$ | 加速度噪声 → 速度的**单重积分** | 速度方差增长，时间幂次为 $T^1$ |
| $Q_{12} = Q_{21} = \sigma_a^2 \frac{T^2}{2}$ | 位置-速度协方差 | 同一噪声源既影响速度又影响位置，两者正相关 |

---

## 7. 两种方法的对比

如果把**离散方差**记为 $\sigma_a^2$（注意单位不同），并代入和连续方差的换算关系 $S_w = \sigma_a^2 T$，你会发现：

| | 连续白噪声（严格积分） | 离散恒定白噪声 |
|---|---|---|
| $Q_{11}$ | $\sigma_a^2 T \cdot \frac{T^3}{3} = \frac{\sigma_a^2 T^4}{3}$ | $\frac{\sigma_a^2 T^4}{4}$ | 
| $Q_{22}$ | $\sigma_a^2 T \cdot T = \sigma_a^2 T^2$ | $\sigma_a^2 T^2$ |
| $Q_{12}$ | $\sigma_a^2 T \cdot \frac{T^2}{2} = \frac{\sigma_a^2 T^3}{2}$ | $\sigma_a^2 \frac{T^3}{2}$ |

| 场景                               | 推荐方法                                          |
| -------------------------------- | --------------------------------------------- |
| $T$ 很小（高频采样，如 $T < 0.1\text{s}$） | 两种差异不大，用离散近似 $T^4/4$ 计算简便即可                   |
| $T$ 较大，或导航/航天等高精度场景              | 用严格连续积分 $T^3/3$，否则滤波器会**低估真实不确定性**，导致过度自信甚至发散 |

**工程结论**：$T$ 较小时两者差异很小；$T$ 较大或精度要求极高时，严格积分（$T^3/3$）更准确。

---
## 8. 工程调参的实用技巧

### 8.1 从物理意义出发定量级

问自己：在一个采样周期 $T$ 内，由于模型不确定性，状态各分量最多可能变化多少？

| 状态分量 | 最大不确定变化 $\Delta x_{\max}$ | 对应 Q 对角元 |
|---------|-------------------------------|-------------|
| 位置 | $\Delta x$ | $\approx (\Delta x)^2$ |
| 速度 | $\Delta v$ | $\approx (\Delta v)^2$ |

### 8.2 利用新息（Innovation）检验

运行滤波器后，检查新息序列 $\mathbf{y}_n = \mathbf{z}_n - H\hat{\mathbf{x}}_{n,n-1}$：

- 如果新息均值**显著不为零** → 模型有偏，可能需要扩增状态或增大 Q。
- 如果新息方差**远大于理论值** $S = HPH^T + R$  → Q 太小，模型过于自信。
	1.  **Q 设得太小** → 过程噪声被低估
	    
	2.  **$P_{k∣k−1​}=FP_{k−1}​F^T+Q$增长不足** → 预测协方差太小
	    
	3.  **理论新息协方差 $S=HPH^T+R$ 被低估** → 滤波器认为"预测很准，测量偏差应该很小"
	    
	4.  **卡尔曼增益 $K=PH^TS^{−1}$ 偏小** → 滤波器更相信预测，不太愿意相信测量
	    
	5.  **但真实世界的过程噪声其实很大**（或有未建模动态/bias）→ 预测值持续偏离真实轨迹
	    
	6.  **实际测量值与预测值频繁大幅偏离** → 实际新息方差很大
- 如果新息方差**远小于理论值** → Q 太大，滤波器过度依赖测量。

实际工程中常做**归一化新息平方（NIS）检验**：

$$\epsilon_k = \mathbf{y}_k^T S_k^{-1} \mathbf{y}_k$$

理论上 $\epsilon_k \sim \chi^2$（卡方分布），自由度等于测量维度，推理如下。

#### 第一步：新息是高斯随机向量

如果卡尔曼滤波的系统模型、噪声假设都正确，那么：
- 预测误差 $\tilde{\mathbf{x}}_{k|k-1} = \mathbf{x}_k - \hat{\mathbf{x}}_{k|k-1}$ 是零均值高斯（因为初始误差和噪声都是高斯，线性系统保持高斯性）
- 测量噪声 $\mathbf{v}_k$ 是零均值高斯
- 新息 $\mathbf{y}_k = H\tilde{\mathbf{x}}_{k|k-1} + \mathbf{v}_k$ 是**两个独立高斯向量的线性组合**

因此：
$$\mathbf{y}_k \sim \mathcal{N}(\mathbf{0}, S_k)$$

其中 $S_k = H P_{k|k-1} H^T + R$。

#### 第二步：白化（Whitening）变换

对协方差矩阵 $S_k$ 做**特征分解**或**Cholesky 分解**，令 $S_k = L L^T$（$L$ 为下三角矩阵）。

定义变换后的向量：
$$\mathbf{z}_k = L^{-1} \mathbf{y}_k = S_k^{-1/2} \mathbf{y}_k$$

计算 $\mathbf{z}_k$ 的协方差：

$$\text{Cov}(\mathbf{z}_k) = S_k^{-1/2} \cdot \text{Cov}(\mathbf{y}_k) \cdot (S_k^{-1/2})^T = S_k^{-1/2} S_k S_k^{-1/2} = I$$

所以：
$$\mathbf{z}_k \sim \mathcal{N}(\mathbf{0}, I)$$

这意味着 $\mathbf{z}_k$ 的每个分量 $z_1, z_2, \dots, z_m$ 都是**独立的标准正态分布** $N(0,1)$。

#### 第三步：二次型 = 独立标准正态的平方和

把 $\mathbf{z}_k$ 代回 NIS 的定义：

$$\epsilon_k = \mathbf{y}_k^T S_k^{-1} \mathbf{y}_k = (L^{-1}\mathbf{y}_k)^T (L^{-1}\mathbf{y}_k) = \mathbf{z}_k^T \mathbf{z}_k = \sum_{i=1}^{m} z_i^2$$

其中 $m$ 是测量向量 $\mathbf{z}_k$ 的维度。

统计学中，**卡方分布**正是这么定义的：

> 若 $z_1, z_2, \dots, z_m \overset{iid}{\sim} N(0,1)$，则 $\sum_{i=1}^m z_i^2 \sim \chi^2(m)$

自由度为 $m$，因为求和项里有 $m$ 个**独立**的标准正态变量。

因此：

$$\boxed{\epsilon_k = \mathbf{y}_k^T S_k^{-1} \mathbf{y}_k \sim \chi^2(m)}$$

其中 $m$ 就是**测量维度**（即传感器一次给出几个数）。

- **如果模型完全正确**（$F, H, Q, R$ 都准），那么 $\epsilon_k$ 必须服从 $\chi^2(m)$。
- **如果模型有错误**（比如 $Q$ 太小、有未建模 bias、$R$ 不准），新息的统计特性就会偏离这个分布。

所以工程上把 NIS 当作**假设检验**：

1. 设定置信区间（如 95%），查 $\chi^2$ 表得到阈值。
2. 滑动窗口统计 NIS 的样本均值。
3. 如果持续超出阈值，就拒绝"模型正确"的原假设，进而调整 $Q$ 或 $R$。

- 如果 $\epsilon_k$ 持续 **> 阈值** → $S_k$ 被低估 → 增大 Q（或检查模型）
- 如果 $\epsilon_k$ 持续 **<< 阈值** → $S_k$ 被高估 → 减小 Q 或 R

### 8.3 自适应 Q（进阶）

如果噪声特性时变，可采用：
- **Sage-Husa 自适应滤波**：在线估计 Q。
- **多模型滤波（IMM）**：切换不同 Q 的模型。
- **模糊/神经网络调参**：根据工况动态调整。

---

## 9. 常见误区

| 误区 | 问题 | 正确做法 |
|------|------|---------|
| Q 设为零 | 滤波器过度自信，一旦模型不准就发散 | 至少保留一个小的正定 Q |
| Q 各元素随意设定 | 单位不一致，滤波器性能差 | 从物理噪声源推导，注意量纲 |
| Q 与 R 比例失衡 | 估计震荡或滞后 | 通过新息协方差检验调整 |
| 忽略采样周期 T 的影响 | Q 与 T 的幂次相关，T 变化后性能突变 | 重新推导或缩放 Q |

---

## 10. 快速参考公式

| 模型 | 关键假设 | Q 矩阵 |
|------|---------|--------|
| 一维恒定 | 状态本身有随机扰动 | $Q = \sigma_w^2$ |
| 匀速 CV | 加速度为白噪声 | $Q = S_w \begin{bmatrix} T^3/3 & T^2/2 \\ T^2/2 & T \end{bmatrix}$ |
| 匀加速 CA | jerk 为白噪声 | $Q = S_w \begin{bmatrix} T^5/20 & T^4/8 & T^3/6 \\ T^4/8 & T^3/3 & T^2/2 \\ T^3/6 & T^2/2 & T \end{bmatrix}$ |
| 随机游走 | 状态导数为白噪声 | $Q = S_w T$ |

---

**总结**：构建 Q 的正确顺序是：**识别物理噪声源 → 写出连续时间模型 → 离散化映射到状态空间 → 计算协方差积分**。避免直接"拍脑袋"填数字，否则滤波器要么发散要么过度平滑。

<!--stackedit_data:
eyJoaXN0b3J5IjpbNDQ1Mzg2NSwtODc1MTI0MDc0LC0xNjcwOD
I4NzA3LDE3NzA5MzYzMTEsLTExMTY0OTgxNTAsLTEzMTExOTU0
NTUsMTk5Nzg3OTc2NywtMTYxNTg4MjUwNywxNjExNjc5N119
-->