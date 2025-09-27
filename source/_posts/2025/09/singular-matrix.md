---
title: 奇異矩陣 - Singular matrix
date: 2025-09-20 22:58:06
updated: 2025-09-20 22:58:06
categories: 線性代數
mathjax: true
---

## 定義

一個方陣 $A$ 如果 **行列式為 0**：

$$
\det(A) = 0
$$

那麼這個矩陣就是 **奇異矩陣 (Singular matrix)**

反之，如果 \$\det(A) \neq 0\$，則稱為 **非奇異矩陣 (invertible matrix)**

<!-- more -->

## 等價條件

矩陣 \$A\$ 是奇異矩陣代表以下條件中任一成立：

- \$A\$ **不可逆**，沒有 \$A^{-1}\$
- \$A\$ 的 **秩 (rank)** 小於矩陣大小
- 存在非零向量 \$x\$，使得

  $$
  A x = 0
  $$

## 幾何意義

奇異矩陣代表 **壓扁** 了空間。

例如在 \$\mathbb{R}^2\$ 的奇異矩陣：

{% raw %}

$$
A =
\begin{bmatrix}
1 & 1 \\
2 & 2
\end{bmatrix}
$$

{% endraw %}

→ \$\det(A)=0\$，兩行線性相關，會把 2D 平面壓到一條直線

## 舉例

- 非奇異

{% raw %}

$$
A =
\begin{bmatrix}
1 & 2 \\
3 & 4
\end{bmatrix}, \quad
\det(A) = -2 \neq 0
$$

{% endraw %}

- 奇異

{% raw %}

$$
B =
\begin{bmatrix}
1 & 2 \\
2 & 4
\end{bmatrix}, \quad
\det(B) = 0
$$

{% endraw %}

因為第二行是第一行的 2 倍，線性相關，因此 \$\det(B)=0\$

## 線性方程組的解

接著來探討一下 **奇異矩陣** 與 **非奇異矩陣** 在 $Ax = 0$ 與 $Ax = b$ 時各種解的狀況：

### 齊次方程組 $Ax = 0$

**奇異矩陣：**

- 有 **無窮多解**

**非奇異矩陣：**

- 有且只有 **唯一零解** $x = 0$

### 非齊次方程組 $Ax = b$

**奇異矩陣：**

- 如果 $b$ 在 $A$ 的列空間中：有 **無窮多解**

ex.

{% raw %}

$$
A =
\begin{bmatrix}
1 & 2 \\
2 & 4
\end{bmatrix}

\quad

B =
\begin{bmatrix}
3 \\
6
\end{bmatrix}
$$

{% endraw %}

最後只會化簡成一條函式，因此有無窮多解：

$$
x_1 + 2x_2 = 3
$$

- 如果 $b$ 不在 $A$ 的列空間中：**無解**

**非奇異矩陣：**

- 對任意 $b$ 都有 **唯一解** $x = A^{-1}b$

## Reference

[奇异矩阵](https://baike.baidu.com/item/%E5%A5%87%E7%95%B0%E7%9F%A9%E9%99%A3/9658459)
