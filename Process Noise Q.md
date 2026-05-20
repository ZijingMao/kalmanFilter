
## 0. 过程噪声Q的结果是：

$$Q = \sigma_a^2 \begin{bmatrix} \frac{T^4}{4} & \frac{T^3}{2} \\ \frac{T^3}{2} & T^2 \end{bmatrix}$$

下面解释这个推导的完整过程，以及它背后的物理假设。

---

## 1. 物理假设：分段恒定加速度（Piecewise Constant Acceleration）

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

$$Q_{\text{连续}} = S_w \begin{bmatrix} \frac{T^3}{3} & \frac{T^2}{2} \\ \frac{T^2}{2} & T \end{bmatrix}$$

是基于**连续时间白噪声**假设（加速度是功率谱密度为 $S_w$ 的白噪声），通过积分 $Q = \int_0^T e^{A\tau} G S_w G^T e^{A^T\tau} d\tau$ 得到的。

| | 图片中的方法 | 严格连续时间积分 |
|---|---|---|
| **假设** | 每个周期内加速度恒定 | 加速度是连续白噪声 |
| **参数** | $\sigma_a^2$（离散方差） | $S_w$（功率谱密度，单位不同）|
| **结果** | $Q_{11} = \sigma_a^2 \frac{T^4}{4}$ | $Q_{11} = S_w \frac{T^3}{3}$ |
| **适用场景** | 工程近似、$T$ 较小 | 理论严格推导 |

**两者关系**：如果令 $\sigma_a^2 = S_w / T$（即把连续白噪声在一个周期内"平均"成离散方差），图片中的 $Q_{11}$ 会变成 $S_w \frac{T^3}{4}$，与严格结果的 $S_w \frac{T^3}{3}$ 略有差异。这说明图片中的方法是一种**工程近似**，在采样周期 $T$ 较小时与严格方法接近，计算上更简便。

---

## 5. 总结

图片中的推导逻辑是：

1. **把加速度当作离散随机输入**，方差 $\sigma_a^2$；
2. **通过运动学公式**得到它对位置/速度的影响矩阵 $G$；
3. **协方差传播**：$Q = \sigma_a^2 G G^T$。

这是一种非常实用的工程化近似，避免了复杂的矩阵指数积分，在 $T$ 不大时效果与严格方法几乎一致。如果你的采样频率较高，完全可以放心使用。}
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTYxMTY3OTddfQ==
-->