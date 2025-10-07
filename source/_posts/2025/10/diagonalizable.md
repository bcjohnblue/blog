---
title: 矩陣對角化
date: 2025-10-07 11:20:35
updated: 2025-10-07 11:20:35
categories: 線性代數
mathjax: true
---

## 定義

對於 $n \times n$ 矩陣 $A$，如果存在 [可逆矩陣](https://zh.wikipedia.org/zh-tw/%E9%9D%9E%E5%A5%87%E5%BC%82%E6%96%B9%E9%98%B5) $P$ 和 [對角矩陣](https://zh.wikipedia.org/zh-tw/%E5%B0%8D%E8%A7%92%E7%9F%A9%E9%99%A3) $D$，使得：

$$A = P D P^{-1}$$

則稱矩陣 $A$ 是 **可對角化的(diagonalizable)**，這個過程稱為 **對角化(diagonalization)**

<!-- more -->

## 對角化的條件

矩陣 $A$ 可對角化的**充分必要條件**是：

1. $A$ 有 $n$ 個線性無關的特徵向量
2. 或者等價地，$A$ 的特徵多項式有 $n$ 個線性無關的特徵向量

## 對角化步驟

### 步驟 1：求特徵值

求解特徵方程：
$$\det(A - \lambda I) = 0$$

### 步驟 2：求特徵向量

對每個特徵值 $\lambda_i$，求解：
$$(A - \lambda_i I)\mathbf{v} = \mathbf{0}$$

### 步驟 3：構建對角化矩陣

1. 將所有線性無關的特徵向量組成矩陣 $P = [\mathbf{v}_1, \mathbf{v}_2, \ldots, \mathbf{v}_n]$
2. 對角矩陣 $D$ 的對角元素為對應的特徵值：

{% raw %}

$$
D = \begin{bmatrix}
\lambda_1 & 0 & \cdots & 0 \\
0 & \lambda_2 & \cdots & 0 \\
\vdots & \vdots & \ddots & \vdots \\
0 & 0 & \cdots & \lambda_n
\end{bmatrix}
$$

{% endraw %}

### 步驟 4：驗證

檢查是否滿足：$A = P D P^{-1}$

## 例子

### 例子 1：可對角化矩陣

考慮矩陣：

{% raw %}

$$A = \begin{bmatrix} 2 & 1 \\ 0 & 3 \end{bmatrix}$$

{% endraw %}

**步驟 1：求特徵值**

{% raw %}

$$\det(A - \lambda I) = \det\begin{bmatrix} 2-\lambda & 1 \\ 0 & 3-\lambda \end{bmatrix} = (2-\lambda)(3-\lambda) = 0$$

{% endraw %}

得到特徵值：$\lambda_1 = 2, \lambda_2 = 3$

**步驟 2：求特徵向量**

對於 $\lambda_1 = 2$：

{% raw %}

$$(A - 2I)\mathbf{v} = \begin{bmatrix} 0 & 1 \\ 0 & 1 \end{bmatrix}\begin{bmatrix} x \\ y \end{bmatrix} = \begin{bmatrix} 0 \\ 0 \end{bmatrix}$$

{% endraw %}

得到特徵向量：$\mathbf{v}_1 = \begin{bmatrix} 1 \\ 0 \end{bmatrix}$

對於 $\lambda_2 = 3$：

{% raw %}

$$(A - 3I)\mathbf{v} = \begin{bmatrix} -1 & 1 \\ 0 & 0 \end{bmatrix}\begin{bmatrix} x \\ y \end{bmatrix} = \begin{bmatrix} 0 \\ 0 \end{bmatrix}$$

{% endraw %}

得到特徵向量：$\mathbf{v}_2 = \begin{bmatrix} 1 \\ 1 \end{bmatrix}$

**步驟 3：構建對角化矩陣**

{% raw %}

$$P = \begin{bmatrix} 1 & 1 \\ 0 & 1 \end{bmatrix}, \quad D = \begin{bmatrix} 2 & 0 \\ 0 & 3 \end{bmatrix}$$

{% endraw %}

**步驟 4：驗證**

{% raw %}

$$P^{-1} = \begin{bmatrix} 1 & -1 \\ 0 & 1 \end{bmatrix}$$

$$P D P^{-1} = \begin{bmatrix} 1 & 1 \\ 0 & 1 \end{bmatrix}\begin{bmatrix} 2 & 0 \\ 0 & 3 \end{bmatrix}\begin{bmatrix} 1 & -1 \\ 0 & 1 \end{bmatrix} = \begin{bmatrix} 2 & 1 \\ 0 & 3 \end{bmatrix} = A$$

{% endraw %}

### 例子 2：不可對角化矩陣

考慮矩陣：

{% raw %}

$$B = \begin{bmatrix} 1 & 1 \\ 0 & 1 \end{bmatrix}$$

{% endraw %}

**步驟 1：求特徵值**

{% raw %}

$$\det(B - \lambda I) = \det\begin{bmatrix} 1-\lambda & 1 \\ 0 & 1-\lambda \end{bmatrix} = (1-\lambda)(1-\lambda) = (1-\lambda)^2 = 0$$

{% endraw %}

得到特徵值：$\lambda = 1$

**步驟 2：求特徵向量**

對於 $\lambda = 1$：

{% raw %}

$$(B - I)\mathbf{v} = \begin{bmatrix} 0 & 1 \\ 0 & 0 \end{bmatrix}\begin{bmatrix} x \\ y \end{bmatrix} = \begin{bmatrix} 0 \\ 0 \end{bmatrix}$$

{% endraw %}

這給出方程組：

- $0 \cdot x + 1 \cdot y = 0$，即 $y = 0$
- $0 \cdot x + 0 \cdot y = 0$，即 $0 = 0$（恆等式）

因此，特徵向量為 $\mathbf{v} = \begin{bmatrix} x \\ 0 \end{bmatrix} = x\begin{bmatrix} 1 \\ 0 \end{bmatrix}$

**為什麼不可對角化**：

- 要對角化 $2 \times 2$ 矩陣，需要 2 個線性無關的特徵向量
- 但 $B$ 只有 1 個線性無關的特徵向量 $\begin{bmatrix} 1 \\ 0 \end{bmatrix}$
- 因此無法構建可逆矩陣 $P$ 來進行對角化

## 重要性質

### 1. 冪運算簡化

如果 $A = P D P^{-1}$，則：
$$A^k = P D^k P^{-1}$$

證明請看 - [三、为什么要对角化](https://zhuanlan.zhihu.com/p/684220530)

### 2. 行列式

對於可對角化矩陣 $A = P D P^{-1}$，矩陣 $A$ 與 $D$ 的行列式相等：

$$\det(A) = \det(D) = \lambda_1 \lambda_2 \cdots \lambda_n$$

證明請見 - [李宏毅老師講解的 - Characteristic Polynomial 篇章](https://youtu.be/1RyHRIP8QGg?si=ATUCEF5VNTAAg8ML&t=3473)

### 3. 跡

對於可對角化矩陣 $A = P D P^{-1}$，矩陣 $A$ 與 $D$ 的 [跡](https://zh.wikipedia.org/zh-tw/%E8%B7%A1) 相等：

$$\text{tr}(A) = \text{tr}(D) = \lambda_1 + \lambda_2 + \cdots + \lambda_n$$

證明請見 - [wiki - 跡數的相似不變性](https://zh.wikipedia.org/zh-tw/%E8%B7%A1#%E8%BF%B9%E6%95%B0%E7%9A%84%E7%9B%B8%E4%BC%BC%E4%B8%8D%E5%8F%98%E6%80%A7)

## 相關連結

[奇異矩陣](/2025/09/20/奇異矩陣-singular-matrix/)
[特徵向量與特徵值](/2025/09/20/特徵向量與特徵值/)

## 參考資料

[wiki - 可對角化矩陣](https://zh.wikipedia.org/zh-tw/%E5%B0%8D%E8%A7%92%E5%8C%96)
[wiki - 跡](https://zh.wikipedia.org/zh-tw/%E8%B7%A1)
[通俗易懂：矩阵对角化](https://zhuanlan.zhihu.com/p/684220530)
[Linear Algebra Lecture 26: Diagonalization](https://www.youtube.com/watch?v=TsB5_BiMFoo&list=PLJV_el3uVTsNmr39gwbyV-0KjULUsN7fW&index=27)
