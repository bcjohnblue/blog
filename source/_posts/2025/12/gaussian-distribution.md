---
title: 高斯分布的數學表示：從一維到多維
date: 2025-12-14 23:26:47
updated: 2025-12-14 23:26:47
categories: Math
mathjax: true
---

高斯分布（Gaussian Distribution），也稱為常態分布（Normal Distribution），是統計學和機器學習中最重要的機率分布之一。

<!-- more -->

## 一維高斯分布

### 基本形式

一維高斯分布的機率密度函數（PDF）為：

$$f(x) = \frac{1}{\sigma\sqrt{2\pi}} \exp\left(-\frac{(x-\mu)^2}{2\sigma^2}\right)$$

其中：

- $\mu$ 是均值（mean）
- $\sigma^2$ 是變異數（variance）
- $\sigma$ 是標準差（standard deviation）

![](./gaussian_1d.png)

從圖中可以看出 **均值 $\mu$** 決定分布的中心位置，而 **標準差 $\sigma^2$** 決定分布的寬度，值越大分布就越寬

### 標準化形式

當 $\mu = 0$ 且 $\sigma = 1$ 時，稱為標準高斯分布：

$$f(x) = \frac{1}{\sqrt{2\pi}} \exp\left(-\frac{x^2}{2}\right)$$

這種高斯分布就是我們時常聽到的在多少標準差內會有多少比例的數值：

大約 68.2% 的數值會落在平均值 ±1 個標準差的範圍內，約 95.4% 落在 ±2 個標準差內，而有約 99.7% 落在 ±3 個標準差內

![](./gaussian_1d_normal.png)

---

## 二維高斯分布

### 基本形式

二維高斯分布的機率密度函數為：

$$f(x_1, x_2) = \frac{1}{2\pi\sqrt{|\Sigma|}} \exp\left(-\frac{1}{2}(\mathbf{x} - \boldsymbol{\mu})^T \Sigma^{-1} (\mathbf{x} - \boldsymbol{\mu})\right)$$

其中：

- $\mathbf{x} = \begin{bmatrix} x_1 \\ x_2 \end{bmatrix}$ 是二維隨機向量
- $\boldsymbol{\mu} = \begin{bmatrix} \mu_1 \\ \mu_2 \end{bmatrix}$ 是均值向量
- $\Sigma$ 是協變異數矩陣（covariance matrix）

### 協變異數矩陣

二維情況下的協變異數矩陣為：

$$
\Sigma = \begin{bmatrix}
\sigma_1^2 & \sigma_{12} \\
\sigma_{21} & \sigma_2^2
\end{bmatrix} = \begin{bmatrix}
\sigma_1^2 & \rho\sigma_1\sigma_2 \\
\rho\sigma_1\sigma_2 & \sigma_2^2
\end{bmatrix}
$$

其中：

- $\sigma_1^2, \sigma_2^2$ 分別是 $x_1$ 和 $x_2$ 的變異數
- $\sigma_{12} = \sigma_{21} = \rho\sigma_1\sigma_2$ 是協變異數
- $\rho$ 是相關係數，範圍在 $[-1, 1]$

### 特殊情況

#### 1. 獨立變數（$\rho = 0$）

當兩個變數獨立時，協變異數矩陣為對角矩陣：

$$
\Sigma = \begin{bmatrix}
\sigma_1^2 & 0 \\
0 & \sigma_2^2
\end{bmatrix}
$$

此時 PDF 可以分解為：

$$f(x_1, x_2) = f_1(x_1) \cdot f_2(x_2) = \frac{1}{\sqrt{2\pi\sigma_1^2}} \exp\left(-\frac{(x_1-\mu_1)^2}{2\sigma_1^2}\right) \cdot \frac{1}{\sqrt{2\pi\sigma_2^2}} \exp\left(-\frac{(x_2-\mu_2)^2}{2\sigma_2^2}\right)$$

#### 2. 等變異數（$\sigma_1 = \sigma_2 = \sigma$）

$$
\Sigma = \sigma^2 \begin{bmatrix}
1 & \rho \\
\rho & 1
\end{bmatrix}
$$

## 多維高斯分布

### 一般形式

$d$ 維高斯分布的機率密度函數為：

