---
title: 高斯分布 (3) - 仿射變換後的性質變化
date: 2026-03-18 23:33:35
updated: 2026-03-18 23:33:35
categories: Math
mathjax: true
---

###### 此為 **高斯分布** 系列文章 - 第 3 篇：

1. <a href="/2026/03/18/高斯分布-1-從共變異數矩陣出發/" target="_blank">高斯分布 (1) - 從共變異數矩陣出發</a>
2. <a href="/2026/03/18/高斯分布-2-一維到多維的分布形式/" target="_blank">高斯分布 (2) - 一維到多維的分布形式</a>
3. <a href="/2026/03/18/高斯分布-3-仿射變換後的性質變化/" target="_blank">高斯分布 (3) - 仿射變換後的性質變化</a>

## 前言

在上一篇文章中，我們認識了多維高斯分佈的數學表示方式，知道 **均值向量 $\boldsymbol{\mu}$** 決定了分佈的中心，而 **共變異數矩陣 $\Sigma$** 決定了資料的分散形狀與方向，這篇文章我們將進一步探討：當高斯分佈經過 **矩陣變換(仿射變換)** 後，會產生什麼樣的變化？

## 線性變換與仿射變換

首先我們先簡單介紹一下什麼是線性變換與仿射變換：

#### 線性變換（Linear Transformation）

