---
title: 高斯分布 (2) - 一維到多維的分布形式
date: 2026-03-18 22:33:35
updated: 2026-03-18 22:33:35
categories: Math
mathjax: true
---

###### 此為 **高斯分布** 系列文章 - 第 2 篇：

1. <a href="/2026/03/18/高斯分布-1-從共變異數矩陣出發/" target="_blank">高斯分布 (1) - 從共變異數矩陣出發</a>
2. <a href="/2026/03/18/高斯分布-2-一維到多維的分布形式/" target="_blank">高斯分布 (2) - 一維到多維的分布形式</a>
3. <a href="/2026/03/18/高斯分布-3-仿射變換後的性質變化/" target="_blank">高斯分布 (3) - 仿射變換後的性質變化</a>

## 前言

上一篇文章中學習了高斯分布的基礎數學 - **共變異數矩陣**，這篇文章則是從一維高斯分布出發，逐漸了解到三維甚至多維高斯分布的數學形式，而在這之中我們也可以看到 **共變異數矩陣** 在高斯分布中的重要性

## 介紹

**高斯分布(Gaussian Distribution)**，也稱為**常態分布(Normal Distribution)**，是我們在生活中和統計學裡最常遇見的機率分布

我們常常聽到的經驗法則：「大約有 68% 的數據（例如身高、考試成績），會落在平均值正負 1 個標準差以內」，這裡所指的「鐘型曲線」規律，其實就是高斯分布的特性

為什麼它這麼重要？因為自然界中絕大多數的現象，最終都會不約而同地呈現出這種形狀。加上它在數學計算上非常直觀又方便，這使得它成為機器學習和建立各種數據模型時最常用的基礎

## 一維高斯分布

### 基本表示

若隨機變數 $X$ 服從均值為 $\mu$、變異數為 $\sigma^2$ 的一維高斯分布，可記為：

$$X \sim \mathcal{N}(\mu, \sigma^2)$$

### 機率密度表示

