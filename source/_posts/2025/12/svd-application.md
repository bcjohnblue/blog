---
title: 奇異值分解 SVD (2) - 實際應用
date: 2025-12-07 23:48:40
updated: 2025-12-07 23:48:40
categories: 線性代數
mathjax: true
---

###### 此為 **奇異值分解 SVD** 系列文章 - 第 2 篇：

1. <a href="/2025/12/07/奇異值分解-svd-1-定義與性質/" target="_blank">奇異值分解 SVD (1) - 定義與性質</a>
2. <a href="/2025/12/07/奇異值分解-svd-2-實際應用/" target="_blank">奇異值分解 SVD (2) - 實際應用</a>

## 前言

在上一篇文章中，我們詳細介紹了**奇異值分解 (SVD)** 的定義、性質和計算方法

本文將介紹 SVD 的兩個實際應用：

1. **圖像壓縮**：利用 SVD 的低秩近似特性，大幅減少圖像存儲空間
2. **極分解**：從 SVD 推導極分解，理解線性變換的幾何意義

<!-- more -->

## 1. 圖像壓縮

### 1.1 基本原理

圖像可以表示為一個矩陣，其中每個元素代表像素的灰度值（或 RGB 通道值）。對於一張 $m \times n$ 的灰度圖像，我們可以將其視為一個 $m \times n$ 矩陣 $A$

對矩陣 $A$ 進行 SVD 分解：

$$A = Q_1\Sigma Q_2^T$$

其中：

- $Q_1$ 是 $m \times m$ 正交矩陣
- $\Sigma$ 是 $m \times n$ 對角矩陣（奇異值按降序排列：$\sigma_1 \geq \sigma_2 \geq \cdots \geq \sigma_r > 0$）
- $Q_2$ 是 $n \times n$ 正交矩陣
- $r$ 是矩陣 $A$ 的秩

### 1.2 主成分分析與低秩近似