$$f(\mathbf{x}) = \frac{1}{(2\pi)^{d/2}|\Sigma|^{1/2}} \exp\left(-\frac{1}{2}(\mathbf{x} - \boldsymbol{\mu})^T \Sigma^{-1} (\mathbf{x} - \boldsymbol{\mu})\right)$$

其中：

- $\mathbf{x} \in \mathbb{R}^d$ 是 $d$ 維隨機向量
- $\boldsymbol{\mu} \in \mathbb{R}^d$ 是均值向量
- $\Sigma \in \mathbb{R}^{d \times d}$ 是協變異數矩陣（對稱正定）

### 協變異數矩陣的性質

協變異數矩陣 $\Sigma$ 具有以下重要性質：

1. **對稱性**：$\Sigma = \Sigma^T$
2. **正定性**：對於所有非零向量 $\mathbf{v}$，有 $\mathbf{v}^T \Sigma \mathbf{v} > 0$
3. **對角元素**：$\Sigma_{ii} = \text{Var}(x_i)$
4. **非對角元素**：$\Sigma_{ij} = \text{Cov}(x_i, x_j)$

### 馬哈拉諾比斯距離

在多維高斯分布中，指數項中的二次型：

$$D^2 = (\mathbf{x} - \boldsymbol{\mu})^T \Sigma^{-1} (\mathbf{x} - \boldsymbol{\mu})$$

稱為**馬哈拉諾比斯距離**（Mahalanobis distance），它衡量了點 $\mathbf{x}$ 到均值 $\boldsymbol{\mu}$ 的「標準化」距離。

### 特徵值分解

協變異數矩陣可以進行特徵值分解：

$$\Sigma = P \Lambda P^T$$

其中：

- $P$ 是正交矩陣（$P^T P = I$），其列向量是特徵向量
- $\Lambda$ 是對角矩陣，對角元素是特徵值

這意味著我們可以將多維高斯分布旋轉到主成分空間中。

## 幾何解釋

### 等高線

多維高斯分布的等高線（等密度線）是橢球面，其形狀由協變異數矩陣決定：

1. **橢球的主軸**：對應於協變異數矩陣的特徵向量方向
2. **橢球的半軸長度**：與特徵值的平方根成正比
3. **橢球的傾斜**：由特徵向量的方向決定

### 標準化

通過變換 $\mathbf{z} = \Sigma^{-1/2}(\mathbf{x} - \boldsymbol{\mu})$，可以將任意多維高斯分布轉換為標準多維高斯分布：

$$f(\mathbf{z}) = \frac{1}{(2\pi)^{d/2}} \exp\left(-\frac{1}{2}\mathbf{z}^T \mathbf{z}\right)$$

## 應用實例

### 1. 機器學習中的應用

- **高斯混合模型**：用多個高斯分布的加權組合來建模複雜分布
- **高斯過程**：用於回歸和分類的非參數方法
- **貝葉斯推論**：高斯分布的共軛性質使其在貝葉斯統計中非常重要

### 2. 信號處理

- **雜訊建模**：假設雜訊服從高斯分布
- **卡爾曼濾波**：基於高斯分布的狀態估計

### 3. 計算機視覺

- **圖像去噪**：假設圖像雜訊為高斯分布
- **特徵檢測**：使用高斯核進行圖像平滑

## 總結

從一維到多維高斯分布的推導展現了數學的優雅統一性：

1. **一維**：使用標量參數 $\mu$ 和 $\sigma^2$
2. **二維**：使用向量 $\boldsymbol{\mu}$ 和矩陣 $\Sigma$
3. **多維**：使用相同的形式，只是維度擴展

協變異數矩陣是多維高斯分布的核心，它不僅描述了各變數的變異程度，還捕捉了變數間的相關性。這種數學框架為理解複雜的多變量現象提供了強大的工具。

在實際應用中，理解這些數學表示法對於正確使用高斯分布進行建模和推論至關重要。無論是在統計學、機器學習還是信號處理領域，高斯分布都扮演著基礎而重要的角色。

## 參考資料

[多元高斯分布（The Multivariate normal distribution）](https://www.cnblogs.com/bingjianing/p/9117330.html)
[从零开始推导多元高斯分布](https://zhuanlan.zhihu.com/p/36522776)
[如何理解多维高斯分布？](https://zhuanlan.zhihu.com/p/675264968)
