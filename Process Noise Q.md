
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

你之前看到的另一种常见形式：

$$Q_{\text{连续}} = S_w \begin{bmatrix} \frac{T^3}{3} & \frac{T^2}{2} \\ \frac{T离散连续统一：$\sigma_a^2$ 的两种身份

工程上两个 $\sigma_a^2$ 经常被混用，但**物理意义和单位完全不同**：

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

这就是**连续到离散协方差转换的核心公式**。

### 5.4 计算矩阵指数 $e^{A\tau}$

对 $A = \begin{bmatrix} 0 & 1 \\ 0 & 0 \end{bmatrix}$，注意到 $A^2 = \begin{bmatrix} 0 & 0 \\ 0 & 0 \end{bmatrix}$，所以：

$$e^{A\tau} = I + A\tau + \frac{A^2\tau^2}{2!} & T \end{bmatrix}$$

是基于**连续时间白噪声**假设（加速度是功率谱密度为 $S_w$ 的白噪声），通过积分 $Q = \int_0^T e^{A\tau} G S_w G^T e^{A+ \cdots = \begin{bmatrix} 1 & \tau \\ 0 & 1 \end{bmatrix}$$

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

## 7. 两种方法的对比（统一用 $\sigma_a^2$ 表示）

如果把上一张图的**离散方差**也记为 $\sigma_a^2$（注意单位不同），并代入换算关系 $S_w = \sigma_a^2 T$，你会发现：

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


构建过程噪声协方差矩阵 **Q** 是卡尔曼滤波设计中最关键也最容易出错的环节之一。下面从理论推导到工程实践，系统地说明构建方法。

---

## 1. Q 的物理意义

Q 描述的是**状态预测步骤中不确定性增长的程度**：

$$P_{n+1,n} = F P_{n,n} F^T + Q$$

它量化了系统动态模型与真实物理过程之间的差异。模型越精确，Q 越小；模型越粗糙或存在未建模动态，Q 越大。

---

## 2. 构建 Q 的核心思路：从"噪声源"出发

不要直接猜 Q 的每个元素，而是先找到**真正驱动系统不确定性的物理噪声源**，再映射到状态空间。

### 通用步骤

1. **识别噪声源**：通常是加速度噪声、力矩噪声、温度扰动、控制输入噪声等。
2. **写出连续时间噪声强度**：设物理噪声为 $w(t)$，其功率谱密度（PSD）为 $S_w$。
3. **离散化映射到状态**：通过系统动力学，把连续噪声积分映射到离散状态增量。
4. **计算协方差**：$Q = E[w_d w_d^T]$。

---

## 3. 常见模型的 Q 构建方法

### 3.1 一维恒定系统（如液位、温度）

状态：$x$（标量）

$$x_{n+1} = x_n + w_n$$

噪声直接作用于状态本身：
$$Q = q = \sigma_w^2$$

其中 $\sigma_w$ 是过程噪声标准差，反映状态的真实波动幅度。

---

### 3.2 运动学模型（Constant Velocity）

状态：$\mathbf{x} = \begin{bmatrix} x \\ \dot{x} \end{bmatrix}$

假设**加速度**是随机噪声（白噪声），即 $\ddot{x} = w(t)$。

连续时间状态空间：
$$\dot{\mathbf{x}} = \underbrace{\begin{bmatrix} 0 & 1 \\ 0 & 0 \end{bmatrix}}_{A} \mathbf{x} + \underbrace{\begin{bmatrix} 0 \\ 1 \end{bmatrix}}_{G} w(t)$$

离散化（采样周期 $T$）后，通过**连续到离散的协方差转换**：

$$Q = \int_0^T e^{A\tau} G S_w G^T (e^{A\tau})^T d\tau$$

计算得：

$$\boxed{Q = S_w \begin{bmatrix} \frac{T^3}{3} & \frac{T^2}{2} \\ \frac{T^2}{2} & T \end{bmatrix}}$$

其中 $S_w$ 是加速度噪声的功率谱密度（单位：$\text{(m/s}^2)^2/\text{Hz}$）。

**工程近似**：如果 $T$ 很小，有时简化为：
$$Q \approx S_w \begin{bmatrix} \frac{T^3}{3} & \frac{T^2}{2} \\ \frac{T^2}{2} & T \end{bmatrix}$$

---

### 3.3 匀加速模型（Constant Acceleration）

状态：$\mathbf{x} = \begin{bmatrix} x \\ \dot{x} \\ \ddot{x} \end{bmatrix}$

假设**加加速度（jerk）**是白噪声：$\dddot{x} = w(t)$。

同理推导得：

$$\boxed{Q = S_w \begin{bmatrix} \frac{T^5}{20} & \frac{T^4}{8} & \frac{T^3}{6} \\ \frac{T^4}{8} & \frac{T^3}{3} & \frac{T^2}{2} \\ \frac{T^3}{6} & \frac{T^2}{2} & T \end{bmatrix}}$$

---

### 3.4 通用线性系统

对于 $\dot{\mathbf{x}} = A\mathbf{x} + G w(t)$，离散化后：

$$Q_d = \int_0^T e^{A\tau} G Q_c G^T (e^{A\tau})^T d\tau$$

其中 $Q_c$ 是连续时间过程噪声协方差矩阵。

**数值计算**：当解析积分困难时，可用数值积分或泰勒展开近似：
$$Q_d \approx T \cdot G Q_c G^T + \frac{T^2}{2}(A G Q_c G^T + G Q_c G^T A^T) + \cdots$$

---

## 4. 状态扩增后的 Q 构建

如果你按之前的建议把 bias/drift 扩增为状态：

$$\mathbf{x} = \begin{bmatrix} x \\ b \\ d \end{bmatrix}$$

需要为扩增状态指定各自的噪声：

- **bias $b$**：通常建模为**随机常数**（random constant），即 $\dot{b} = 0$，所以 $Q$ 中对应 $b$ 的噪声项为 0 或极小值。如果 bias 会缓慢漂移，可设为小的随机游走：$Q_{bb} = \sigma_b^2 T$。
- **drift $d$**：通常建模为**随机游走**：$\dot{d} = w_d(t)$，所以 $Q_{dd} = S_d T$。

完整的 Q 矩阵可能是分块对角或耦合形式，取决于噪声源是否独立。

---

## 5. 工程调参的实用技巧

### 5.1 从物理意义出发定量级

问自己：在一个采样周期 $T$ 内，由于模型不确定性，状态各分量最多可能变化多少？

| 状态分量 | 最大不确定变化 $\Delta x_{\max}$ | 对应 Q 对角元 |
|---------|-------------------------------|-------------|
| 位置 | $\Delta x$ | $\approx (\Delta x)^2$ |
| 速度 | $\Delta v$ | $\approx (\Delta v)^2$ |

### 5.2 利用新息（Innovation）检验

运行滤波器后，检查新息序列 $\mathbf{y}_n = \mathbf{z}_n - H\hat{\mathbf{x}}_{n,n-1}$：

- 如果新息均值**显著不为零** → 模型有偏，可能需要扩增状态或增大 Q。
- 如果新息方差**远大于理论值** $S = HPH^T + R$ → Q 太小，模型过于自信。
- 如果新息方差**远小于理论值** → Q 太大，滤波器过度依赖测量。

### 5.3 自适应 Q（进阶）

如果噪声特性时变，可采用：
- **Sage-Husa 自适应滤波**：在线估计 Q。
- **多模型滤波（IMM）**：切换不同 Q 的模型。
- **模糊/神经网络调参**：根据工况动态调整。

---

## 6. 常见误区

| 误区 | 问题 | 正确做法 |
|------|------|---------|
| Q 设为零 | 滤波器过度自信，一旦模型不准就发散 | 至少保留一个小的正定 Q |
| Q 各元素随意设定 | 单位不一致，滤波器性能差 | 从物理噪声源推导，注意量纲 |
| Q 与 R 比例失衡 | 估计震荡或滞后 | 通过新息协方差检验调整 |
| 忽略采样周期 T 的影响 | Q 与 T 的幂次相关，T 变化后性能突变 | 重新推导或缩放 Q |

---

## 7. 快速参考公式

| 模型 | 关键假设 | Q 矩阵 |
|------|---------|--------|
| 一维恒定 | 状态本身有随机扰动 | $Q = \sigma_w^2$ |
| 匀速 CV | 加速度为白噪声 | $Q = S_w \begin{bmatrix} T^3/3 & T^2/2 \\ T^2/2 & T \end{bmatrix}$ |
| 匀加速 CA | jerk 为白噪声 | $Q = S_w \begin{bmatrix} T^5/20 & T^4/8 & T^3/6 \\ T^4/8 & T^3/3 & T^2/2 \\ T^3/6 & T^2/2 & T \end{bmatrix}$ |
| 随机游走 | 状态导数为白噪声 | $Q = S_w T$ |

---

**总结**：构建 Q 的正确顺序是：**识别物理噪声源 → 写出连续时间模型 → 离散化映射到状态空间 → 计算协方差积分**。避免直接"拍脑袋"填数字，否则滤波器要么发散要么过度平滑。

<!--stackedit_data:
eyJoaXN0b3J5IjpbMjk5OTM4NTY3LC0xMzExMTk1NDU1LDE5OT
c4Nzk3NjcsLTE2MTU4ODI1MDcsMTYxMTY3OTddfQ==
-->