---
title: 代數重數與幾何重數
date: 2025-10-13 23:52:27
updated: 2025-10-13 23:52:27
categories: 線性代數
mathjax: true
---

## 背景：特徵值與特徵向量

對一個 $n \times n$ 矩陣 $A$，如果存在非零向量 $\mathbf{v}$ 和實數（或複數） $\lambda$，使得：

$$
A\mathbf{v} = \lambda \mathbf{v}
$$

其中：

- $\lambda$：**特徵值 (eigenvalue)**
- $\mathbf{v}$：**特徵向量 (eigenvector)**

## 代數重數（Algebraic Multiplicity）

### 定義

某個特徵值 $\lambda$ 的 **代數重數** 是：

> $\lambda$ 在特徵多項式中的根出現的次數

### 特徵多項式

$$
p(\lambda) = \det(A - \lambda I)
$$

如果：

$$
p(\lambda) = (\lambda_1 - \lambda)^{m_1} (\lambda_2 - \lambda)^{m_2} \cdots (\lambda_k - \lambda)^{m_k}
$$

那麼每個 $m_i$ 就是對應特徵值 $\lambda_i$ 的 **代數重數**

<!-- more -->

### 範例

{% raw %}

$$
A =
\begin{bmatrix}
2 & 0 \\
0 & 2
\end{bmatrix}
$$

{% endraw %}

特徵多項式：

$$
\det(A - \lambda I) = (2 - \lambda)^2
$$

因此：特徵值 $\lambda = 2$ 的 **代數重數**($\lambda$) 為 2

## 幾何重數（Geometric Multiplicity）

### 定義

某個特徵值 $\lambda$ 的 **幾何重數** 是：

> 對應的 [特徵空間(eigenspace)](https://youtu.be/1RyHRIP8QGg?si=8CeMpfmz0F51Bv94&t=1496) 的維度，也就是  
> $$ \text{nullity}(A - \lambda I) $$
> 實際上代表的就是該特徵值有幾個獨立的特徵向量

## 範例

### - 範例 1

設

{% raw %}

$$
A = \begin{bmatrix} 4 & 2 \\ 1 & 3 \end{bmatrix}
$$

{% endraw %}

**計算代數重數：**

計算特徵多項式：

{% raw %}

$$
\det(A - \lambda I) = \det\begin{bmatrix} 4-\lambda & 2 \\ 1 & 3-\lambda \end{bmatrix} = (4-\lambda)(3-\lambda) - 2 = \lambda^2 - 7\lambda + 10
$$

{% endraw %}

因式分解：

$$
\lambda^2 - 7\lambda + 10 = (\lambda - 2)(\lambda - 5)
$$

因此特徵值為 $\lambda_1 = 2$ 和 $\lambda_2 = 5$，**代數重數**$(λ_1)=1$ **代數重數**$(λ_2)=1$

**計算幾何重數：**

對於 $\lambda_1 = 2$：

{% raw %}

$$
A - 2I = \begin{bmatrix} 2 & 2 \\ 1 & 1 \end{bmatrix}
$$

{% endraw %}

得到方程式：

$$
x_1 + x_2 = 0
$$

求得 **零空間(Null space)**：

{% raw %}

$$
\mathrm{Null}(A) = \text{span}\left\{
\begin{bmatrix}1\\-1\end{bmatrix}
\right\}
$$

{% endraw %}

=> **幾何重數**$(λ_1)=1$

<br />

對於 $\lambda_2 = 5$：

{% raw %}

$$
A - 5I = \begin{bmatrix} -1 & 2 \\ 1 & -2 \end{bmatrix}
$$

{% endraw %}

求得 **零空間(Null space)**：

{% raw %}

$$
\mathrm{Null}(A) = \text{span}\left\{
\begin{bmatrix}2\\1\end{bmatrix}
\right\}
$$

{% endraw %}

=> **幾何重數**$(λ_2)=1$

### - 範例 2

再看另一個矩陣：

{% raw %}

$$
B = \begin{bmatrix} 3 & 1 \\ 0 & 3 \end{bmatrix}
$$

{% endraw %}

**計算代數重數：**

計算特徵多項式：

{% raw %}

$$
\det(B - \lambda I) = \det\begin{bmatrix} 3-\lambda & 1 \\ 0 & 3-\lambda \end{bmatrix} = (3-\lambda)^2
$$

{% endraw %}

因此特徵值 $\lambda = 3$ 其 **代數重數**$(λ)=2$

**計算幾何重數：**

{% raw %}

$$
B - 3I = \begin{bmatrix} 0 & 1 \\ 0 & 0 \end{bmatrix}
$$

{% endraw %}

得到以下方程式：

- $0 \cdot x_1 + 1 \cdot x_2 = 0$ → $x_2 = 0$
- $0 \cdot x_1 + 0 \cdot x_2 = 0$ → 恆等式

所以 $x_1$ 是自由變數，$x_2 = 0$

求得 **零空間(Null space)**：

{% raw %}

$$
\mathrm{Null}(B) = \text{span}\left\{
\begin{bmatrix}1\\0\end{bmatrix}
\right\}
$$

{% endraw %}

=> **幾何重數**$(λ)=1$ (意味著只有一個線性獨立的特徵向量)

矩陣 $B$ 的 **代數重數 = 2，幾何重數 = 1**，由此我們可以發現兩個重要的特性

## 特性

**1. 同一個特徵值的代數重數必定大於等於幾何重數**

$$
\text{幾何重數} \leq \text{代數重數}
$$

**解釋：**

- **代數重數**：特徵值在特徵多項式中出現的次數（理論上應該有幾個特徵向量）
- **幾何重數**：實際上有幾個線性獨立的特徵向量
- 由於特徵向量必須線性獨立，所以實際能得到的特徵向量數量不會超過理論上的數量
- 當特徵值有重根時，可能無法找到足夠的線性獨立特徵向量

**2. 代數重數等於幾何重數時，代表矩陣可以對角化**

**解釋：**

- 矩陣可對角化的[條件](/2025/10/07/矩陣對角化/#對角化的條件) - 矩陣有 $n$ 個線性無關的特徵向量
- 當所有特徵值的代數重數都等於幾何重數時，我們可以找到 $n$ 個線性獨立的特徵向量 => 符合對角化條件
- 相反地，當有特徵值的幾何重數小於代數重數時，代表實際找到的特徵向量數量沒有理論上的多 => 不符合對角化條件

## 相關連結

[零空間(Null space)](/2025/10/13/零空間-null-space/)
[特徵向量與特徵值](/2025/09/20/特徵向量與特徵值/)
[矩陣對角化](/2025/10/07/矩陣對角化/)
[線性生成空間-span](/2025/10/13/線性生成空間-span/)

## 參考資料

[通俗易懂：对称矩阵](https://zhuanlan.zhihu.com/p/685078086)
[特征根的代数重数与几何重数](https://zhuanlan.zhihu.com/p/626538997)
