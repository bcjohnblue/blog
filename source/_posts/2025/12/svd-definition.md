---
title: 奇異值分解 SVD (1) - 定義與性質
date: 2025-12-07 23:30:00
updated: 2025-12-07 23:30:00
categories: 線性代數
mathjax: true
---

###### 此為 **奇異值分解 SVD** 系列文章 - 第 1 篇：

1. <a href="/2025/12/07/奇異值分解-svd-1-定義與性質/" target="_blank">奇異值分解 SVD (1) - 定義與性質</a>
2. <a href="/2025/12/07/奇異值分解-svd-2-實際應用/" target="_blank">奇異值分解 SVD (2) - 實際應用</a>

## 前言

在前面的文章中，我們學習了多種矩陣分解方法，如 [特徵值分解](/2025/09/20/特徵向量與特徵值/)、[矩陣對角化](/2025/10/07/矩陣對角化/)、[對稱矩陣的譜分解](/2025/10/15/對稱矩陣/#譜分解-Spectral-Decomposition)、[正定矩陣的 Cholesky 分解](/2025/11/16/正定矩陣與半正定矩陣-3-cholesky-分解/) 等。然而，這些分解方法都有一個共同的限制：**只能作用在方陣上**。對於非方陣（如 $m \times n$ 矩陣，其中 $m \neq n$），這些方法都無法直接應用

相較之下，**奇異值分解 (Singular Value Decomposition, SVD)** 適用於 **任意矩陣**(包括非方陣)，這使得它成為線性代數中最普遍、最實用的分解方法。無論是瘦高矩陣（$m > n$）、矮胖矩陣（$m < n$），還是方陣（$m = n$），SVD 都能提供一個優雅的分解形式

下面我們來學習 SVD 的定義、性質、計算方法以及其代表的幾何意義

## 定義

對於任意 $m \times n$ 矩陣 $A$，存在分解：

$$A = Q_1\Sigma Q_2^T$$

其中：

- **$Q_1$**：$m \times m$ 正交矩陣
- **$\Sigma$**：$m \times n$ 對角矩陣（對角線元素稱為**奇異值**）
- **$Q_2$**：$n \times n$ 正交矩陣

<!-- more -->

## 範例

#### 1. $3 \times 2$ 的矩陣：

{% raw %}

$$
A =
\begin{bmatrix}
2 & 0 \\
0 & -3 \\
0 & 0
\end{bmatrix}
$$

{% endraw %}

經過 SVD 分解後可以寫成：

{% raw %}

$$
A = Q_1\Sigma Q_2^T =
\begin{bmatrix}
0 & 1 & 0 \\
-1 & 0 & 0 \\
0 & 0 & 1
\end{bmatrix}
\begin{bmatrix}
3 & 0 \\
0 & 2 \\
0 & 0
\end{bmatrix}
\begin{bmatrix}
0 & 1 \\
1 & 0
\end{bmatrix}
$$

{% endraw %}

其中：

{% raw %}

$Q_1 = \begin{bmatrix} 0 & 1 & 0 \\ -1 & 0 & 0 \\ 0 & 0 & 1 \end{bmatrix}$、
$\Sigma = \begin{bmatrix} 3 & 0 \\ 0 & 2 \\ 0 & 0 \end{bmatrix}$、
$Q_2 = \begin{bmatrix} 0 & 1 \\ 1 & 0 \end{bmatrix}$

{% endraw %}

<br />
<br />

$Q_1$ 及 $Q_2$ 為正交矩陣，$\Sigma$ 為對角矩陣，奇異值為 3 跟 2

#### 2. $3 \times 3$ 的方陣：

{% raw %}

$$
A =
\begin{bmatrix}
0 & 0 & 0 \\
0 & 4 & 0 \\
0 & 0 & 1
\end{bmatrix}
$$

{% endraw %}

經過 SVD 分解後可以寫成：

{% raw %}

$$
A = Q_1\Sigma Q_2^T =
\begin{bmatrix}
0 & 0 & 1 \\
1 & 0 & 0 \\
0 & 1 & 0
\end{bmatrix}
\begin{bmatrix}
4 & 0 & 0 \\
0 & 1 & 0 \\
0 & 0 & 0
\end{bmatrix}
\begin{bmatrix}
0 & 1 & 0 \\
0 & 0 & 1 \\
1 & 0 & 0
\end{bmatrix}
$$

{% endraw %}

其中：

{% raw %}

$Q_1 = \begin{bmatrix} 0 & 0 & 1 \\ 1 & 0 & 0 \\ 0 & 1 & 0 \end{bmatrix}$、
$\Sigma = \begin{bmatrix} 4 & 0 & 0 \\ 0 & 1 & 0 \\ 0 & 0 & 0 \end{bmatrix}$、
$Q_2 = \begin{bmatrix} 0 & 0 & 1 \\ 1 & 0 & 0 \\ 0 & 1 & 0 \end{bmatrix}$

{% endraw %}

<br />
<br />

$Q_1$ 及 $Q_2$ 為正交矩陣，$\Sigma$ 為對角矩陣，奇異值為 4 跟 1

## 奇異值的性質

**奇異值** $\sigma_i \geq 0$ 位於 $\Sigma$ 的對角線上，通常按降序排列：

$$\sigma_1 \geq \sigma_2 \geq \cdots \geq \sigma_r > 0$$

其中 $r = \text{rank}(A)$ 是矩陣的 [秩](/2025/09/24/%E7%9F%A9%E9%99%A3%E4%B8%AD%E7%9A%84%E8%BB%B8%E5%85%83%E8%88%87%E7%A7%A9/)

對於 $m \times n$ 矩陣 $A$（假設是 $m > n$ 的瘦高矩陣），$\Sigma$ 的形式為：

{% raw %}

$$
\Sigma = \begin{bmatrix}
\sigma_1 & 0 & 0 & \cdots & 0 \\
0 & \sigma_2 & 0 & \cdots & 0 \\
0 & 0 & \sigma_3 & \cdots & 0 \\
\vdots & \vdots & \vdots & \ddots & \vdots \\
0 & 0 & 0 & \cdots & \sigma_n \\
0 & 0 & 0 & \cdots & 0 \\
\vdots & \vdots & \vdots & \ddots & \vdots \\
0 & 0 & 0 & \cdots & 0
\end{bmatrix}
$$

{% endraw %}

當 $m = n$（$A$ 為 $n \times n$ 方陣）時，$\Sigma$ 仍然是 $n \times n$ 的對角矩陣，但只有前 $r = \text{rank}(A)$ 個奇異值非零，其餘位置為 0：

{% raw %}

$$
\Sigma = \begin{bmatrix}
\sigma_1 & 0 & \cdots & 0 & 0 & \cdots & 0 \\
0 & \sigma_2 & \cdots & 0 & 0 & \cdots & 0 \\
\vdots & \vdots & \ddots & \vdots & \vdots & \ddots & \vdots \\
0 & 0 & \cdots & \sigma_r & 0 & \cdots & 0 \\
0 & 0 & \cdots & 0 & 0 & \cdots & 0 \\
\vdots & \vdots & \ddots & \vdots & \vdots & \ddots & \vdots \\
0 & 0 & \cdots & 0 & 0 & \cdots & 0
\end{bmatrix}
$$

{% endraw %}

## 從 $A^TA$ 和 $AA^T$ 出發

為什麽任意形狀的矩陣 $A$ 都可以經過 SVD 分解成兩個正交矩陣與一個對角矩陣的乘積呢？要了解這件事，我們需要先從這兩個矩陣出發：

1. **$A^TA$**（$n \times n$ 矩陣）
2. **$AA^T$**（$m \times m$ 矩陣）

下面讓我們來了解這兩個矩陣的七大關鍵性質，在深刻了解這些性質後我們就能輕鬆地了解為什麼 SVD 分解的形式長這樣

### 1. 矩陣的大小

對於 $m \times n$ 矩陣 $A$，我們可以構造兩個重要的矩陣：

- **$A^TA$**：$n \times n$ 方陣（將 $A^T$ 與 $A$ 相乘）
- **$AA^T$**：$m \times m$ 方陣（將 $A$ 與 $A^T$ 相乘）

這兩個矩陣都是**方陣**，因此我們可以對它們應用前面學過的方陣分解方法（如特徵值分解、對角化等），這正是推導 SVD 的關鍵步驟

### 2. 對稱矩陣

我們證明 $A^TA$ 和 $AA^T$ 都是 [對稱矩陣](/2025/10/15/對稱矩陣/)

###### **證明 $A^TA$ 是對稱矩陣：**

根據 [轉置矩陣的性質](/2025/09/27/轉置矩陣/#4-矩陣乘法的轉置)：

$$(A^TA)^T = A^T(A^T)^T = A^TA$$

$A^TA$ 的轉置為自己，這表明 $A^TA$ 是對稱矩陣

###### **證明 $AA^T$ 是對稱矩陣：**

同樣地：

$$(AA^T)^T = (A^T)^TA^T = AA^T$$

因此 $AA^T$ 也是對稱矩陣

### 3. 零空間

我們證明 $A^TA$ 和 $A$ 有相同的 [零空間](/2025/10/13/零空間-null-space)，而 $AA^T$ 與 $A^T$ 有相同的 [左零空間](/2025/10/13/零空間-null-space/#左零空間)

###### **證明：$A^TA$ 和 $A$ 有相同的零空間：**

$$\text{Null}(A^TA) = \text{Null}(A)$$

我們需要證明兩個方向都成立：

**方向 1：$\text{Null}(A) \subseteq \text{Null}(A^TA)$**

如果 $\mathbf{x} \in \text{Null}(A)$，即 $A\mathbf{x} = \mathbf{0}$，將等式左乘以 $A^T$，則：

$$A^TA\mathbf{x} = A^T(A\mathbf{x}) = A^T\mathbf{0} = \mathbf{0}$$

因此 $\mathbf{x} \in \text{Null}(A^TA)$

**方向 2：$\text{Null}(A^TA) \subseteq \text{Null}(A)$**

如果 $\mathbf{x} \in \text{Null}(A^TA)$，即 $A^TA\mathbf{x} = \mathbf{0}$，將等式左乘以 $\mathbf{x}^T$，則：

$$\mathbf{x}^TA^TA\mathbf{x} = \mathbf{x}^T\mathbf{0} = 0$$

根據 [向量內積與範數的關係](/2025/10/18/向量的內積與外積/#1-與範數的關係)：

{% raw %}

$$\mathbf{x}^TA^TA\mathbf{x} = (A\mathbf{x})^T(A\mathbf{x}) = \|A\mathbf{x}\|^2$$

{% endraw %}

因此：

{% raw %}

$$\|A\mathbf{x}\|^2 = 0$$

{% endraw %}

這意味著 $A\mathbf{x} = \mathbf{0}$，即 $\mathbf{x} \in \text{Null}(A)$

**結論：** 由於兩個方向都成立，我們得到 $\text{Null}(A) = \text{Null}(A^TA)$，即兩者有相同的零空間

###### **證明：$AA^T$ 和 $A^T$ 有相同的左零空間：**

$$\text{Null}(AA^T) = \text{Null}(A^T)$$

**方向一：** $\text{Null}(A^T) \subseteq \text{Null}(AA^T)$

假設 $\mathbf{y} \in \text{Null}(A^T)$，即 $A^T\mathbf{y} = \mathbf{0}$，將等式左乘 $A$，則：

{% raw %}

$$AA^T\mathbf{y} = (AA^T)\mathbf{y} = (AA^T)^T\mathbf{y} = A\mathbf{0} = \mathbf{0}$$

{% endraw %}

因此 $\mathbf{y} \in \text{Null}(AA^T)$

**方向二：** $\text{Null}(AA^T) \subseteq \text{Null}(A^T)$

假設 $\mathbf{y} \in \text{Null}(AA^T)$，即 $AA^T\mathbf{y} = \mathbf{0}$，將等式左乘 $\mathbf{y}^T$：

{% raw %}

$$\mathbf{y}^TAA^T\mathbf{y} = \mathbf{0}$$

{% endraw %}

注意到：

{% raw %}

$$\mathbf{y}^TAA^T\mathbf{y} = (A^T\mathbf{y})^T(A^T\mathbf{y}) = \|A^T\mathbf{y}\|^2$$

{% endraw %}

因此：

{% raw %}

$$\|A^T\mathbf{y}\|^2 = 0$$

{% endraw %}

這意味著 $A^T\mathbf{y} = \mathbf{0}$，即 $\mathbf{y} \in \text{Null}(A^T)$

**結論：** 由於兩個方向都成立，我們得到 $\text{Null}(AA^T) = \text{Null}(A^T)$，即兩者有相同的左零空間

### 4. 正定矩陣或半正定矩陣

我們證明 $A^TA$ 和 $AA^T$ 都是正定矩陣或半正定矩陣

###### **$A^TA$ 的正定性**

我們考慮 $A^TA$ 的 [二次型](/2025/11/03/正定矩陣與半正定矩陣-1-定義與性質/#二次型（Quadratic-Form）)：

{% raw %}

$$\mathbf{x}^T(A^TA)\mathbf{x} = \mathbf{x}^TA^TA\mathbf{x} = (A\mathbf{x})^T(A\mathbf{x}) = \|A\mathbf{x}\|^2$$

{% endraw %}

接著根據 [秩-零化度定理](/2025/10/13/零空間-null-space/#性質)，**零空間(Null space)** 的維度為：

{% raw %}

$$\dim(\text{Null}(A)) = n - \text{rank}(A)$$

{% endraw %}

當 $\text{rank}(A) = n$，也就是 [行滿秩](/2025/09/24/矩陣中的軸元與秩/#範例-3：行滿秩矩陣) 時：

$$\dim(\text{Null}(A)) = n - n = 0$$

這意味著 $\text{Null}(A) = \{\mathbf{0}\}$，即 $A\mathbf{x} = \mathbf{0}$ 只有當 $\mathbf{x}$ 是零向量時成立

=> 而這給出了 $A^TA$ 的二次型恆正的結論，也就是說 $A^TA$ 是**正定矩陣**：

{% raw %}

$$\mathbf{x}^T(A^TA)\mathbf{x} = \|A\mathbf{x}\|^2 \gt 0$$

{% endraw %}

另一方面當 $\text{rank}(A) < n$ 也就是 $r < n$ 時：

$$\dim(\text{Null}(A)) = n - r > 0$$

此時 $\text{Null}(A) \neq \{\mathbf{0}\}$，也就是說存在某些向量 $\mathbf{x}$ 可以使得 $A\mathbf{x} = \mathbf{0}$

=> 而這給出了 $A^TA$ 的二次型非負的結論，也就是說 $A^TA$ 是**半正定矩陣**：

{% raw %}

$$\mathbf{x}^T(A^TA)\mathbf{x} = \|A\mathbf{x}\|^2 \ge 0$$

{% endraw %}

###### **$AA^T$ 的正定性**

我們考慮 $AA^T$ 的二次型：

{% raw %}

$$\mathbf{x}^T(AA^T)\mathbf{x} = \mathbf{x}^TAA^T\mathbf{x} = (A^T\mathbf{x})^T(A^T\mathbf{x}) = \|A^T\mathbf{x}\|^2$$

{% endraw %}

同樣我們可以計算 [左零空間的維度](/2025/10/13/零空間-null-space/#性質-1) 為：

{% raw %}

$$\dim(\text{Null}(A^T)) = m - \text{rank}(A)$$

{% endraw %}

當 $\text{rank}(A) = m$，也就是 [列滿秩](/2025/09/24/矩陣中的軸元與秩/#範例-2：列滿秩矩陣) 時：

$$\dim(\text{Null}(A^T)) = m - m = 0$$

這意味著 $\text{Null}(A^T) = \{\mathbf{0}\}$，即 $A^T\mathbf{x} = \mathbf{0}$ 只有當 $\mathbf{x}$ 是零向量時成立

=> 而這給出了 $AA^T$ 的二次型恆正的結論，也就是說 $AA^T$ 是**正定矩陣**：

{% raw %}

$$\mathbf{x}^T(AA^T)\mathbf{x} = \|A^T\mathbf{x}\|^2 \gt 0$$

{% endraw %}

另一方面當 $\text{rank}(A) < m$ 也就是 $r < m$ 時：

$$\dim(\text{Null}(A^T)) = m - r > 0$$

此時 $\text{Null}(A^T) \neq \{\mathbf{0}\}$，也就是說存在某些向量 $\mathbf{x}$ 可以使得 $A^T\mathbf{x} = \mathbf{0}$

=> 而這給出了 $AA^T$ 的二次型非負的結論，也就是說 $AA^T$ 是**半正定矩陣**：

{% raw %}

$$\mathbf{x}^T(AA^T)\mathbf{x} = \|A^T\mathbf{x}\|^2 \ge 0$$

{% endraw %}

###### **小結**

| 矩陣   | 條件                                 | 正定性 | 原因                                                                                                    |
| ------ | ------------------------------------ | ------ | ------------------------------------------------------------------------------------------------------- |
| $A^TA$ | $A$ 行滿秩（$\text{rank}(A) = n$）   | 正定   | $\text{Null}(A) = \{\mathbf{0}\}$ $\Rightarrow$ $\|\|A\mathbf{x}\|\|^2 > 0$ 對所有非零 $\mathbf{x}$     |
| $A^TA$ | $A$ 非行滿秩（$\text{rank}(A) < n$） | 半正定 | 存在非零 $\mathbf{x}$ 使得 $A\mathbf{x} = \mathbf{0}$ $\Rightarrow$ $\|\|A\mathbf{x}\|\|^2 = 0$         |
| $AA^T$ | $A$ 列滿秩（$\text{rank}(A) = m$）   | 正定   | $\text{Null}(A^T) = \{\mathbf{0}\}$ $\Rightarrow$ $\|\|A^T\mathbf{x}\|\|^2 > 0$ 對所有非零 $\mathbf{x}$ |
| $AA^T$ | $A$ 非列滿秩（$\text{rank}(A) < m$） | 半正定 | 存在非零 $\mathbf{x}$ 使得 $A^T\mathbf{x} = \mathbf{0}$ $\Rightarrow$ $\|\|A^T\mathbf{x}\|\|^2 = 0$     |

### 5. 相同的特徵值

我們證明 $A^TA$ 和 $AA^T$ 擁有相同的特徵值

假設 $\lambda \neq 0$ 是 $A^TA$ 的特徵值，對應的特徵向量為 $\mathbf{x}$，即：

$$A^TA\mathbf{x} = \lambda\mathbf{x}, \quad \mathbf{x} \neq \mathbf{0}$$

我們要證明 $\lambda$ 也是 $AA^T$ 的特徵值

**步驟 1**：在等式兩邊左乘 $A$

$$A(A^TA\mathbf{x}) = A(\lambda\mathbf{x})$$

**步驟 2**：套用乘法結合律，將 $A\mathbf{x}$ 單獨放在括號中

$$AA^T(A\mathbf{x}) = \lambda(A\mathbf{x})$$

**步驟 3**：結論

由步驟 2 可以觀察出，當我們把 $A\mathbf{x}$ 看作 $AA^T$ 的特徵向量，即可得證 $AA^T$ 與 $A^TA$ 擁有同樣的特徵值 $\lambda$：

$$AA^T(A\mathbf{x}) = \lambda(A\mathbf{x})$$

#### 零特徵值的情況

需要注意的是，由於 $A^TA$ 和 $AA^T$ 矩陣的大小不一樣，即使他們擁有同樣的特徵值 $\lambda$，但其餘的特徵值數量是不一致的，所以兩者可能有不同數量的零特徵值：

- 如果 $m > n$（瘦高矩陣），$AA^T$ 至少有 $m - n$ 個零特徵值
- 如果 $m < n$（矮胖矩陣），$A^TA$ 至少有 $n - m$ 個零特徵值
- 如果 $\text{rank}(A) = r < \min(m,n)$，兩者都有額外的零特徵值

**範例**

考慮 $3 \times 2$ 的瘦高矩陣 $A$（秩為 2）：

- $A^TA$ 是 $2 \times 2$，有 2 個非零特徵值，0 個零特徵值
- $AA^T$ 是 $3 \times 3$，有 2 個非零特徵值（與 $A^TA$ 相同），1 個零特徵值

### 6. 特徵向量的對應關係

從上述求證相同特徵值的章節中可以看出，如果：

- $\mathbf{v_i}$ 是 $A^TA$ 的某個特徵向量，對應特徵值 $\lambda_i$
- $\mathbf{u_i}$ 是 $AA^T$ 的某個特徵向量，對應相同特徵值 $\lambda_i$

則有以下特徵向量的對應關係：

$$\mathbf{u_i} = A\mathbf{v_i}$$

#### 單位化

需要注意的是，$A\mathbf{v_i}$ 通常不是單位向量。為了得到單位特徵向量，我們需要進行正規化

{% raw %}

假設 $\mathbf{v_i}$ 已經是單位向量（ $\|\mathbf{v_i}\| = 1$），我們計算 $\|A\mathbf{v_i}\|$：

$$
\begin{align*}
\|A\mathbf{v_i}\|^2 &= (A\mathbf{v_i})^T(A\mathbf{v_i}) \\
&= \mathbf{v_i}^TA^TA\mathbf{v_i} \\
&= \mathbf{v_i}^T(\lambda_i\mathbf{v_i}) \quad (\text{因為 } A^TA\mathbf{v_i} = \lambda_i\mathbf{v_i}) \\
&= \lambda_i\mathbf{v_i}^T\mathbf{v_i} \\
&= \lambda_i
\end{align*}
$$

{% endraw %}

因此：

{% raw %}

$$\|A\mathbf{v_i}\| = \sqrt{\lambda_i}$$

{% endraw %}

單位化後的特徵向量為：

{% raw %}

$$\mathbf{u_i} = \frac{A\mathbf{v_i}}{\|A\mathbf{v_i}\|} = \frac{A\mathbf{v_i}}{\sqrt{\lambda_i}}$$

{% endraw %}

### 7. 矩陣對角化

基於前面的結果，我們可以將對稱矩陣 $A^TA$ 和 $AA^T$ 進行 [正交對角化](/2025/10/15/對稱矩陣/#4-對角化性質)

#### $A^TA$ 的對角化

由於 $A^TA$ 是對稱矩陣，它可以被對角化為：

{% raw %}

$$A^TA = Q_2\Lambda Q_2^T$$

{% endraw %}

其中：

**$Q_2$** 是由 $A^TA$ 的單位特徵向量組成的 $n \times n$ 正交矩陣

{% raw %}

$$
Q_2 = \begin{bmatrix}
| & | & & | \\
\mathbf{v}_1 & \mathbf{v}_2 & \cdots & \mathbf{v}_n \\
| & | & & |
\end{bmatrix}
$$

{% endraw %}

**$\Lambda$** 是由特徵值組成的 $n \times n$ 對角矩陣

{% raw %}

$$
\Lambda = \begin{bmatrix}
\lambda_1 & 0 & \cdots & 0 \\
0 & \lambda_2 & \cdots & 0 \\
\vdots & \vdots & \ddots & \vdots \\
0 & 0 & \cdots & \lambda_n
\end{bmatrix}
$$

{% endraw %}

其中 $\lambda_1 \geq \lambda_2 \geq \cdots \geq \lambda_n \geq 0$（按降序排列）

#### $AA^T$ 的對角化

同樣地，$AA^T$ 也是對稱矩陣，可以被對角化為：

{% raw %}

$$AA^T = Q_1\Lambda Q_1^T$$

{% endraw %}

其中：

**$Q_1$** 是由 $AA^T$ 的單位特徵向量組成的 $m \times m$ 正交矩陣

{% raw %}

$$
Q_1 = \begin{bmatrix}
| & | & & | \\
\mathbf{u}_1 & \mathbf{u}_2 & \cdots & \mathbf{u}_m \\
| & | & & |
\end{bmatrix}
$$

{% endraw %}

**$\Lambda$** 是由特徵值組成的 $m \times m$ 對角矩陣

{% raw %}

$$
\Lambda = \begin{bmatrix}
\lambda_1 & 0 & \cdots & 0 \\
0 & \lambda_2 & \cdots & 0 \\
\vdots & \vdots & \ddots & \vdots \\
0 & 0 & \cdots & \lambda_m
\end{bmatrix}
$$

{% endraw %}

其中 $\lambda_1 \geq \lambda_2 \geq \cdots \geq \lambda_m \geq 0$（按降序排列）

### 小結：$A^TA$ 和 $AA^T$ 的性質總整理

以上我們推導了 $A^TA$ 和 $AA^T$ 的七大關鍵性質：

|                      | $A^TA$                               | $AA^T$                                                  | 說明                    |
| -------------------- | ------------------------------------ | ------------------------------------------------------- | ----------------------- |
| 1. 矩陣大小           | $n \times n$                         | $m \times m$                                            | 兩者都是方陣            |
| 2. 對稱矩陣           | $(A^TA)^T = A^TA$                    | $(AA^T)^T = AA^T$                                       | 兩者都是對稱矩陣        |
| 3. 零空間             | $\text{Null}(A^TA) = \text{Null}(A)$ | $\text{Null}(AA^T) = \text{Null}(A^T)$                  | 零空間相同              |
| 4. 正定矩陣或半正定矩陣 | $\mathbf{x}^TA^TA\mathbf{x} \geq 0$  | $\mathbf{x}^TAA^T\mathbf{x} \geq 0$                     | 兩者至少都是半正定矩陣  |
| 5. 相同的特徵值       | $\lambda_1, \ldots, \lambda_r > 0$   | $\lambda_1, \ldots, \lambda_r > 0$                      | 前 $r$ 個非零特徵值相同 |
| 6. 特徵向量的對應關係 | $\mathbf{v}_i$ 是特徵向量            | $\mathbf{u}_i = \frac{A\mathbf{v}_i}{\sqrt{\lambda_i}}$ | 兩者特徵向量間的相關性  |
| 7. 矩陣對角化         | $A^TA = Q_2\Lambda Q_2^T$            | $AA^T = Q_1\Lambda Q_1^T$                               | 正交對角化性質        |

這些性質是推導 SVD 的基礎，接著我們將利用它們推導出 $A = Q_1\Sigma Q_2^T$ 的形式

## 推導奇異值分解的形式

接著我們從 $A^TA$ 和 $AA^T$ 的性質出發，推導出奇異值分解的定義形式

#### 步驟 1：從特徵向量關係出發

從 [6. 特徵向量的對應關係](./#6-特徵向量的對應關係) 我們知道：

{% raw %}

$$\mathbf{u}_i = \frac{A\mathbf{v}_i}{\sqrt{\lambda_i}}$$

{% endraw %}

兩邊同乘 $\sqrt{\lambda_i}$：

{% raw %}

$$A\mathbf{v}_i = \sqrt{\lambda_i}\mathbf{u}_i$$

{% endraw %}

這個關係對所有 $i = 1, 2, \ldots, r$（$r$ 是秩）都成立

#### 步驟 2：改寫成矩陣形式

對於以上所有的 $A\mathbf{v}_i$，我們可以寫成：

{% raw %}

$$
\begin{align*}
A\mathbf{v}_1 &= \sqrt{\lambda_1}\mathbf{u}_1 \\
A\mathbf{v}_2 &= \sqrt{\lambda_2}\mathbf{u}_2 \\
&\vdots \\
A\mathbf{v}_n &= \sqrt{\lambda_n}\mathbf{u}_n
\end{align*}
$$

{% endraw %}

將這些方程以矩陣形式表示為：

{% raw %}

$$
A
\begin{bmatrix}
| & | & & | \\
\mathbf{v}_1 & \mathbf{v}_2 & \cdots & \mathbf{v}_n \\
| & | & & |
\end{bmatrix}
=
\begin{bmatrix}
| & | & & | \\
\mathbf{u}_1 & \mathbf{u}_2 & \cdots & \mathbf{u}_m \\
| & | & & |
\end{bmatrix}

\begin{bmatrix}
\sqrt{\lambda_1} & 0 & \cdots & 0 & 0 & \cdots & 0 \\
0 & \sqrt{\lambda_2} & \cdots & 0 & 0 & \cdots & 0 \\
\vdots & \vdots & \ddots & \vdots & \vdots & \ddots & \vdots \\
0 & 0 & \cdots & \sqrt{\lambda_r} & 0 & \cdots & 0 \\
0 & 0 & \cdots & 0 & 0 & \cdots & 0 \\
\vdots & \vdots & \ddots & \vdots & \vdots & \ddots & \vdots \\
0 & 0 & \cdots & 0 & 0 & \cdots & 0
\end{bmatrix}
$$

{% endraw %}

其中最右邊的對角矩陣是 $m \times n$ 的矩陣 $\Sigma$，這個矩陣的特點是：

- **對角線元素**：為特徵值的平方根 $\sqrt{\lambda_i}$，就是一開始定義的 [奇異值 ($\sigma_i$)](./#奇異值的性質)，從左上開始由大到小排列
- **非對角線元素**：全部為零
- **矩陣形狀**：維度為 $m \times n$，由原矩陣 $A$ 的維度決定
  - 若 $m > n$：矩陣呈現瘦高形狀，下方有零列，即 [範例 1. $3 \times 2$ 的矩陣](./#1-3-times-2-的矩陣：)
  - 若 $m < n$：矩陣呈現矮胖形狀，右方有零行
  - 若 $m = n$：矩陣為方陣，即 [範例 2. $3 \times 3$ 的方陣](./#2-3-times-3-的方陣：)
- **秩**：前 $r$ 個奇異值為正數（$r = \text{rank}(A)$），其餘為零

#### 步驟 3：使用正交矩陣表示

從 [7. 矩陣對角化](./#7-矩陣對角化) 可知：

{% raw %}

$$
Q_2 = \begin{bmatrix}
| & | & & | \\
\mathbf{v}_1 & \mathbf{v}_2 & \cdots & \mathbf{v}_n \\
| & | & & |
\end{bmatrix}
$$

$$
Q_1 = \begin{bmatrix}
| & | & & | \\
\mathbf{u}_1 & \mathbf{u}_2 & \cdots & \mathbf{u}_m \\
| & | & & |
\end{bmatrix}
$$

{% endraw %}

則 步驟 2 所得的式子可以寫成：

{% raw %}

$$AQ_2 = Q_1\Sigma$$

{% endraw %}

#### 步驟 4：得到 SVD 形式

在等式兩邊右乘 $Q_2^T$：

{% raw %}

$$AQ_2Q_2^T = Q_1\Sigma Q_2^T$$

{% endraw %}

由於 $Q_2$ 是正交矩陣，有 $Q_2^TQ_2 = I$，所以：

{% raw %}

$$A = Q_1\Sigma Q_2^T$$

{% endraw %}

**由此我們得到了奇異值分解(SVD)的標準形式！**

## $\Sigma$ 矩陣的具體形式

根據矩陣 $A$ 的大小，對角矩陣 $\Sigma$ 也會有不同的表示形式，這部分容易被混淆所以讓我們細看一下

$\Sigma$ 跟 $A$ 一樣都是 $m \times n$ 的矩陣，其形式取決於 $m$ 和 $n$ 的大小關係：

##### 情況 1：$m > n$（瘦高矩陣）

{% raw %}

$$
\Sigma = \begin{bmatrix}
\sigma_1 & 0 & \cdots & 0 \\
0 & \sigma_2 & \cdots & 0 \\
\vdots & \vdots & \ddots & \vdots \\
0 & 0 & \cdots & \sigma_n \\
0 & 0 & \cdots & 0 \\
\vdots & \vdots & & \vdots \\
0 & 0 & \cdots & 0
\end{bmatrix}_{m \times n}
$$

{% endraw %}

##### 情況 2：$m < n$（矮胖矩陣）

{% raw %}

$$
\Sigma = \begin{bmatrix}
\sigma_1 & 0 & \cdots & 0 & 0 & \cdots & 0\\
0 & \sigma_2 & \cdots & 0 & 0 & \cdots & 0\\
\vdots & \vdots & \ddots & \vdots & \vdots & & \vdots \\
0 & 0 & \cdots & \sigma_m & 0 & \cdots & 0
\end{bmatrix}_{m \times n}
$$

{% endraw %}

##### 情況 3：$m = n$（方陣）

{% raw %}

$$
\Sigma = \begin{bmatrix}
\sigma_1 & 0 & \cdots & 0 \\
0 & \sigma_2 & \cdots & 0 \\
\vdots & \vdots & \ddots & \vdots \\
0 & 0 & \cdots & \sigma_n
\end{bmatrix}_{n \times n}
$$

{% endraw %}

需要注意的是只有 $\sigma_1, \sigma_2, ... \sigma_r$ => 前 $r$ 個奇異值為正數，$\sigma_{r+1}, \sigma_{r+2}, ... \sigma_{n}$ => $\sigma_r$ 之後的奇異數為零

## 計算矩陣的 SVD

以上我們了解了 SVD 分解的形式，接著我們要問的是，對於矩陣 $A$ 的奇異值分解：

{% raw %}

$$A = Q_1\Sigma Q_2^T$$

{% endraw %}

要如何在已知矩陣 $A$ 的情況下，求得 $Q_1$, $Q_2$ 以及中間的 $\Sigma$ 矩陣呢？

### 計算方法概述

根據前面的推導，我們有兩種方法可以計算 SVD：

#### 方法 1：從 $A^TA$ 出發（推薦，當 $m \geq n$ 時）

當矩陣 $A$ 是瘦高矩陣（$m \geq n$）時，通常從 $A^TA$ 開始計算，因為 $A^TA$ 的維度較小

**步驟**：

1. **計算 $A^TA$**：得到 $n \times n$ 的對稱矩陣
2. **求 $A^TA$ 的特徵值和特徵向量**：
   - 特徵值：$\lambda_1 \geq \lambda_2 \geq \cdots \geq \lambda_n \geq 0$
   - 特徵向量：$\mathbf{v}_1, \mathbf{v}_2, \ldots, \mathbf{v}_n$（單位化）
3. **計算奇異值**：$\sigma_i = \sqrt{\lambda_i}$（按降序排列）
4. **構建 $Q_2$**：$Q_2 = [\mathbf{v}_1, \mathbf{v}_2, \ldots, \mathbf{v}_n]$（$n \times n$ 正交矩陣）
5. **構建 $\Sigma$**：$m \times n$ 對角矩陣，對角元素為 $\sigma_1, \sigma_2, \ldots, \sigma_n$
6. **計算 $Q_1$**：利用關係式 $\mathbf{u}_i = \frac{A\mathbf{v}_i}{\sigma_i}$（僅對 $\sigma_i > 0$ 的 $i$），然後擴展為完整的正交基

#### 方法 2：從 $AA^T$ 出發（當 $m \leq n$ 時）

當矩陣 $A$ 是矮胖矩陣（$m \leq n$）時，適合從維度較小的 $AA^T$ 開始計算

**步驟**：

1. **計算 $AA^T$**：得到 $m \times m$ 的對稱矩陣
2. **求 $AA^T$ 的特徵值和特徵向量**：
   - 特徵值：$\lambda_1 \geq \lambda_2 \geq \cdots \geq \lambda_m \geq 0$
   - 特徵向量：$\mathbf{u}_1, \mathbf{u}_2, \ldots, \mathbf{u}_m$（單位化）
3. **計算奇異值**：$\sigma_i = \sqrt{\lambda_i}$（按降序排列）
4. **構建 $Q_1$**：$Q_1 = [\mathbf{u}_1, \mathbf{u}_2, \ldots, \mathbf{u}_m]$（$m \times m$ 正交矩陣）
5. **構建 $\Sigma$**：$m \times n$ 對角矩陣，對角元素為 $\sigma_1, \sigma_2, \ldots, \sigma_m$
6. **計算 $Q_2$**：利用關係式 $\mathbf{v}_i = \frac{A^T\mathbf{u}_i}{\sigma_i}$（僅對 $\sigma_i > 0$ 的 $i$），然後擴展為完整的正交基

### 計算流程範例（以方法 1 為例）

假設我們有一個 $3 \times 2$ 矩陣 $A$（$m \geq n$ 的瘦高矩陣）：

{% raw %}

$$
A =
\begin{bmatrix}
2 & 0 \\
0 & -3 \\
0 & 0
\end{bmatrix}
$$

{% endraw %}

實際計算流程如下：

##### 步驟 1：計算 $A^TA$

{% raw %}

$$
\begin{align*}
A^TA &= \begin{bmatrix} 2 & 0 & 0 \\ 0 & -3 & 0 \end{bmatrix} \begin{bmatrix} 2 & 0 \\ 0 & -3 \\ 0 & 0 \end{bmatrix} \\
&= \begin{bmatrix} 4 & 0 \\ 0 & 9 \end{bmatrix}
\end{align*}
$$

{% endraw %}

##### 步驟 2：對角化 $A^TA$

由於 {% raw %} $A^TA = \begin{bmatrix} 4 & 0 \\ 0 & 9 \end{bmatrix}$ {% endraw %} 已經是對角矩陣，我們可以直接讀出特徵值和特徵向量：

**特徵值**（按降序排列）：

- $\lambda_1 = 9$
- $\lambda_2 = 4$

**特徵向量**（標準正交）：

- {% raw %} $\mathbf{v}_1 = \begin{bmatrix} 0 \\ 1 \end{bmatrix}$（對應 $\lambda_1 = 9$） {% endraw %}

- {% raw %} $\mathbf{v}_2 = \begin{bmatrix} 1 \\ 0 \end{bmatrix}$（對應 $\lambda_2 = 4$） {% endraw %}

##### 步驟 3：計算奇異值

{% raw %}

$$
\begin{align*}
\sigma_1 &= \sqrt{\lambda_1} = \sqrt{9} = 3 \\
\sigma_2 &= \sqrt{\lambda_2} = \sqrt{4} = 2
\end{align*}
$$

{% endraw %}

##### 步驟 4：構建 $\Sigma$ 矩陣

{% raw %}

$$
\Sigma = \begin{bmatrix}
3 & 0 \\
0 & 2 \\
0 & 0
\end{bmatrix}_{3 \times 2}
$$

{% endraw %}

##### 步驟 5：構建 $Q_2$

{% raw %}

$$
Q_2 = \begin{bmatrix} \mathbf{v}_1 & \mathbf{v}_2 \end{bmatrix} = \begin{bmatrix} 0 & 1 \\ 1 & 0 \end{bmatrix}_{2 \times 2}
$$

{% endraw %}

##### 步驟 6：構建 $Q_1$

利用關係式 $\mathbf{u}_i = \displaystyle \frac{A\mathbf{v}_i}{\sigma_i}$ 計算前 $r$ 個特徵向量（$r = 2$）：

{% raw %}

$$
\begin{align*}
\mathbf{u}_1 &= \frac{A\mathbf{v}_1}{\sigma_1} = \frac{1}{3} \begin{bmatrix} 2 & 0 \\ 0 & -3 \\ 0 & 0 \end{bmatrix} \begin{bmatrix} 0 \\ 1 \end{bmatrix} = \frac{1}{3} \begin{bmatrix} 0 \\ -3 \\ 0 \end{bmatrix} = \begin{bmatrix} 0 \\ -1 \\ 0 \end{bmatrix} \\
\mathbf{u}_2 &= \frac{A\mathbf{v}_2}{\sigma_2} = \frac{1}{2} \begin{bmatrix} 2 & 0 \\ 0 & -3 \\ 0 & 0 \end{bmatrix} \begin{bmatrix} 1 \\ 0 \end{bmatrix} = \frac{1}{2} \begin{bmatrix} 2 \\ 0 \\ 0 \end{bmatrix} = \begin{bmatrix} 1 \\ 0 \\ 0 \end{bmatrix}
\end{align*}
$$

{% endraw %}

以上已經找出 $Q_1$ 的 2 組特徵向量，而 $Q_1$ 是 $3 \times 3$ 的矩陣，所以我們還需要找到最後一組特徵向量

幸運的是由於 $Q_1$ 是正交矩陣，正交矩陣的 [行向量性質](/2025/10/06/正交矩陣/#7-行向量性質) 指明了第三組特徵向量必須與前 2 組特徵向量正交，於是我們需要找到一個與 $\mathbf{u}_1$ 和 $\mathbf{u}_2$ 都正交的單位向量

設 {% raw %} $\mathbf{u}_3 = \begin{bmatrix} a \\ b \\ c \end{bmatrix}$ {% endraw %}，需要滿足以下正交條件：

- $\mathbf{u}_1^T\mathbf{u}_3 = 0 \Rightarrow -b = 0 \Rightarrow b = 0$
- $\mathbf{u}_2^T\mathbf{u}_3 = 0 \Rightarrow a = 0$
- {% raw %} $\|\mathbf{u}_3\| = 1 \Rightarrow c = \pm 1$ {% endraw %}

取 {% raw %} $\mathbf{u}_3 = \begin{bmatrix} 0 \\ 0 \\ 1 \end{bmatrix}$ {% endraw %}，則：

{% raw %}

$$
Q_1 = \begin{bmatrix} \mathbf{u}_1 & \mathbf{u}_2 & \mathbf{u}_3 \end{bmatrix} = \begin{bmatrix} 0 & 1 & 0 \\ -1 & 0 & 0 \\ 0 & 0 & 1 \end{bmatrix}_{3 \times 3}
$$

{% endraw %}

##### 驗證結果

最後讓我們驗證 $A = Q_1\Sigma Q_2^T$：

{% raw %}

$$
\begin{align*}
Q_1\Sigma Q_2^T &= \begin{bmatrix} 0 & 1 & 0 \\ -1 & 0 & 0 \\ 0 & 0 & 1 \end{bmatrix} \begin{bmatrix} 3 & 0 \\ 0 & 2 \\ 0 & 0 \end{bmatrix} \begin{bmatrix} 0 & 1 \\ 1 & 0 \end{bmatrix} \\
&= \begin{bmatrix} 0 & 1 & 0 \\ -1 & 0 & 0 \\ 0 & 0 & 1 \end{bmatrix} \begin{bmatrix} 0 & 3 \\ 2 & 0 \\ 0 & 0 \end{bmatrix} \\
&= \begin{bmatrix} 2 & 0 \\ 0 & -3 \\ 0 & 0 \end{bmatrix} = A
\end{align*}
$$

{% endraw %}

驗證成功！

#### 額外計算：SVD 分解的多種方式

事實上在 步驟 2 與步驟 6 選擇特徵向量 $\mathbf{v}_i$ 與 $\mathbf{u}_i$ 的時候可以有多種組合，以下讓我們看看同一矩陣 $A$ 的另一種 SVD 分解方式

- {% raw %} $\mathbf{v}_1' = -\mathbf{v}_1 = \begin{bmatrix} 0 \\ -1 \end{bmatrix}$ {% endraw %}
- {% raw %} $\mathbf{u}_1' = -\mathbf{u}_1 = \begin{bmatrix} 0 \\ 1 \\ 0 \end{bmatrix}$ {% endraw %}
- {% raw %} $\mathbf{v}_2' = \mathbf{v}_2 = \begin{bmatrix} 1 \\ 0 \end{bmatrix}$ {% endraw %}
- {% raw %} $\mathbf{u}_2' = \mathbf{u}_2 = \begin{bmatrix} 1 \\ 0 \\ 0 \end{bmatrix}$ {% endraw %}
- {% raw %} $\mathbf{u}_3' = -\mathbf{u}_3 = \begin{bmatrix} 0 \\ 0 \\ -1 \end{bmatrix}$ {% endraw %}（一樣有保持正交性）

則：

{% raw %}

$$
Q_1' = \begin{bmatrix} 0 & 1 & 0 \\ 1 & 0 & 0 \\ 0 & 0 & -1 \end{bmatrix}, \quad
Q_2' = \begin{bmatrix} 0 & 1 \\ -1 & 0 \end{bmatrix}, \quad
\Sigma = \begin{bmatrix} 3 & 0 \\ 0 & 2 \\ 0 & 0 \end{bmatrix}
$$

{% endraw %}

驗證：

{% raw %}

$$
\begin{align*}
Q_1'\Sigma (Q_2')^T &= \begin{bmatrix} 0 & 1 & 0 \\ 1 & 0 & 0 \\ 0 & 0 & -1 \end{bmatrix} \begin{bmatrix} 3 & 0 \\ 0 & 2 \\ 0 & 0 \end{bmatrix} \begin{bmatrix} 0 & -1 \\ 1 & 0 \end{bmatrix} \\
&= \begin{bmatrix} 0 & 1 & 0 \\ 1 & 0 & 0 \\ 0 & 0 & -1 \end{bmatrix} \begin{bmatrix} 0 & -3 \\ 2 & 0 \\ 0 & 0 \end{bmatrix} \\
&= \begin{bmatrix} 2 & 0 \\ 0 & -3 \\ 0 & 0 \end{bmatrix} = A
\end{align*}
$$

{% endraw %}

**結論**：以另一組的 $Q_1'$, $Q_2'$ 所構建的 SVD 相乘一樣可以得到矩陣 $A$，這說明了 SVD 分解是有很多種方式的

## 總結

在本文中，我們詳細介紹了**奇異值分解 (SVD)** 的定義、性質和計算方法

與前面學習的分解方法（如特徵值分解、矩陣對角化、Cholesky 分解等）不同，SVD 最重要的特點是**不受矩陣形狀的限制**：無論是瘦高矩陣（$m > n$）、矮胖矩陣（$m < n$），還是方陣（$m = n$），SVD 都能提供一個優雅的分解形式 $A = Q_1\Sigma Q_2^T$。更重要的是，對於任意矩陣 $A$，SVD 分解總是存在，不需要滿足任何特殊條件（如可逆性、對稱性等），這個普遍適用性使得 SVD 成為線性代數中最強大的工具之一，能夠統一處理各種矩陣分解問題

在下一篇文章中，我們將探討 SVD 在實際應用中的重要作用，包括**圖像壓縮**（利用 SVD 的低秩近似特性，大幅減少圖像存儲空間）以及**極分解 (Polar decomposition)**（從 SVD 推導極分解，理解線性變換的幾何意義）

## 相關連結

[特徵向量與特徵值](/2025/09/20/特徵向量與特徵值/)
[矩陣對角化](/2025/10/07/矩陣對角化/)
[矩陣中的軸元與秩](/2025/09/24/矩陣中的軸元與秩/)
[零空間 - Null space](/2025/10/13/零空間-null-space/)
[向量的內積與外積](/2025/10/18/向量的內積與外積/#1-與範數的關係)
[對稱矩陣](/2025/10/15/對稱矩陣/)
[轉置矩陣](/2025/09/27/轉置矩陣/)
[正交矩陣](/2025/10/06/正交矩陣/)
[正定矩陣與半正定矩陣 (1) - 定義與性質](/2025/11/03/正定矩陣與半正定矩陣-1-定義與性質/)
[正定矩陣與半正定矩陣 (3) - Cholesky 分解](/2025/11/16/正定矩陣與半正定矩陣-3-cholesky-分解/)

## 參考資料

[陳晏笙老師開放課程：單元 15．奇異值分解-線性代數的大一統理論](https://www.youtube.com/watch?v=gH6cVt8_m9I&list=PL68D2uCy1WTNz4hadNnAXaFmb9_0fvDzg&index=16)
[線代啟示錄 - 奇異值分解 (SVD)](https://ccjou.wordpress.com/2009/09/01/%E5%A5%87%E7%95%B0%E5%80%BC%E5%88%86%E8%A7%A3-svd/)
[線代啟示錄 - 矩陣分解](https://ccjou.wordpress.com/%E5%B0%88%E9%A1%8C%E6%8E%A2%E7%A9%B6/%E7%9F%A9%E9%99%A3%E5%88%86%E8%A7%A3/)
[Wikipedia - 奇異值分解](https://zh.wikipedia.org/wiki/%E5%A5%87%E5%BC%82%E5%80%BC%E5%88%86%E8%A7%A3)
[Wikipedia - 子集](https://zh.wikipedia.org/zh-tw/%E5%AD%90%E9%9B%86)

{% raw %}

<style>
table {
  th, td {
   padding: 8px 12px;
  } 
}
</style>

{% endraw %}