[線性變換](https://zh.wikipedia.org/zh-tw/%E5%8F%98%E6%8D%A2%E7%9F%A9%E9%98%B5) 可寫成：

$$
\mathbf{Y} = A\mathbf{X}
$$

其中 $A$ 是矩陣。線性變換的幾何特性是原點保持不變（$\mathbf{X}=\mathbf{0}$ 時，$\mathbf{Y}=\mathbf{0}$）

#### 仿射變換（Affine Transformation）

[仿射變換](https://zh.wikipedia.org/wiki/%E4%BB%BF%E5%B0%84%E5%8F%98%E6%8D%A2) 可寫成：

$$
\mathbf{Y} = A\mathbf{X} + \mathbf{b}
$$

其中 $\mathbf{b}$ 是平移向量。仿射變換可視為「先線性變換，再平移」

**結論**：所以兩者的差別只是仿射變換多一個平移的向量

<!-- more -->

#### 對高斯分布的意義

若 $\mathbf{X}$ 是高斯分布，則：

- $A$ 會改變分布形狀與方向
- $\mathbf{b}$ 只會平移中心（改變均值，不改變散布形狀）

## 高斯分布的仿射變換

#### 定義

設 $\mathbf{X}$ 是一個 $d$ 維隨機向量，服從多維高斯分布：

$$
\mathbf{X} \sim \mathcal{N}(\boldsymbol{\mu}, \Sigma)
$$

對 $\mathbf{X}$ 進行仿射變換：

$$
\mathbf{Y} = A\mathbf{X} + \mathbf{b}
$$

其中：

- $A$ 是一個 $m \times d$ 的矩陣（$m$ 可以是任意正整數）
- $\mathbf{b}$ 是一個 $m$ 維的常數向量

以下我們來探討當高斯分布經過 **仿射變換** 後，其 **平均值($\boldsymbol{\mu}$)** 及 **共變異數矩陣($\Sigma$)** 會有什麼樣的改變

#### 變換後的分布

**定理**：如果 $\mathbf{X} \sim \mathcal{N}(\boldsymbol{\mu}, \Sigma)$，則經過仿射變換 $\mathbf{Y} = A\mathbf{X} + \mathbf{b}$ 後，$\mathbf{Y}$ 仍然服從多維高斯分布：

$$
\mathbf{Y} \sim \mathcal{N}(A\boldsymbol{\mu} + \mathbf{b}, A\Sigma A^T)
$$

其中：

- 變換後的均值向量為

$$
\boldsymbol{\mu}_Y = A\boldsymbol{\mu} + \mathbf{b}
$$

- 變換後的共變異數矩陣為

$$
\Sigma_Y = A\Sigma A^T
$$

#### - 均值變換的證明

套用期望值符號：

$$
\mathbb{E}[\mathbf{Y}] = \mathbb{E}[A\mathbf{X} + \mathbf{b}]
$$

利用期望的線性性質：

$$
\mathbb{E}[A\mathbf{X} + \mathbf{b}] = A\mathbb{E}[\mathbf{X}] + \mathbb{E}[\mathbf{b}]
$$

由於 $\mathbf{b}$ 是常數向量，$\mathbb{E}[\mathbf{b}] = \mathbf{b}$，因此：

$$
\mathbb{E}[\mathbf{Y}] = A\boldsymbol{\mu} + \mathbf{b}
$$

#### - 共變異數矩陣變換的證明

[共變異數矩陣的期望值表示](/2026/03/18/高斯分布-1-從共變異數矩陣出發/#期望值表示-2)：

$$
\text{Cov}(\mathbf{Y}) = \mathbb{E}\left[(\mathbf{Y} - \mathbb{E}[\mathbf{Y}])(\mathbf{Y} - \mathbb{E}[\mathbf{Y}])^T\right]
$$

將 $\mathbf{Y} = A\mathbf{X} + \mathbf{b}$ 和 $\mathbb{E}[\mathbf{Y}] = A\boldsymbol{\mu} + \mathbf{b}$ 代入：

$$
\mathbf{Y} - \mathbb{E}[\mathbf{Y}] = (A\mathbf{X} + \mathbf{b}) - (A\boldsymbol{\mu} + \mathbf{b}) = A(\mathbf{X} - \boldsymbol{\mu})
$$

因此：

$$
\text{Cov}(\mathbf{Y}) = \mathbb{E}\left[A(\mathbf{X} - \boldsymbol{\mu}) \cdot (A(\mathbf{X} - \boldsymbol{\mu}))^T\right]
$$

展開後：

$$
\text{Cov}(\mathbf{Y}) = \mathbb{E}\left[A(\mathbf{X} - \boldsymbol{\mu})(\mathbf{X} - \boldsymbol{\mu})^T A^T\right]
$$

由於 $A$ 是常數矩陣，可提出期望值外：

$$
\text{Cov}(\mathbf{Y}) = A \mathbb{E}\left[(\mathbf{X} - \boldsymbol{\mu})(\mathbf{X} - \boldsymbol{\mu})^T\right] A^T
$$

而

$$
\mathbb{E}\left[(\mathbf{X} - \boldsymbol{\mu})(\mathbf{X} - \boldsymbol{\mu})^T\right] = \text{Cov}(\mathbf{X}) = \Sigma
$$

所以：

$$
\text{Cov}(\mathbf{Y}) = A\Sigma A^T
$$

最終得到：

$$
\mathbf{Y} \sim \mathcal{N}(A\boldsymbol{\mu} + \mathbf{b}, A\Sigma A^T)
$$

#### 共變異數矩陣的特徵值分解

對 [多維高斯分布而言，$\Sigma$ 是對稱正定矩陣](/2026/03/18/高斯分布-2-一維到多維的分布形式/#3-多維高斯分佈的共變異數矩陣是對稱且正定的)，可做 [正交對角化](/2025/10/15/對稱矩陣/#4-對角化性質)：

$$
\Sigma = Q\Lambda Q^T
$$

其中：

- $Q$ 為正交矩陣，欄向量是特徵向量
- $\Lambda=\text{diag}(\lambda_1,\ldots,\lambda_d)$，且 $\lambda_i>0$，對角線元素是 $\Sigma$ 的特徵值

#### 特徵值分解後的幾何意義

回顧多維高斯分佈的機率密度函數：

$$
f(\mathbf{x}) = \frac{1}{(2\pi)^{d/2}|\Sigma|^{1/2}} \exp\left(-\frac{1}{2}(\mathbf{x} - \boldsymbol{\mu})^T \Sigma^{-1} (\mathbf{x} - \boldsymbol{\mu})\right)
$$

在這個公式中，指數的部分包含了一個 [二次型(Quadratic Form)](/2025/11/03/正定矩陣與半正定矩陣-1-定義與性質/#二次型（Quadratic-Form）)：

$$
(\mathbf{x}-\boldsymbol{\mu})^T\Sigma^{-1}(\mathbf{x}-\boldsymbol{\mu})
$$

這個二次型的值決定了高斯分佈的機率密度大小，當二次型的值等於同一個常數時（即到中心的距離相等），在空間中會形成一個等密度面，這個等密度面通常是一個橢球面

如果我們利用 $\Sigma = Q\Lambda Q^T$，可以將 [二次型轉換到由特徵向量構成的新座標系中](/2025/11/12/正定矩陣與半正定矩陣-2-橢球體的形狀/#座標變換與對角化)，這賦予了共變異數矩陣的特徵分解非常明確的幾何意義：

- **特徵向量（$Q$）** 決定了等密度橢球的**主軸方向（旋轉）**
- **特徵值（$\Lambda$）** 決定了各主軸上的**伸縮程度（尺度）**。具體來說，等密度橢球在第 $i$ 個主軸方向的半徑長度，會與該方向的特徵值平方根 $\sqrt{\lambda_i}$ 成正比

因此，當資料經過仿射變換後，只要對新的共變異數矩陣 $\Sigma_Y=A\Sigma A^T$ 進行特徵分解，即是讀出「變換後橢球朝向與長短軸」的直接工具

## 例子：二維高斯分布的仿射變換

假設有一個二維標準高斯，其均值在原點，且共變異數矩陣為單位矩陣：

{% raw %}

$$
\boldsymbol{\mu}
=
\begin{bmatrix}
0 \\
0
\end{bmatrix},
\quad
\Sigma
=
\begin{bmatrix}
1 & 0 \\
0 & 1
\end{bmatrix}
$$

{% endraw %}

這個標準高斯的數學表示為：

$$
\mathbf{X} \sim \mathcal{N}\left(
\boldsymbol{\mu},
I
\right)
$$

<img src="./gaussian_2d_normalize.png" style="max-width: 600px;">

[圖片程式碼](https://github.com/bcjohnblue/python-plot/blob/main/2026/03/gaussian/two-dimension-transform.py)

將這個高斯分佈進行仿射變換：

$$
\mathbf{Y} = A\mathbf{X} + \mathbf{b}
$$

每一個點是將縱座標壓縮 0.5 倍，逆時針旋轉 30 度後再往右上平移各一單位後可以得到下圖：

<img src="./gaussian_2d_transformation.png" style="max-width: 600px;">

[圖片程式碼](https://github.com/bcjohnblue/python-plot/blob/main/2026/03/gaussian/two-dimension-transform.py)

我們知道高斯分佈的平移是藉由 $\mathbf{b}$ 決定的，所以往右上平移各一單位的數學表示為：

{% raw %}

$$
\mathbf{b}
=
\begin{bmatrix}
1 \\
1
\end{bmatrix}
$$

{% endraw %}

而負責改變分佈形狀與方向的 $A$，根據 [極分解的幾何意義](/2025/12/07/奇異值分解-svd-2-實際應用/#2-3-幾何意義)，先伸縮再旋轉可以表示為 **旋轉矩陣(R)** 與 **縮放矩陣(S)** 的乘積：

$$
A = RS
$$

其中：

{% raw %}

$$
R =
\begin{bmatrix}
\cos(-30^\circ) & -\sin(-30^\circ) \\
\sin(-30^\circ) & \cos(-30^\circ)
\end{bmatrix}
=
\begin{bmatrix}
\frac{\sqrt{3}}{2} & \frac{1}{2} \\
-\frac{1}{2} & \frac{\sqrt{3}}{2}
\end{bmatrix},
\quad
S =
\begin{bmatrix}
1 & 0 \\
0 & 0.5
\end{bmatrix}
$$

{% endraw %}

把矩陣乘開可得：

{% raw %}

$$
A
=
\begin{bmatrix}
\frac{\sqrt{3}}{2} & \frac{1}{2} \\
-\frac{1}{2} & \frac{\sqrt{3}}{2}
\end{bmatrix}
\begin{bmatrix}
1 & 0 \\
0 & 0.5
\end{bmatrix}
=
\begin{bmatrix}
\frac{\sqrt{3}}{2} & \frac{1}{4} \\
-\frac{1}{2} & \frac{\sqrt{3}}{4}
\end{bmatrix}
$$

{% endraw %}

則均值為：

$$
\boldsymbol{\mu}_Y = A\boldsymbol{\mu}_X + \mathbf{b} = A\mathbf{0} + \mathbf{b} = \mathbf{b}
$$

共變異數矩陣為：

$$
\Sigma_Y = A I A^T = AA^T = (RS)(RS)^T = RSS^TR^T
$$

進一步直接計算：

{% raw %}

$$
\Sigma_Y
=
\begin{bmatrix}
\frac{\sqrt{3}}{2} & \frac{1}{4} \\
-\frac{1}{2} & \frac{\sqrt{3}}{4}
\end{bmatrix}
\begin{bmatrix}
\frac{\sqrt{3}}{2} & -\frac{1}{2} \\
\frac{1}{4} & \frac{\sqrt{3}}{4}
\end{bmatrix}
=
\begin{bmatrix}
\frac{13}{16} & -\frac{3\sqrt{3}}{16} \\
-\frac{3\sqrt{3}}{16} & \frac{7}{16}
\end{bmatrix}
$$

{% endraw %}

其中：

{% raw %}

$$
SS^T =
\begin{bmatrix}
1 & 0 \\
0 & 0.25
\end{bmatrix}
$$

{% endraw %}

把它寫成特徵值分解的形式：

{% raw %}

$$
\Sigma_Y = R\Lambda R^T,\quad \Lambda = \begin{bmatrix}1 & 0 \\ 0 & 0.25\end{bmatrix}
$$

{% endraw %}

因此 $\Sigma_Y$ 的特徵值為 $\lambda_1=1,\lambda_2=0.25$，對應的主軸尺度（標準差）為 $\sqrt{\lambda_1}=1,\sqrt{\lambda_2}=0.5$

從幾何的角度來看，這個仿射變換可以拆解為三個直觀的步驟：

1. **縮放（矩陣 $S$）**：$x$ 軸方向維持不變，$y$ 軸方向壓縮為原本的 $0.5$ 倍
2. **旋轉（矩陣 $R$）**：將壓縮後的形狀整體逆時針旋轉 $30^\circ$（即旋轉 $-30^\circ$）
3. **平移（向量 $\mathbf{b}$）**：將整個分佈的中心往右上方平移至座標 $(1, 1)$

經過這些變換後，原本標準高斯分佈呈現的 **「圓形」等密度線**，就會被拉伸並旋轉成一個 **「傾斜的橢圓」**。而這個新橢圓的幾何特徵，正好與共變異數矩陣 $\Sigma_Y$ 的特徵分解結果完美對應：

- **主軸方向**：由旋轉矩陣 $R$（對應特徵向量矩陣）決定
- **主軸尺度**：沿著兩個主軸的半徑長度分別為 $1$ 與 $0.5$（對應特徵值的平方根）

## 總結

1. **高斯分佈對仿射變換具有封閉性**：若隨機向量 $\mathbf{X}$ 服從高斯分佈，經過仿射變換 $\mathbf{Y} = A\mathbf{X} + \mathbf{b}$ 後，$\mathbf{Y}$ 依然是高斯分佈，且新分佈為 $\mathbf{Y} \sim \mathcal{N}(A\boldsymbol{\mu} + \mathbf{b}, A\Sigma A^T)$。
2. **平移與線性的幾何意義**：平移向量 $\mathbf{b}$ 僅改變分佈的中心點（均值），不影響資料的分散程度；而線性變換矩陣 $A$ 則主導了分佈形狀的拉伸與方向的旋轉。
3. **從矩陣變換理解形狀**：以二維標準高斯分佈為例，原本對稱的「圓形」等密度線，經過縮放矩陣與旋轉矩陣的作用後，會變形成為具有特定朝向與長短軸的「傾斜橢圓」。
4. **特徵值分解的直觀解釋**：對共變異數矩陣進行特徵分解（$\Sigma = Q\Lambda Q^T$）是解析高斯分佈幾何特徵的最強工具——**特徵向量**精準指出了等密度橢球的主軸方向，而**特徵值的平方根**則決定了資料在該方向上的分佈尺度。

## 相關連結

[奇異值分解 SVD (2) - 實際應用](/2025/12/07/奇異值分解-svd-2-實際應用/#2-3-幾何意義)
[正交矩陣 - 9. 幾何意義](/2025/10/06/正交矩陣/#9-幾何意義)

## 參考資料

[如何直观地理解「协方差矩阵」？](https://www.zhihu.com/tardis/zm/art/37609917?source_id=1003)
