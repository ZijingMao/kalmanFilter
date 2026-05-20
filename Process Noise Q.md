
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
是的，**理论值就是 $S = HPH^T + R$**（有时文献里也写作 $\Sigma$ 或 $S_k$）。

这句话背后的逻辑是一条完整的因果链，核心在于：**新息（innovation）是滤波器自我诊断的"体检报告"**。

---

## 1. 新息是什么？

$$\mathbf{y}_k = \mathbf{z}_k - H\hat{\mathbf{x}}_{k|k-1}$$

即**实际测量值**减去**基于模型的预测测量值**。它回答的问题是：

> "根据我的模型，我预期测量值应该是多少？实际测到的和我预期的差了多少？"

如果滤波器设计正确，新息应该是**零均值的白噪声**——因为真实世界的随机扰动不可预测，有时正有时负，平均下来为0。

---

## 2. 理论值 $S = HPH^T + R$ 从哪来？

这是**假设模型完全正确、且噪声统计已知**时，新息"应该"具有的协方差。

推导很简单：

$$\mathbf{y}_k = H\mathbf{x}_k + \mathbf{v}_k - H\hat{\mathbf{x}}_{k|k-1} = H(\mathbf{x}_k - \hat{\mathbf{x}}_{k|k-1}) + \mathbf{v}_k = H\tilde{\mathbf{x}}_{k|k-1} + \mathbf{v}_k$$

其中：
- $\tilde{\mathbf{x}}_{k|k-1}$ 是预测误差，协方差为 $P_{k|k-1}$（先验估计误差协方差）
- $\mathbf{v}_k$ 是测量噪声，协方差为 $R$

假设两者独立，则：

$$\boxed{S_k = E[\mathbf{y}_k \mathbf{y}_k^T] = H P_{k|k-1} H^T + R}$$

**物理意义**：理论新息方差由两部分组成——
1. **$HPH^T$**：我对状态的预测本身就不准（预测误差投影到测量空间）
2. **$R$**：传感器本身就有噪声

两者共同决定了"预测"和"测量"之间**理应存在多大的正常偏差**。

---

## 3. 实际新息方差 vs 理论值

实际运行滤波器时，你可以统计新息序列的**样本协方差**：

$$\hat{S}_k = \frac{1}{N}\sum_{i=k-N+1}^{k} \mathbf{y}_i \mathbf{y}_i^T$$

然后比较 $\hat{S}_k$（实际）和 $S_k$（理论）。

---

## 4. "实际 >> 理论" 的物理逻辑

如果实际新息方差 **远大于** $HPH^T + R$，说明：

> **"预测"和"测量"之间的冲突，比滤波器认为的正常水平剧烈得多。**

为什么会这样？因为滤波器**错误地低估了预测的不确定性**。

具体因果链：

1. **Q 设得太小** → 过程噪声被低估
2. **$P_{k|k-1} = F P_{k-1} F^T + Q$ 增长不足** → 预测协方差太小
3. **理论新息协方差 $S = HPH^T + R$ 被低估** → 滤波器认为"预测很准，测量偏差应该很小"
4. **卡尔曼增益 $K = PH^T S^{-1}$ 偏小** → 滤波器更相信预测，不太愿意相信测量
5. **但真实世界的过程噪声其实很大**（或有未建模动态/bias）→ 预测值持续偏离真实轨迹
6. **实际测量值与预测值频繁大幅偏离** → 实际新息方差很大

**一句话总结**：滤波器以为自己的预测很靠谱（$P$ 小），所以觉得测量值应该乖乖待在预测附近。但现实中预测已经跑偏了，测量值"不听话"地大幅偏离，滤波器却拒绝及时调整，导致新息持续很大。

---

## 5. 类比：自信的司机与导航

想象你在开车，导航（模型）说"前方100米直行"。

- **$P$ 小（Q 小）**：导航极其自信，认为"这条路我熟，不可能偏"。
- **$R$**：GPS 定位误差。
- **理论 $S$**：导航认为"你实际看到的 GPS 位置应该只在我预测位置附近 $\pm$2米内"。

但实际情况是路上有**大坑**（未建模的过程噪声），车不断颠簸偏移。

- 导航坚持"不可能偏"，拒绝根据 GPS 修正（$K$ 小）。
- 你一看 GPS，发现实际位置偏离了预测 **10米**。
- 新息 = 10米，远大于导航预期的 2米。

这说明导航**过于自信**，没有给"路面颠簸"（过程噪声）留足够余地——对应 Q 太小。

---

## 6. 反过来：实际 << 理论

如果实际新息方差 **远小于** $HPH^T + R$，说明：

> 滤波器认为预测和测量应该偏差很大，但实际它们很吻合。

这通常意味着 **Q 太大** 或 **R 太大**。滤波器过度怀疑自己的预测，把测量权重拉得很高，结果预测和测量几乎重合，新息很小。滤波器"过于自卑"，估计值过度抖动。

---

## 7. 工程用法：新息归一化检验

实际工程中常做**归一化新息平方（NIS）检验**：

$$\epsilon_k = \mathbf{y}_k^T S_k^{-1} \mathbf{y}_k$$

理论上 $\epsilon_k \sim \chi^2$（卡方分布），自由度等于测量维度。

- 如果 $\epsilon_k$ 持续 **> 阈值** → $S_k$ 被低估 → 增大 Q（或检查模型）
- 如果 $\epsilon_k$ 持续 **<< 阈值** → $S_k$ 被高估 → 减小 Q 或 R

这是卡尔曼滤波调参最客观的依据——**让滤波器的"自我预期"与"实际表现"匹配**。

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
eyJoaXN0b3J5IjpbLTM4NTExOTI1MSwtMTY3MDgyODcwNywxNz
cwOTM2MzExLC0xMTE2NDk4MTUwLC0xMzExMTk1NDU1LDE5OTc4
Nzk3NjcsLTE2MTU4ODI1MDcsMTYxMTY3OTddfQ==
-->