---
title: 特徵向量與特徵值
date: 2025-09-20 23:22:38
updated: 2025-10-12 23:22:38
categories: 線性代數
mathjax: true
---

## 前言

**特徵向量(Eigenvector)** 與 **特徵值(Eigenvalue)** 是線性代數中最基礎的概念，這篇文章複習這兩者的計算方式

## 定義

給定一個 $n \times n$ 的方陣 $A$，如果存在一個**非零向量** $\mathbf{x}$，使得：

$$A\mathbf{x} = \lambda \mathbf{x}$$

其中 $\lambda$ 是一個純量，則稱：

- $\mathbf{x}$ 為矩陣 $A$ 的 **特徵向量**
- $\lambda$ 為矩陣 $A$ 的 **特徵值**

<!-- more -->

為什麼要規定 $\mathbf{x}$ 為非零向量？因為如果 $\mathbf{x} = \mathbf{0}$，則 $A\mathbf{x} = \lambda \mathbf{x}$ 將恆成立，這會使得任何純量都成為特徵值，失去意義

## 幾何意義

特徵向量與特徵值有一個重要的幾何解釋：

> 在特徵值為實數的情況下，畫一條通過原點的特徵向量，則在這個直線上的任何向量被 $A$ 作用後，所得到的結果仍然會在這條直線上。

換句話說，特徵向量在線性變換下保持方向不變，只是長度被縮放 $\lambda$ 倍。

## 特徵方程

要找到特徵向量和特徵值，我們需要求解：

$$A\mathbf{x} = \lambda \mathbf{x}$$

將此式改寫：

$$
A\mathbf{x} - \lambda \mathbf{x} = \mathbf{0}
$$

$$
A\mathbf{x} - \lambda I \mathbf{x} = \mathbf{0}
$$

$$
(A - \lambda I)\mathbf{x} = \mathbf{0}
$$

其中為了方便計算引入 **單位矩陣 $I$**

<br />

由於 $\mathbf{x} \neq \mathbf{0}$，但 $(A - \lambda I)\mathbf{x} = \mathbf{0}$，這意味著：

**$(A - \lambda I)$ 必須是奇異矩陣（Singular Matrix）**

為什麼呢？假設 $K = (A - \lambda I)$ ：

- 如果 $K$ 是 [奇異矩陣](/2025/09/20/奇異矩陣-singular-matrix/)，則 $K\mathbf{x} = \mathbf{0}$ 有無窮多解
- 如果 $K$ 是 **非奇異矩陣**，則 $K\mathbf{x} = \mathbf{0}$ 只有唯一零解

=> 由於我們需要 x 是非零向量，所以 $K$ 必須是 **奇異矩陣**

**奇異矩陣** 的行列式必為 0，因此：

$$\det(A - \lambda I) = 0$$

這個方程稱為 **特徵方程（Characteristic Equation）**，解此方程可得到所有特徵值

## 具體計算範例

讓我們用一個具體例子來說明計算過程，假設：

{% raw %}

$$
A = \begin{pmatrix} 1 & 2 \\ 3 & 2 \end{pmatrix}
$$

{% endraw %}

### 步驟 1：建立特徵方程

{% raw %}

$$A - \lambda I = \begin{pmatrix} 1 & 2 \\ 3 & 2 \end{pmatrix} - \lambda \begin{pmatrix} 1 & 0 \\ 0 & 1 \end{pmatrix} = \begin{pmatrix} 1-\lambda & 2 \\ 3 & 2-\lambda \end{pmatrix}$$

{% endraw %}

### 步驟 2：計算行列式

{% raw %}

$$\det(A - \lambda I) = \begin{vmatrix} 1-\lambda & 2 \\ 3 & 2-\lambda \end{vmatrix} = (1-\lambda)(2-\lambda) - 2 \cdot 3$$

$$= 2 - 3\lambda + \lambda^2 - 6 = \lambda^2 - 3\lambda - 4$$

{% endraw %}

### 步驟 3：求解特徵值

$$\lambda^2 - 3\lambda - 4 = 0$$

