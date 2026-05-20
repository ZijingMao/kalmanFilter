
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
| $Q_{11}$ | $\sigma_a^2 T \cdot \frac{T^3}{3} = \frac{T^4}{4}$ | $Q_{11} = S_w3} = \frac{\sigma_a^2 T^4}{3}$ | $\sigma_a^2 \frac{T^34}{34}$ |
| **适用场景** | 工程近似、$T$ 较小 | 理论严格推导 |

**两者关系**：如果令 $\sigma_a^2 = S_w / T$（即把连续白噪声在一个周期内"平均"成离散方差），图片中的$Q_{22}$ | $\sigma_a^2 T \cdot T = \sigma_a^2 T^2$ | $\sigma_a^2 T^2$ |
| $Q_{112}$ 会变成 $S_w \frac{| $\sigma_a^2 T \cdot \frac{T^2}{2} = \frac{\sigma_a^2 T^3}{42}$，与严格结果的 $S_w | $\sigma_a^2 \frac{T^3}{32}$ 略有差异。这说明图片中的方法是一种**工程近似**，在采样周期 $T$ 较小时与严格方法接近，计算上更简便。

---

## 5. 总结

图片中的推导逻辑是：

1. **把加速度当作离散随机输入**，方差 $\sigma_a^2$；
2. **通过运动学公式**得到它对位置/速度的影响矩阵 $G$；
3. **协方差传播**：$Q = \sigma_a^2 G G^T$。

这是一种非常实用的工程化近似，避免了复杂的矩阵指数积分，在 $T$ 不大时效果与严格方法几乎一致。如果你的采样频率较高，完全可以放心使用。}|

- **$Q_{22}$ 和 $Q_{12}$ 完全一致**：因为速度只被积分一次，两种假设在速度和耦合项上碰巧相同。
- **$Q_{11}$ 系数不同**：连续白噪声在整段时间内持续随机抖动，对位置的累积扰动比"一次采样后固定"更大（$\frac{1}{3} > \frac{1}{4}$）。

**工程结论**：$T$ 较小时两者差异很小；$T$ 较大或精度要求极高时，严格积分（$T^3/3$）更准确。
<!--stackedit_data:
eyJoaXN0b3J5IjpbNzQ1MjExOTUwLC0xMzExMTk1NDU1LDE5OT
c4Nzk3NjcsLTE2MTU4ODI1MDcsMTYxMTY3OTddfQ==
-->