其 [機率密度函數（PDF）](https://zh.wikipedia.org/zh-tw/%E6%A9%9F%E7%8E%87%E5%AF%86%E5%BA%A6%E5%87%BD%E6%95%B8) 為：

$$f(x) = \frac{1}{\sigma\sqrt{2\pi}} \exp\left(-\frac{(x-\mu)^2}{2\sigma^2}\right)$$

其中：

- $\mu$ 是 **均值（mean）**
- $\sigma^2$ 是 **變異數（variance）**
- $\sigma$ 是 **標準差（standard deviation）**

<!-- more -->

<img src="./gaussian_1d.png" style="max-width: 600px;">

[圖片程式碼](https://github.com/bcjohnblue/python-plot/blob/main/2026/03/gaussian/one-dimension.py)

從圖中可以看出 **均值 $\mu$** 決定分布的中心位置，而 **標準差 $\sigma^2$** 決定分布的寬度，值越大分布就越寬

### 標準化形式

當 $\mu = 0$ 且 $\sigma = 1$ 時，稱為標準高斯分布：

$$f(x) = \frac{1}{\sqrt{2\pi}} \exp\left(-\frac{x^2}{2}\right)$$

這種高斯分布就是我們時常聽到的在多少標準差內會有多少比例的數值：

大約 68.2% 的數值會落在平均值 ±1 個標準差的範圍內，約 95.4% 落在 ±2 個標準差內，而有約 99.7% 落在 ±3 個標準差內

<img src="./gaussian_1d_normal.png" style="max-width: 600px;">

[圖片程式碼](https://github.com/bcjohnblue/python-plot/blob/main/2026/03/gaussian/one-dimension-normal.py)

## 二維高斯分布

### 基本形式

二維高斯分布的機率密度函數為：

$$f(x, y) = \frac{1}{2\pi\sqrt{|\Sigma|}} \exp\left(-\frac{1}{2}(\mathbf{x} - \boldsymbol{\mu})^T \Sigma^{-1} (\mathbf{x} - \boldsymbol{\mu})\right)$$

將矩陣部分展開表示為：

{% raw %}

$$f(x, y) = \frac{1}{2\pi\sqrt{|\Sigma|}} \exp\left(-\frac{1}{2} \begin{bmatrix} x - \mu_x & y - \mu_y \end{bmatrix} \Sigma^{-1} \begin{bmatrix} x - \mu_x \\ y - \mu_y \end{bmatrix}\right)$$

{% endraw %}

其中：

- {% raw %} $\mathbf{x} = \begin{bmatrix} x \\ y \end{bmatrix}$ {% endraw %} 是二維隨機向量
- {% raw %} $\boldsymbol{\mu} = \begin{bmatrix} \mu_x \\ \mu_y \end{bmatrix}$ {% endraw %} 是均值向量
- $\Sigma$ 是 **共變異數矩陣（covariance matrix）**

### 共變異數矩陣

二維情況下的共變異數矩陣為：

{% raw %}

$$
\Sigma = \begin{bmatrix}
\sigma_x^2 & \sigma_{xy} \\
\sigma_{yx} & \sigma_y^2
\end{bmatrix} = \begin{bmatrix}
\sigma_x^2 & \rho\sigma_x\sigma_y \\
\rho\sigma_x\sigma_y & \sigma_y^2
\end{bmatrix}
$$

{% endraw %}

其中：

- $\sigma_x^2, \sigma_y^2$ 分別是 $x$ 和 $y$ 的變異數
- $\sigma_{xy} = \sigma_{yx} = \rho\sigma_x\sigma_y$ 是共變異數
- $\rho$ 是相關係數，範圍在 $[-1, 1]$

### 不同分布情況

#### 1. 均值 ($\mu$)

均值的數值決定高斯分布的中心點

<img src="./gaussian_2d_mu.png" style="max-width: 600px;">

[圖片程式碼](https://github.com/bcjohnblue/python-plot/blob/main/2026/03/gaussian/two-dimension.py)

#### 2. 等方差（$\sigma_x = \sigma_y = \sigma$）

當兩個維度的變異數相同且相互獨立時，共變異數矩陣可寫為：

{% raw %}

$$
\Sigma = \begin{bmatrix}
\sigma^2 & 0 \\
0 & \sigma^2
\end{bmatrix}
$$

{% endraw %}

此時等高線是以均值為中心的同心圓：

<img src="./gaussian_2d_circle.png" style="max-width: 600px;">

[圖片程式碼](https://github.com/bcjohnblue/python-plot/blob/main/2026/03/gaussian/two-dimension.py)

#### 3. 獨立變數 ($\rho = 0$)

當兩個變數獨立時，共變異數矩陣為對角矩陣：

{% raw %}

$$
\Sigma = \begin{bmatrix}
\sigma_x^2 & 0 \\
0 & \sigma_y^2
\end{bmatrix}
$$

{% endraw %}

此時機率密度函數可以分解為兩個一維高斯分布的乘積：

$$f(x, y) = f_x(x) \cdot f_y(y) = \frac{1}{\sqrt{2\pi\sigma_x^2}} \exp\left(-\frac{(x-\mu_x)^2}{2\sigma_x^2}\right) \cdot \frac{1}{\sqrt{2\pi\sigma_y^2}} \exp\left(-\frac{(y-\mu_y)^2}{2\sigma_y^2}\right)$$

畫出來是橢圓的形狀：

<img src="./gaussian_2d_indepedent.png" style="max-width: 600px;">

[圖片程式碼](https://github.com/bcjohnblue/python-plot/blob/main/2026/03/gaussian/two-dimension.py)

#### 4. 變異數 ($\sigma$)

變異數越大，分布越分散，等高線橢圓在對應軸方向會被拉得更開

<img src="./gaussian_2d_sigma.png" style="max-width: 600px;">

[圖片程式碼](https://github.com/bcjohnblue/python-plot/blob/main/2026/03/gaussian/two-dimension.py)

#### 5. 相關係數 ($\rho$)

相關係數影響橢圓的傾斜程度

<img src="./gaussian_2d_rho_0.8.png" style="max-width: 600px;">

[圖片程式碼](https://github.com/bcjohnblue/python-plot/blob/main/2026/03/gaussian/two-dimension.py)

## 三維高斯分布

### 基本形式

三維高斯分布的機率密度函數為：

$$f(x, y, z) = \frac{1}{(2\pi)^{3/2}\sqrt{|\Sigma|}} \exp\left(-\frac{1}{2}(\mathbf{x} - \boldsymbol{\mu})^T \Sigma^{-1} (\mathbf{x} - \boldsymbol{\mu})\right)$$

將矩陣部分展開表示為：

{% raw %}

$$f(x, y, z) = \frac{1}{(2\pi)^{3/2}\sqrt{|\Sigma|}} \exp\left(-\frac{1}{2} \begin{bmatrix} x - \mu_x & y - \mu_y & z - \mu_z \end{bmatrix} \Sigma^{-1} \begin{bmatrix} x - \mu_x \\ y - \mu_y \\ z - \mu_z \end{bmatrix}\right)$$

{% endraw %}

其中：

- {% raw %} $\mathbf{x} = \begin{bmatrix} x \\ y \\ z \end{bmatrix}$ {% endraw %} 是三維隨機向量
- {% raw %} $\boldsymbol{\mu} = \begin{bmatrix} \mu_x \\ \mu_y \\ \mu_z \end{bmatrix}$ {% endraw %} 是均值向量
- $\Sigma$ 是 **共變異數矩陣（covariance matrix）**

### 共變異數矩陣

三維情況下的共變異數矩陣為：

{% raw %}

$$
\Sigma = \begin{bmatrix}
\sigma_x^2 & \sigma_{xy} & \sigma_{xz} \\
\sigma_{yx} & \sigma_y^2 & \sigma_{yz} \\
\sigma_{zx} & \sigma_{zy} & \sigma_z^2
\end{bmatrix}
$$

{% endraw %}

其中：

- $\sigma_x^2, \sigma_y^2, \sigma_z^2$ 分別是 $x$、$y$ 和 $z$ 的變異數
- $\sigma_{xy} = \sigma_{yx}$、$\sigma_{xz} = \sigma_{zx}$、$\sigma_{yz} = \sigma_{zy}$ 是共變異數
- 共變異數矩陣是對稱矩陣

### 不同分布情況

#### 1. 均值

均值向量 $\boldsymbol{\mu}$ 決定分布中心在空間中的位置，改變均值只會造成分布整體平移，不會改變形狀與擴散程度（這些由共變異數矩陣 $\Sigma$ 決定）

例如，當 $\mu_y$ 增加時，整個三維高斯分布會沿著 $y$ 軸方向移動：

<img src="./gaussian_3d_mu_y_2.png" style="max-width: 600px;">

[圖片程式碼](https://github.com/bcjohnblue/python-plot/blob/main/2026/03/gaussian/three-dimension.py)

#### 2. 等方差（$\sigma_x = \sigma_y = \sigma_z = \sigma$）

當三個維度的變異數相同且相互獨立時：

{% raw %}

$$
\Sigma = \sigma^2 \begin{bmatrix}
1 & 0 & 0 \\
0 & 1 & 0 \\
0 & 0 & 1
\end{bmatrix} = \sigma^2 I
$$

{% endraw %}

此時的等高面是標準的球體，分布在各個方向上完全對稱

<img src="./gaussian_3d_sphere.png" style="max-width: 600px;">

[圖片程式碼](https://github.com/bcjohnblue/python-plot/blob/main/2026/03/gaussian/three-dimension.py)

#### 3. 獨立變數

當三個變數獨立時，共變異數矩陣為對角矩陣：

{% raw %}

$$
\Sigma = \begin{bmatrix}
\sigma_x^2 & 0 & 0 \\
0 & \sigma_y^2 & 0 \\
0 & 0 & \sigma_z^2
\end{bmatrix}
$$

{% endraw %}

此時機率密度函數可以分解為三個一維高斯分布的乘積：

$$f(x, y, z) = f_x(x) \cdot f_y(y) \cdot f_z(z)$$

在三維空間中，等高面會呈現橢球體的形狀，其主軸與座標軸平行

<img src="./gaussian_3d_sigma_y_2.png" style="max-width: 600px;">

[圖片程式碼](https://github.com/bcjohnblue/python-plot/blob/main/2026/03/gaussian/three-dimension.py)

#### 3. 變數之間有相關性

當變數之間存在線性相關時，共變異數矩陣會出現非零的非對角元素：

{% raw %}

$$
\Sigma = \begin{bmatrix}
\sigma_x^2 & \sigma_{xy} & \sigma_{xz} \\
\sigma_{yx} & \sigma_y^2 & \sigma_{yz} \\
\sigma_{zx} & \sigma_{zy} & \sigma_z^2
\end{bmatrix}
$$

{% endraw %}

其中非對角元素（如 $\sigma_{xy}, \sigma_{xz}, \sigma_{yz}$）表示不同維度之間的共變異數，此時等高面仍是橢球體，但主軸通常不再與座標軸平行，而會發生旋轉

<img src="./gaussian_3d_rotate.png" style="max-width: 600px;">

[圖片程式碼](https://github.com/bcjohnblue/python-plot/blob/main/2026/03/gaussian/three-dimension.py)

## 多維高斯分布

### 基本表示

若將三維情況推廣到一般 $d$ 維，隨機向量 $\mathbf{X}$ 的分布可記為：

$$\mathbf{X} \sim \mathcal{N}(\boldsymbol{\mu}, \Sigma)$$

其中：

- $\mathbf{X}$ 是 $d$ 維隨機向量
- $\boldsymbol{\mu}$ 是均值向量
- $\Sigma$ 是共變異數矩陣

### 機率密度表示

$d$ 維高斯分布的機率密度函數為：

$$f(\mathbf{x}) = \frac{1}{(2\pi)^{d/2}|\Sigma|^{1/2}} \exp\left(-\frac{1}{2}(\mathbf{x} - \boldsymbol{\mu})^T \Sigma^{-1} (\mathbf{x} - \boldsymbol{\mu})\right)$$

其中：

- $\mathbf{x} \in \mathbb{R}^d$ 是 $d$ 維隨機向量
- $\boldsymbol{\mu} \in \mathbb{R}^d$ 是均值向量
- $\Sigma \in \mathbb{R}^{d \times d}$ 是共變異數矩陣（對稱正定）

## 總結

從一維到多維高斯分布的推導，展現了數學上優雅的統一性：

1. **一維**：使用純量 $\mu$ 和 $\sigma^2$ 來描述中心位置與分布的寬度
2. **二維**：擴展為均值向量 $\boldsymbol{\mu}$ 和共變異數矩陣 $\Sigma$，等密度線呈現橢圓形狀
3. **三維**：數學形式不變，同樣由 $\boldsymbol{\mu}$ 和 $\Sigma$ 決定，但在幾何上等密度面從橢圓升級為立體的橢球體
4. **多維**：無論維度多高，數學表示依然保持一致，只需擴展向量與矩陣的維度即可

回顧這一連串的推導，可以發現**共變異數矩陣**正是多維高斯分布的核心。它不僅精準描述了個別變數的離散程度，更捕捉了變數彼此之間的相關性，從而在幾何上完全決定了等密度橢球的長短軸比例與旋轉方向

## 補充資料

### 1. 相關係數

#### 定義

**相關係數(Correlation Coefficient)** 是衡量兩個隨機變數之間線性關係強度的統計量，記為 $\rho$

對於兩個隨機變數 $X$ 和 $Y$，其相關係數定義為：

$$\rho_{XY} = \frac{\text{Cov}(X, Y)}{\sigma_X \sigma_Y}$$

其中：

- $\text{Cov}(X, Y)$ 是 $X$ 和 $Y$ 的共變異數
- $\sigma_X$ 和 $\sigma_Y$ 分別是 $X$ 和 $Y$ 的標準差

#### 範圍

相關係數的範圍為 $-1 \leq \rho \leq 1$，以下我們來證明這一點

##### 證明方法一：利用共變異數矩陣半正定性

考慮兩個隨機變數 $X$ 和 $Y$ 的共變異數矩陣：

{% raw %}

$$
\Sigma = \begin{bmatrix}
\sigma_X^2 & \text{Cov}(X, Y) \\
\text{Cov}(X, Y) & \sigma_Y^2
\end{bmatrix}
$$

{% endraw %}

由於 <a href="/2025/12/10/共變異數矩陣/#半正定性-SPD" target="_blank" style="vertical-align: -6px">共變異數矩陣必定是半正定矩陣</a>，而半正定矩陣的一個重要性質是 <a href="/2025/11/03/正定矩陣與半正定矩陣-1-定義與性質/#半正定矩陣的等價條件" target="_blank">半正定矩陣的所有順序主子式的行列式都大於等於 0</a>

由此得到共變異數矩陣的行列式必須非負：

$$|\Sigma| = \sigma_X^2 \sigma_Y^2 - [\text{Cov}(X, Y)]^2 \geq 0$$

因此：

$$[\text{Cov}(X, Y)]^2 \leq \sigma_X^2 \sigma_Y^2$$

兩邊開平方根：

$$|\text{Cov}(X, Y)| \leq \sigma_X \cdot \sigma_Y$$

兩邊同除以 $\sigma_X \sigma_Y$（由於標準差始終大於 $0$，所以 $\sigma_X, \sigma_Y > 0$）：

$$\left|\frac{\text{Cov}(X, Y)}{\sigma_X \sigma_Y}\right| \leq 1$$

即：

$$|\rho_{XY}| \leq 1$$

得證：

$$-1 \leq \rho_{XY} \leq 1$$

<!-- **補充說明**：

等號成立的條件是共變異數矩陣的行列式為零，即 $|\Sigma| = 0$，這意味著矩陣是奇異的（singular）。此時 $X$ 和 $Y$ 之間存在完美的線性關係：

- 當 $\rho = 1$ 時，$Y = aX + b$（其中 $a > 0$）
- 當 $\rho = -1$ 時，$Y = aX + b$（其中 $a < 0$） -->

##### 證明方法二：利用柯西-施瓦茨不等式

對於任意兩個隨機變數 $X$ 和 $Y$，[柯西-施瓦茨不等式(Cauchy-Schwarz inequality)](https://en.wikipedia.org/wiki/Cauchy%E2%80%93Schwarz_inequality#Probability_theory) 指出：

<!-- $$|E[XY]|^2 \leq E[X^2] \cdot E[Y^2]$$

令 $X' = X - \mu_X$ 和 $Y' = Y - \mu_Y$（中心化後的隨機變數），則：

$$|E[X'Y']|^2 \leq E[(X')^2] \cdot E[(Y')^2]$$

注意到：

- $E[X'Y'] = E[(X - \mu_X)(Y - \mu_Y)] = \text{Cov}(X, Y)$
- $E[(X')^2] = E[(X - \mu_X)^2] = \sigma_X^2 = \text{Var}(X)$
- $E[(Y')^2] = E[(Y - \mu_Y)^2] = \sigma_Y^2 = \text{Var}(Y)$ -->

$$\text{Var}(X) \geq \frac{[\text{Cov}(X, Y)]^2}{\text{Var}(Y)}$$

其中：

- $\text{Cov}(X, Y)$ 是 $X$ 和 $Y$ 的共變異數
- $\text{Var}(X)$ 和 $\text{Var}(Y)$ 分別是 $X$ 和 $Y$ 的變異數

首先將變異數改成用標準差改寫：

$$[\text{Cov}(X, Y)]^2 \leq \sigma_X^2 \cdot \sigma_Y^2$$

兩邊開平方根：

$$|\text{Cov}(X, Y)| \leq \sigma_X \cdot \sigma_Y$$

兩邊同除以 $\sigma_X \sigma_Y$（假設 $\sigma_X, \sigma_Y > 0$）：

$$\left|\frac{\text{Cov}(X, Y)}{\sigma_X \sigma_Y}\right| \leq 1$$

即：

$$|\rho_{XY}| \leq 1$$

得證：

$$-1 \leq \rho_{XY} \leq 1$$

### 2. 馬哈拉諾比斯距離 (Mahalanobis distance)

#### 定義

對於隨機向量 $\mathbf{X} \sim \mathcal{N}(\boldsymbol{\mu}, \Sigma)$，點 $\mathbf{x}$ 到均值 $\boldsymbol{\mu}$ 的馬哈拉諾比斯距離定義為：

$$
d_M(\mathbf{x}, \boldsymbol{\mu}) = \sqrt{(\mathbf{x} - \boldsymbol{\mu})^T \Sigma^{-1} (\mathbf{x} - \boldsymbol{\mu})}
$$

#### 與多維高斯分布的關係

在 [多維高斯分布的機率密度函數](./#機率密度表示-1) 中，指數項為：

$$
-\frac{1}{2}(\mathbf{x} - \boldsymbol{\mu})^T \Sigma^{-1} (\mathbf{x} - \boldsymbol{\mu})
$$

注意到：

$$
(\mathbf{x} - \boldsymbol{\mu})^T \Sigma^{-1} (\mathbf{x} - \boldsymbol{\mu}) = d_M(\mathbf{x}, \boldsymbol{\mu})^2
$$

因此，機率密度其實和「馬哈拉諾比斯距離的平方」直接相關：距離越小，機率密度越高；距離越大，機率密度越低。

這也解釋了為什麼高斯分布的等密度曲線在二維是橢圓，在高維是橢球面。因為當我們固定機率密度為一個常數時，等價於令馬哈拉諾比斯距離的平方等於一個常數 $c$：

$$
(\mathbf{x} - \boldsymbol{\mu})^T \Sigma^{-1} (\mathbf{x} - \boldsymbol{\mu}) = c
$$

由於共變異數矩陣 $\Sigma$ 是正定矩陣，其反矩陣 $\Sigma^{-1}$ 也是正定矩陣。在線性代數中，由正定矩陣構成的 [二次型](/2025/11/03/正定矩陣與半正定矩陣-1-定義與性質/#二次型（Quadratic-Form）) $\mathbf{y}^T A \mathbf{y} = c$（$c>0$）正是橢圓（2D）或橢球面（高維）的標準代數定義，這一段可以複習之前的文章：[正定矩陣與半正定矩陣 (2) - 橢球體的形狀](/2025/11/12/正定矩陣與半正定矩陣-2-橢球體的形狀/)

#### 特殊情況

##### 1. 當 $\Sigma = I$（單位矩陣）時：

$$
d_M(\mathbf{x}, \boldsymbol{\mu}) = \sqrt{(\mathbf{x} - \boldsymbol{\mu})^T(\mathbf{x} - \boldsymbol{\mu})}
$$

此時馬哈拉諾比斯距離就退化為一般歐氏距離

##### 2. 當 $\Sigma$ 為對角矩陣時（各維度獨立）：

設 $\Sigma = \text{diag}(\sigma_1^2, \sigma_2^2, \cdots, \sigma_d^2)$，則

$$
d_M(\mathbf{x}, \boldsymbol{\mu}) = \sqrt{\sum_{i=1}^{d}\frac{(x_i - \mu_i)^2}{\sigma_i^2}}
$$

也就是每個維度先除以自己的標準差後，再做歐氏距離

#### 範例

假設：

{% raw %}

$$
\boldsymbol{\mu} =
\begin{bmatrix}
0 \\
0
\end{bmatrix},\quad
\Sigma =
\begin{bmatrix}
4 & 0 \\
0 & 1
\end{bmatrix},\quad
\mathbf{x} =
\begin{bmatrix}
2 \\
1
\end{bmatrix}
$$

{% endraw %}

則：

{% raw %}

$$
\Sigma^{-1} =
\begin{bmatrix}
\frac{1}{4} & 0 \\
0 & 1
\end{bmatrix}
$$

{% endraw %}

先計算距離平方：

{% raw %}

$$
d_M(\mathbf{x}, \boldsymbol{\mu})^2
=
\begin{bmatrix} 2 & 1 \end{bmatrix}
\begin{bmatrix}
\frac{1}{4} & 0 \\
0 & 1
\end{bmatrix}
\begin{bmatrix}
2 \\
1
\end{bmatrix}
= 2
$$

{% endraw %}

所以：

$$
d_M(\mathbf{x}, \boldsymbol{\mu}) = \sqrt{2}
$$

這表示在考慮共變異數矩陣的尺度後，$\mathbf{x}$ 距離均值約為 $1.414$ 個「標準化單位」

### 3. 多維高斯分佈的共變異數矩陣是對稱且正定的

上一篇文章中我們已經證明 [共變異數矩陣是對稱的半正定矩陣](/2026/03/18/高斯分布-1-從共變異數矩陣出發/#性質)，以下我們從數學定義的角度，說明為什麼在多維高斯分佈中，它必須進一步是**正定矩陣**

回顧多維高斯分佈的機率密度函數：

$$f(\mathbf{x}) = \frac{1}{(2\pi)^{d/2}|\Sigma|^{1/2}} \exp\left(-\frac{1}{2}(\mathbf{x} - \boldsymbol{\mu})^T \Sigma^{-1} (\mathbf{x} - \boldsymbol{\mu})\right)$$

觀察這個公式，我們發現 $\Sigma$ 必須滿足兩個條件才能讓算式成立：

1. **分母有 $|\Sigma|^{1/2}$**：這意味著行列式 $|\Sigma|$ 必須大於 $0$（否則分母為 $0$）
2. **指數項有 $\Sigma^{-1}$**：這意味著 $\Sigma$ 必須是 [可逆矩陣](https://zh.wikipedia.org/zh-tw/%E9%9D%9E%E5%A5%87%E5%BC%82%E6%96%B9%E9%98%B5)

我們已經知道共變異數矩陣是半正定的（所有特徵值 $\lambda_i \geq 0$）
因為 [矩陣的行列式等於所有特徵值的乘積](/2025/11/03/正定矩陣與半正定矩陣-1-定義與性質/#3-矩陣特徵值乘積等於行列式值)（$|\Sigma| = \lambda_1 \lambda_2 \cdots \lambda_d$），如果要讓 $|\Sigma| > 0$ 且矩陣可逆，代表不能有任何特徵值為 $0$

因此所有特徵值必須**嚴格大於 0**（$\lambda_i > 0$）=> 這正是 [正定矩陣的等價條件](/2025/11/03/正定矩陣與半正定矩陣-1-定義與性質/#正定矩陣的等價條件)

所以，為了讓多維高斯分佈的機率密度函數有意義，其共變異數矩陣必須是對稱且正定的

## 相關連結

[高斯分布 (1) - 從共變異數矩陣出發](/2026/03/18/高斯分布-1-從共變異數矩陣出發/)
[正定矩陣與半正定矩陣 (1) - 定義與性質](/2025/11/03/正定矩陣與半正定矩陣-1-定義與性質/)
[正定矩陣與半正定矩陣 (2) - 橢球體的形狀](/2025/11/12/正定矩陣與半正定矩陣-2-橢球體的形狀/)

## 參考資料

[多元高斯分布（The Multivariate normal distribution）](https://www.cnblogs.com/bingjianing/p/9117330.html)
[从零开始推导多元高斯分布](https://zhuanlan.zhihu.com/p/36522776)
[如何理解多维高斯分布？](https://zhuanlan.zhihu.com/p/675264968)
[Multivariate Gaussian distribution](https://www.youtube.com/watch?v=azrTdjrA2bU)
[相關係數與共變異數(Correlation Coefficient and Covariance)](https://chih-sheng-huang821.medium.com/%E7%9B%B8%E9%97%9C%E4%BF%82%E6%95%B8%E8%88%87%E5%85%B1%E8%AE%8A%E7%95%B0%E6%95%B8-correlation-coefficient-and-covariance-c9324c5cf679)
[如何通俗地讲解「仿射变换」？](https://www.zhihu.com/question/20666664)
[wikipedia - 仿射轉換](https://zh.wikipedia.org/zh-tw/%E4%BB%BF%E5%B0%84%E5%8F%98%E6%8D%A2)
[歐式距離及馬氏距離](https://blog.danielchen.cc/2017/10/27/euclidean-distance-and-mahalanobis-distance/)