$$(\lambda + 1)(\lambda - 4) = 0$$

因此，特徵值為：$\lambda_1 = -1$ 和 $\lambda_2 = 4$

### 步驟 4：求對應的特徵向量

#### 對於 $\lambda_1 = -1$：

{% raw %}

$$(A - \lambda_1 I)\mathbf{x} = \mathbf{0}$$

$$\begin{pmatrix} 2 & 2 \\ 3 & 3 \end{pmatrix} \begin{pmatrix} x_1 \\ x_2 \end{pmatrix} = \begin{pmatrix} 0 \\ 0 \end{pmatrix}$$

{% endraw %}

這給出方程組：

- $2x_1 + 2x_2 = 0$
- $3x_1 + 3x_2 = 0$

兩個方程等價於：$x_1 + x_2 = 0$

選擇 $x_1 = 1$，則 $x_2 = -1$，得到特徵向量：$\mathbf{v}_1 = \begin{pmatrix} 1, -1 \end{pmatrix}$

#### 對於 $\lambda_2 = 4$：

{% raw %}

$$(A - \lambda_2 I)\mathbf{x} = \mathbf{0}$$

$$\begin{pmatrix} -3 & 2 \\ 3 & -2 \end{pmatrix} \begin{pmatrix} x_1 \\ x_2 \end{pmatrix} = \begin{pmatrix} 0 \\ 0 \end{pmatrix}$$

{% endraw %}

這給出方程組：

- $-3x_1 + 2x_2 = 0$
- $3x_1 - 2x_2 = 0$

兩個方程等價於：$3x_1 - 2x_2 = 0$

選擇 $x_1 = 2$，則 $x_2 = 3$，得到特徵向量：$\mathbf{v}_2 = \begin{pmatrix} 2, 3 \end{pmatrix}$

### 驗證

讓我們驗證結果：

{% raw %}

$$A\mathbf{v}_1 = \begin{pmatrix} 1 & 2 \\ 3 & 2 \end{pmatrix} \begin{pmatrix} 1 \\ -1 \end{pmatrix} = \begin{pmatrix} -1 \\ 1 \end{pmatrix} = -1 \begin{pmatrix} 1 \\ -1 \end{pmatrix} = \lambda_1 \mathbf{v}_1$$

$$A\mathbf{v}_2 = \begin{pmatrix} 1 & 2 \\ 3 & 2 \end{pmatrix} \begin{pmatrix} 2 \\ 3 \end{pmatrix} = \begin{pmatrix} 8 \\ 12 \end{pmatrix} = 4 \begin{pmatrix} 2 \\ 3 \end{pmatrix} = \lambda_2 \mathbf{v}_2$$

{% endraw %}

## 重要性質

### 1. 特徵向量的非唯一性

特徵向量並非唯一！對於同一個特徵值，任何滿足特徵方程的向量都是特徵向量。例如：

- 對於 $\lambda_1 = -1$，任何滿足 $x_1 + x_2 = 0$ 的向量都是特徵向量
- $(1, -1)$、$(-1, 1)$、$(2, -2)$ 等都是有效的特徵向量

### 2. 特徵向量的方向性

特徵向量之間只相差一個常數倍數，它們都位於同一條通過原點的直線上：

- $x_1 + x_2 = 0$ 上的特徵向量，$(1, -1)$、$(-1, 1)$、$(2, -2)$ 等，都只相差一個常數倍數

## 相關連結

[奇異矩陣](/2025/09/20/奇異矩陣-singular-matrix/)
[零空間 - Null space](/2025/10/13/零空間-null-space/)
[代數重數與幾何重數](/2025/10/13/代數重數與幾何重數/)

## 參考資料

- [特徵向量(Eigenvector) 及 特徵值(Eigenvalue) 的定義及求法](https://silverwind1982.pixnet.net/blog/post/154593170)

- [Linear Algebra Lecture 25: Eigenvalues and Eigenvectors](https://www.youtube.com/watch?v=1RyHRIP8QGg&list=PLJV_el3uVTsNmr39gwbyV-0KjULUsN7fW&index=25)