在上一篇 [推導奇異值分解的形式](/2025/12/07/奇異值分解-svd-1-定義與性質/#推導奇異值分解的形式) 中，我們推導出矩陣 $A$ 可以表示為：

{% raw %}

$$
A
=
\begin{bmatrix}
| & | & & | \\
\mathbf{u}_1 & \mathbf{u}_2 & \cdots & \mathbf{u}_m \\
| & | & & |
\end{bmatrix}

\begin{bmatrix}
\sigma_1 & 0 & \cdots & 0 & 0 & \cdots & 0 \\
0 & \sigma_2 & \cdots & 0 & 0 & \cdots & 0 \\
\vdots & \vdots & \ddots & \vdots & \vdots & \ddots & \vdots \\
0 & 0 & \cdots & \sigma_r & 0 & \cdots & 0 \\
0 & 0 & \cdots & 0 & 0 & \cdots & 0 \\
\vdots & \vdots & \ddots & \vdots & \vdots & \ddots & \vdots \\
0 & 0 & \cdots & 0 & 0 & \cdots & 0
\end{bmatrix}

\begin{bmatrix}
- & \mathbf{v}_1^T & - \\
- & \mathbf{v}_2^T & - \\
& \vdots & \\
- & \mathbf{v}_n^T & -
\end{bmatrix}
$$

{% endraw %}

將奇異值 $\sigma$ 乘入帶有 $\mathbf{u}$ 的矩陣中：

{% raw %}

$$
A=
\begin{bmatrix}
| & | & & | \\
\sigma_1\mathbf{u}_1 & \sigma_2\mathbf{u}_2 & \cdots & \sigma_m\mathbf{u}_m \\
| & | & & |
\end{bmatrix}

\begin{bmatrix}
- & \mathbf{v}_1^T & - \\
- & \mathbf{v}_2^T & - \\
& \vdots & \\
- & \mathbf{v}_n^T & -
\end{bmatrix}
$$

{% endraw %}

由於 $\sigma_r$ 後的奇異值皆為 $0$，所以矩陣 $A$ 可以化簡為：

{% raw %}

$$
A=
\begin{bmatrix}
| & | & & | \\
\sigma_1\mathbf{u}_1 & \sigma_2\mathbf{u}_2 & \cdots & \sigma_r\mathbf{u}_r \\
| & | & & |
\end{bmatrix}

\begin{bmatrix}
- & \mathbf{v}_1^T & - \\
- & \mathbf{v}_2^T & - \\
& \vdots & \\
- & \mathbf{v}_r^T & -
\end{bmatrix}
$$

{% endraw %}

利用對稱矩陣的 [譜分解](/2025/10/15/對稱矩陣/#譜分解-Spectral-Decomposition) 定理，可以將 $A$ 寫成：

{% raw %}

$$
\begin{align*}
A
&= \sum_{i=1}^{r} \sigma_i \mathbf{u}_{i} \mathbf{v}_{i}^T \\
&= \sigma_1\mathbf{u}_{1}\mathbf{v}_{1}^T + \sigma_2\mathbf{u}_{2}\mathbf{v}_{2}^T + \cdots + \sigma_r\mathbf{u}_{r}\mathbf{v}_{r}^T
\end{align*}
$$

{% endraw %}

讓我們來分析一下這條算式的特性：

- $\sigma_i$ 是奇異值，按照慣例其值由大排到小 $\sigma_1 \geq \sigma_2 \geq \cdots \geq \sigma_r > 0$

- {% raw %} $\mathbf{u}_{i} \mathbf{v}_{i}^T$ {% endraw %} 是向量外積，因此每一項的 {% raw %} $\mathbf{u}_{i} \mathbf{v}_{i}^T$ {% endraw %} 相乘矩陣的 <a href="/2025/10/18/向量的內積與外積/#外積的-Rank-性質">rank 都等於 1</a>

- 根據 [Frobenius 內積](./#在主成分分析與低秩近似的應用) 的推導，可以得知當 $i \neq j$ 時每一個 {% raw %} $\mathbf{u}_{i} \mathbf{v}_{i}^T$ 都與另一個 $\mathbf{u}_{j} \mathbf{v}_{j}^T$ 互相垂直 {% endraw %}

由上述分析可知，矩陣 $A$ 可以視為 $r$ 項矩陣的加總。由於奇異值 $\sigma_i$ 依序遞減，越前面的項對矩陣 $A$ 的貢獻越大。因此，我們只需選擇前 $k$ 項（$k < r$）相加，就能得到矩陣 $A$ 的良好近似，從而達到資料壓縮的效果。這種利用主要成分來近似原始矩陣的方法，就是 **主成分分析 (Principal Component Analysis)** 的核心概念

#### 1.2.1 主成分分析 (PCA)

在 SVD 分解中，每個主成分 {% raw %} $\sigma_i \mathbf{u}_{i} \mathbf{v}_{i}^T$ {% endraw %} 都與其他主成分 {% raw %} $\sigma_j \mathbf{u}_{j} \mathbf{v}_{j}^T$ {% endraw %} （$i \neq j$）正交。在統計學中，正交代表這些主成分彼此獨立、互不相關。

由於奇異值 {% raw %} $\sigma_1, \sigma_2, \ldots, \sigma_r$ {% endraw %} 依序遞減，前面的主成分對矩陣 $A$ 的貢獻越大。因此，我們可以只保留前 $k$ 個主成分的加總來近似矩陣 $A$：

- **第一主成分** {% raw %} $\sigma_1 \mathbf{u}_{1} \mathbf{v}_{1}^T$ {% endraw %}：對矩陣 $A$ 的貢獻最大
- **第二主成分** {% raw %} $\sigma_2 \mathbf{u}_{2} \mathbf{v}_{2}^T$ {% endraw %}：對矩陣 $A$ 的貢獻次大
- 依此類推...

#### 1.2.2 低秩近似 (Low-Rank Approximation)

由於每一項 {% raw %} $\mathbf{u}_{i} \mathbf{v}_{i}^T$ {% endraw %} 的 rank 都等於 1，前 $k$ 項相加組成的矩陣其 rank 為 $k$。因此，我們可以用一個 **rank 為 $k$ 的矩陣** 來近似原本 rank 為 $r$ 的矩陣 $A$（其中 $k < r$）

**$k$ 階近似**（保留前 $k$ 個奇異值）：

{% raw %}

$$A_k = \sum_{i=1}^{k} \sigma_i \mathbf{u}_{i} \mathbf{v}_{i}^T$$

{% endraw %}

### 1.3 壓縮比

假設原始圖像需要存儲 $m \times n$ 個數值

使用 $k$ 階近似需要存儲：

- $k$ 個向量 $\mathbf{u}_1, \mathbf{u}_2, \ldots, \mathbf{u}_k$：每個是 $m$ 維向量，共 $m \times k$ 個數值
- $k$ 個奇異值 $\sigma_1, \sigma_2, \ldots, \sigma_k$：共 $k$ 個數值
- $k$ 個向量 $\mathbf{v}_1, \mathbf{v}_2, \ldots, \mathbf{v}_k$：每個是 $n$ 維向量，共 $n \times k$ 個數值

總共需要：$mk + k + nk = k(m + n + 1)$ 個數值

壓縮比為：

$$\frac{mn}{k(m + n + 1)}$$

### 1.4 實際應用示例

假設有一張 $264 \times 264$ 的灰度圖像：

- **原始存儲**：$264 \times 264 = 69696$ 個數值
- **使用 $k=52$ 階近似**：$52 \times (264 + 264 + 1) = 27508$ 個數值
- **壓縮比**：約 $2.53:1$
- **存儲空間減少**：約 61%

![](./einstein-low-rank.png)
[圖片來源 - 線性代數 21 頁](https://drive.google.com/file/d/1n_xqbu-q77mrq2nz30I94yFV2pWATLmT/view)

由上圖可以看出 $k=52$ 階近似產生出的圖片幾乎與完整的圖像 $k=264$ 沒有差別，但卻可以有效地將存儲空間大大減少

## 2. 極分解

**極分解 (Polar decomposition)** 是另一個重要的矩陣分解，它將方陣分解為一個**正交矩陣**和一個**半正定矩陣**的乘積。極分解可以從 SVD 直接推導出來，展示了 SVD 的強大應用

### 2.1 定義

對於 $n \times n$ 方陣 $A$，存在兩種極分解形式：

#### 右極分解 (Right polar decomposition)

$$A = UP$$

其中：

- **$U$**：$n \times n$ 正交矩陣
- **$P$**：$n \times n$ 半正定矩陣，且 $P = \sqrt{A^T A}$

「右」的命名是因為半正定矩陣 $P$ 在乘法的**右邊**

#### 左極分解 (Left polar decomposition)

$$A = PU$$

其中：

- **$P$**：$n \times n$ 半正定矩陣，且 $P = \sqrt{AA^T}$
- **$U$**：$n \times n$ 正交矩陣

「左」的命名是因為半正定矩陣 $P$ 在乘法的**左邊**

### 2.2 從 SVD 推導極分解

假設 $n \times n$ 方陣 $A$ 的 SVD 為：

$$A = Q_1\Sigma Q_2^T$$

其中 $Q_1$ 和 $Q_2$ 都是 $n \times n$ 正交矩陣，$\Sigma$ 是 $n \times n$ 對角矩陣（對角元素為奇異值）

#### 右極分解的推導

我們可以在 SVD 中間乘以 $Q_2^TQ_2$ 由於其乘積為 $I$ 單位矩陣保持整體等式不變，將 SVD 重新組合後：

{% raw %}

$$A = Q_1\Sigma Q_2^T = Q_1 (Q_2^TQ_2) \Sigma Q_2^T = (Q_1 Q_2^T)(Q_2 \Sigma Q_2^T)$$

{% endraw %}

定義：

- $U = Q_1 Q_2^T$ (正交矩陣)
- $P = Q_2 \Sigma Q_2^T$ (半正定矩陣)

其中：

- $U$ 由於是 [兩個正交矩陣的乘積](/2025/10/06/正交矩陣/#8-正交矩陣的乘積) 也同樣會是正交矩陣
- $P$ 根據對稱矩陣的 [對角化性質](/2025/10/15/對稱矩陣/#4-對角化性質)，$\Sigma$ 的對角元素(奇異值)即為 $P$ 的特徵值，奇異值皆為非負意味著特徵值也是非負的，由此給出 $P$ 是[半正定矩陣](/2025/11/03/正定矩陣與半正定矩陣-1-定義與性質/#半正定矩陣的等價條件) 的定義

因此：

$$A = UP$$

**驗證 $P = \sqrt{A^T A}$：**

首先計算 $A^T A$：

$$A^T A = (Q_2 \Sigma Q_1^T)(Q_1 \Sigma Q_2^T) = Q_2 \Sigma^2 Q_2^T$$

這裡我們得到 $A^T A$ 的 [對角化形式](/2025/10/07/矩陣對角化/)，因此，$A^T A$ 的平方根可以定義為將對角元素開根號：

$$\sqrt{A^T A} = Q_2 \sqrt{\Sigma^2} Q_2^T = Q_2 \Sigma Q_2^T$$

驗證這個定義的正確性，將 $Q_2 \Sigma Q_2^T$ 平方：

$$(Q_2 \Sigma Q_2^T)^2 = Q_2 \Sigma Q_2^T Q_2 \Sigma Q_2^T = Q_2 \Sigma I \Sigma Q_2^T = Q_2 \Sigma^2 Q_2^T = A^T A$$

得證 $P = Q_2 \Sigma Q_2^T = \sqrt{A^T A}$

#### 左極分解的推導

我們可以在 SVD 中間乘以 $Q_1^TQ_1$ 由於其乘積為 $I$ 單位矩陣保持整體等式不變，將 SVD 重新組合後：

{% raw %}

$$A = Q_1\Sigma Q_2^T = Q_1 (Q_1^TQ_1) \Sigma Q_2^T = (Q_1 \Sigma Q_1^T)(Q_1 Q_2^T)$$

{% endraw %}

定義：

- $P = Q_1 \Sigma Q_1^T$ (半正定矩陣)
- $U = Q_1 Q_2^T$ (正交矩陣)

因此：

$$A = PU$$

**驗證 $P = \sqrt{AA^T}$：**

首先計算 $AA^T$：

$$AA^T = (Q_1 \Sigma Q_2^T)(Q_2 \Sigma Q_1^T) = Q_1 \Sigma^2 Q_1^T$$

這裡我們得到 $AA^T$ 的 [對角化形式](/2025/10/07/矩陣對角化/)，因此，$AA^T$ 的平方根可以定義為將對角元素開根號：

$$\sqrt{AA^T} = Q_1 \sqrt{\Sigma^2} Q_1^T = Q_1 \Sigma Q_1^T$$

驗證這個定義的正確性，將 $Q_1 \Sigma Q_1^T$ 平方：

$$(Q_1 \Sigma Q_1^T)^2 = Q_1 \Sigma Q_1^T Q_1 \Sigma Q_1^T = Q_1 \Sigma I \Sigma Q_1^T = Q_1 \Sigma^2 Q_1^T = AA^T$$

得證 $P = Q_1 \Sigma Q_1^T = \sqrt{AA^T}$

### 2.3 幾何意義

極分解提供了理解線性變換的另一種視角：

1. **$P$（半正定矩陣）**：表示**伸縮變換**，沿著特徵向量方向進行非負的伸縮
2. **$U$（正交矩陣）**：表示 [旋轉/鏡射變換](/2025/10/06/正交矩陣/#9-幾何意義)，保持長度和角度不變

因此：

- **右極分解** $A = UP$：先伸縮，後旋轉
- **左極分解** $A = PU$：先旋轉，後伸縮

### 2.4 計算範例

設 $2 \times 2$ 方陣 $A$ 的 SVD 為：

{% raw %}

$$A = Q_1\Sigma Q_2^T = \begin{bmatrix} 0 & 1 \\ 1 & 0 \end{bmatrix} \begin{bmatrix} 3 & 0 \\ 0 & 2 \end{bmatrix} \begin{bmatrix} 1 & 0 \\ 0 & 1 \end{bmatrix} = \begin{bmatrix} 0 & 2 \\ 3 & 0 \end{bmatrix}$$

{% endraw %}

#### 右極分解 $A = UP$

計算 $U = Q_1 Q_2^T$：

{% raw %}

$$U = \begin{bmatrix} 0 & 1 \\ 1 & 0 \end{bmatrix} \begin{bmatrix} 1 & 0 \\ 0 & 1 \end{bmatrix} = \begin{bmatrix} 0 & 1 \\ 1 & 0 \end{bmatrix}$$

{% endraw %}

計算 $P = Q_2 \Sigma Q_2^T$：

{% raw %}

$$P = \begin{bmatrix} 1 & 0 \\ 0 & 1 \end{bmatrix} \begin{bmatrix} 3 & 0 \\ 0 & 2 \end{bmatrix} \begin{bmatrix} 1 & 0 \\ 0 & 1 \end{bmatrix} = \begin{bmatrix} 3 & 0 \\ 0 & 2 \end{bmatrix}$$

{% endraw %}

驗證：

{% raw %}

$$UP = \begin{bmatrix} 0 & 1 \\ 1 & 0 \end{bmatrix} \begin{bmatrix} 3 & 0 \\ 0 & 2 \end{bmatrix} = \begin{bmatrix} 0 & 2 \\ 3 & 0 \end{bmatrix} = A$$

{% endraw %}

**幾何意義**：先將向量沿 $x$ 軸伸縮 3 倍、沿 $y$ 軸伸縮 2 倍，再交換 $x$ 和 $y$ 坐標（鏡射）

#### 左極分解 $A = PU$

計算 $P = Q_1 \Sigma Q_1^T$：

{% raw %}

$$P = \begin{bmatrix} 0 & 1 \\ 1 & 0 \end{bmatrix} \begin{bmatrix} 3 & 0 \\ 0 & 2 \end{bmatrix} \begin{bmatrix} 0 & 1 \\ 1 & 0 \end{bmatrix} = \begin{bmatrix} 2 & 0 \\ 0 & 3 \end{bmatrix}$$

{% endraw %}

計算 $U = Q_1 Q_2^T$（與右極分解相同）：

{% raw %}

$$U = \begin{bmatrix} 0 & 1 \\ 1 & 0 \end{bmatrix}$$

{% endraw %}

驗證：

{% raw %}

$$PU = \begin{bmatrix} 2 & 0 \\ 0 & 3 \end{bmatrix} \begin{bmatrix} 0 & 1 \\ 1 & 0 \end{bmatrix} = \begin{bmatrix} 0 & 2 \\ 3 & 0 \end{bmatrix} = A$$

{% endraw %}

**幾何意義**：先交換 $x$ 和 $y$ 坐標（鏡射），再將向量沿 $x$ 軸伸縮 2 倍、沿 $y$ 軸伸縮 3 倍

## 補充資料

### 1. 弗羅比尼烏斯內積 (Frobenius inner product)

**弗羅比尼烏斯內積 (Frobenius inner product)** 是矩陣之間的內積運算，是向量內積在矩陣空間的推廣

#### 定義

對於兩個 $m \times n$ 矩陣 $A$ 和 $B$，Frobenius 內積定義為：

$$\langle A, B \rangle_F = \sum_{i=1}^{m} \sum_{j=1}^{n} A_{ij} B_{ij}$$

這代表矩陣對應元素乘積之和

#### 矩陣形式

Frobenius 內積也可以用矩陣的 [跡(trace)](https://zh.wikipedia.org/zh-tw/%E8%B7%A1) 表示：

$$\langle A, B \rangle_F = \text{tr}(A^T B) = \text{tr}(B^T A)$$

其中 $\text{tr}(M)$ 表示矩陣 $M$ 的跡（對角線元素之和）

#### 與向量內積的關係

如果將矩陣 $A$ 和 $B$ 的列向量分別記為 $\mathbf{a}_j$ 和 $\mathbf{b}_j$（$j = 1, 2, \ldots, n$），則：

$$\langle A, B \rangle_F = \sum_{j=1}^{n} \mathbf{a}_j \cdot \mathbf{b}_j$$

這表明 Frobenius 內積等於對應列向量的內積之和

#### 計算範例

設兩個 $2 \times 3$ 矩陣：

{% raw %}

$$A = \begin{bmatrix} 2 & 0 & 6 \\ 1 & -1 & 2 \end{bmatrix}, \quad B = \begin{bmatrix} 8 & -3 & 2 \\ 4 & 1 & -5 \end{bmatrix}$$

{% endraw %}

**方法一：直接計算對應元素乘積之和**

$$\langle A, B \rangle_F = \sum_{i=1}^{2} \sum_{j=1}^{3} A_{ij} B_{ij}$$

展開計算：

$$\langle A, B \rangle_F = 2 \cdot 8 + 0 \cdot (-3) + 6 \cdot 2 + 1 \cdot 4 + (-1) \cdot 1 + 2 \cdot (-5)$$

$$= 16 + 0 + 12 + 4 - 1 - 10 = 21$$

**方法二：使用跡的形式**

首先計算 $A^T B$：

{% raw %}

$$A^T B = \begin{bmatrix} 2 & 1 \\ 0 & -1 \\ 6 & 2 \end{bmatrix} \begin{bmatrix} 8 & -3 & 2 \\ 4 & 1 & -5 \end{bmatrix} = \begin{bmatrix} 20 & -5 & -1 \\ -4 & -1 & 5 \\ 56 & -16 & 2 \end{bmatrix}$$

{% endraw %}

計算跡（對角線元素之和）：

$$\text{tr}(A^T B) = 20 + (-1) + 2 = 21$$

兩種方法得到相同的結果：$\langle A, B \rangle_F = 21$

#### 在主成分分析與低秩近似的應用

在 SVD 分解中，矩陣 $A$ 可以表示為：

{% raw %}

$$A = \sum_{i=1}^{r} \sigma_i \mathbf{u}_{i} \mathbf{v}_{i}^T$$

{% endraw %}

我們要證明當 $i \neq j$ 時，矩陣 $\mathbf{u}_i \mathbf{v}_i^T$ 和 $\mathbf{u}_j \mathbf{v}_j^T$ 在 Frobenius 內積意義下互相垂直

**證明**：

使用 Frobenius 內積的跡形式，計算兩個 rank-1 矩陣的內積：

$$\langle \mathbf{u}_i \mathbf{v}_i^T, \mathbf{u}_j \mathbf{v}_j^T \rangle_F = \text{tr}\left((\mathbf{u}_i \mathbf{v}_i^T)^T (\mathbf{u}_j \mathbf{v}_j^T)\right)$$

展開轉置：

$$(\mathbf{u}_i \mathbf{v}_i^T)^T = (\mathbf{v}_i^T)^T \mathbf{u}_i^T = \mathbf{v}_i \mathbf{u}_i^T$$

因此：

$$\langle \mathbf{u}_i \mathbf{v}_i^T, \mathbf{u}_j \mathbf{v}_j^T \rangle_F = \text{tr}(\mathbf{v}_i \mathbf{u}_i^T \mathbf{u}_j \mathbf{v}_j^T)$$

注意到 $\mathbf{u}_i^T \mathbf{u}_j$ 是一個純量（向量內積），可以提出：

$$\text{tr}(\mathbf{v}_i \mathbf{u}_i^T \mathbf{u}_j \mathbf{v}_j^T) = \text{tr}(\mathbf{v}_i (\mathbf{u}_i^T \mathbf{u}_j) \mathbf{v}_j^T) = (\mathbf{u}_i^T \mathbf{u}_j) \text{tr}(\mathbf{v}_i \mathbf{v}_j^T)$$

我們將 $\mathbf{u}_i^T \mathbf{u}_j$ 寫成內積的分量形式：

$$(\mathbf{u}_i^T \mathbf{u}_j) \text{tr}(\mathbf{v}_i \mathbf{v}_j^T) = (\mathbf{u}_i \cdot \mathbf{u}_j) \text{tr}(\mathbf{v}_i \mathbf{v}_j^T)$$

接著我們需要計算 $\text{tr}(\mathbf{v}_i \mathbf{v}_j^T)$

設 $\mathbf{v}_i$ 和 $\mathbf{v}_j$ 都是 $n \times 1$ 的向量，則 $\mathbf{v}_i \mathbf{v}_j^T$ 是一個 $n \times n$ 矩陣。這個矩陣的第 $k$ 行第 $k$ 列（對角線）元素為：

{% raw %}
$$(\mathbf{v}_i \mathbf{v}_j^T)_{kk} = (\mathbf{v}_i)_k (\mathbf{v}_j)_k$$

{% endraw %}

其中 $(\mathbf{v}_i)_k$ 表示向量 $\mathbf{v}_i$ 的第 $k$ 個分量

矩陣的跡是對角線元素之和：

{% raw %}

$$\text{tr}(\mathbf{v}_i \mathbf{v}_j^T) = \sum_{k=1}^{n} (\mathbf{v}_i)_k (\mathbf{v}_j)_k = \mathbf{v}_i \cdot \mathbf{v}_j$$

{% endraw %}

因此：

$$\text{tr}(\mathbf{v}_i \mathbf{v}_j^T) = \mathbf{v}_i \cdot \mathbf{v}_j$$

所以：

$$\langle \mathbf{u}_i \mathbf{v}_i^T, \mathbf{u}_j \mathbf{v}_j^T \rangle_F = (\mathbf{u}_i \cdot \mathbf{u}_j)(\mathbf{v}_i \cdot \mathbf{v}_j)$$

在 SVD 中，$\{\mathbf{u}_1, \mathbf{u}_2, \ldots, \mathbf{u}_r\}$ 是矩陣 $AA^T$ 的特徵向量，$\{\mathbf{v}_1, \mathbf{v}_2, \ldots, \mathbf{v}_r\}$ 是矩陣 $A^TA$ 的特徵向量，由於矩陣 $AA^T$ 跟 $A^TA$ 都是對稱矩陣，所以其 [特徵向量都是正交的](/2025/10/15/對稱矩陣/#3-2-正交特徵向量)，即：

- 當 $i \neq j$ 時，$\mathbf{u}_i \cdot \mathbf{u}_j = 0$
- 當 $i \neq j$ 時，$\mathbf{v}_i \cdot \mathbf{v}_j = 0$

因此，當 $i \neq j$ 時：

$$\langle \mathbf{u}_i \mathbf{v}_i^T, \mathbf{u}_j \mathbf{v}_j^T \rangle_F = (\mathbf{u}_i \cdot \mathbf{u}_j)(\mathbf{v}_i \cdot \mathbf{v}_j) = 0 \cdot 0 = 0$$

**結論**：在 Frobenius 內積意義下，當 $i \neq j$ 時，$\mathbf{u}_i \mathbf{v}_i^T$ 和 $\mathbf{u}_j \mathbf{v}_j^T$ 互相垂直

這個性質在低秩近似中非常重要，因為它保證了 SVD 分解中的各項是**正交的**，使得我們可以獨立地選擇保留哪些項，而不會相互干擾

## 相關連結

[向量的內積與外積](/2025/10/18/向量的內積與外積/)
[矩陣對角化](/2025/10/07/矩陣對角化/)
[對稱矩陣](/2025/10/15/對稱矩陣/)
[正交矩陣](/2025/10/06/正交矩陣/)
[正定矩陣與半正定矩陣 (1) - 定義與性質](/2025/11/03/正定矩陣與半正定矩陣-1-定義與性質/)

## 參考資料

[陳晏笙老師開放課程：單元 15．奇異值分解-線性代數的大一統理論](https://youtu.be/gH6cVt8_m9I?si=bEDdr-VhSUx-w0jv&t=6633)
[【深度学习数学基础 06】奇异值分解与低秩近似](https://zhuanlan.zhihu.com/p/29739527565)
[矩陣的極分解](https://zh.wikipedia.org/wiki/%E6%9E%81%E5%88%86%E8%A7%A3#%E7%9F%A9%E9%98%B5%E7%9A%84%E6%9E%81%E5%88%86%E8%A7%A3)
