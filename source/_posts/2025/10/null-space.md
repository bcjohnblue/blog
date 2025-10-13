---
title: 零空間 - Null space
date: 2025-10-13 22:46:54
updated: 2025-10-13 22:46:54
categories: 線性代數
mathjax: true
---

## 定義

對一個 $m \times n$ 的矩陣 $A$，我們定義它的 [零空間(Null space)](https://zh.wikipedia.org/zh-tw/%E9%9B%B6%E7%A9%BA%E9%97%B4) 為：

$$
\text{Null}(A) = \{\mathbf{v} \in V : A\mathbf{v} = \mathbf{0}\}
$$

也就是所有能讓 $A\mathbf{v}=0$ 的向量集合

<!-- 也就是所有被 $A$ 乘了以後結果是 [零向量(Zero Vector)](https://zh.wikipedia.org/zh-tw/%E5%90%91%E9%87%8F#%E9%9B%B6%E5%90%91%E9%87%8F) 的 $\mathbf{v}$ -->

<!-- more -->

## 性質

- **零空間是一個 [子空間(Subspace)](https://youtu.be/pXtXnY2b2-E?si=_i6AOxIOMsmCvcxO&t=256)**

  因此 **零空間(Null space)** 同樣有以下兩點 **子空間(Subspace)** 的特性

  1. 若 $\mathbf{x}, \mathbf{y} \in \mathrm{Null}(A)$，則

  $$
  A(\mathbf{x}+\mathbf{y}) = A\mathbf{x} + A\mathbf{y} = 0 + 0 = 0
  $$

  2. 且對任意常數 \( c \)：

  $$
  A(c\mathbf{x}) = cA\mathbf{x} = c \cdot 0 = 0
  $$

<br />

- **零空間的維度(nullity)**

  $$
  \text{nullity}(A) = \dim(\mathrm{Null}(A))
  $$

- **[秩－零化度定理(Rank-Nullity Theorem)](https://zh.wikipedia.org/zh-tw/%E7%A7%A9%E2%80%94%E9%9B%B6%E5%8C%96%E5%BA%A6%E5%AE%9A%E7%90%86)**
  $$
  \text{rank}(A) + \text{nullity}(A) = n
  $$
  其中 $n$ 是 $\text{A}$ 的列數

## 範例

設

{% raw %}

$$
A =
\begin{bmatrix}
1 & 2 & 3 \\
2 & 4 & 6
\end{bmatrix}
$$

{% endraw %}

我們要求解 $A x = 0$

{% raw %}

$$
\begin{bmatrix}
1 & 2 & 3 \\
2 & 4 & 6
\end{bmatrix}
\begin{bmatrix}
x_1 \\ x_2 \\ x_3
\end{bmatrix}
=
\begin{bmatrix}
0 \\ 0
\end{bmatrix}
$$

{% endraw %}

轉換為 [簡化列階梯形式(RREF)](https://zh.wikipedia.org/zh-tw/%E9%98%B6%E6%A2%AF%E5%BD%A2%E7%9F%A9%E9%98%B5#%E7%AE%80%E5%8C%96-{zh-hans:%E5%88%97;_zh-hant:%E5%88%97;}-%E9%98%B6%E6%A2%AF%E5%BD%A2%E7%9F%A9%E9%98%B5)：

{% raw %}

$$
\begin{bmatrix}
1 & 2 & 3 \\
0 & 0 & 0
\end{bmatrix}
$$

{% endraw %}

得到方程式：

$$
x_1 + 2x_2 + 3x_3 = 0
$$

解出自由變數：

$$
x_1 = -2x_2 - 3x_3
$$

{% raw %}

$$
x = \begin{bmatrix}
x_1\\x_2\\x_3
\end{bmatrix}
=
\begin{bmatrix}
-2x_2 - 3x_3\\x_2\\x_3
\end{bmatrix}
=
x_2
\begin{bmatrix}
-2\\1\\0
\end{bmatrix}
+
x_3
\begin{bmatrix}
-3\\0\\1
\end{bmatrix}
$$

{% endraw %}

因此：

{% raw %}

$$
\mathrm{Null}(A) = \text{span}\left\{
\begin{bmatrix}-2\\1\\0\end{bmatrix},
\begin{bmatrix}-3\\0\\1\end{bmatrix}
\right\}
$$

{% endraw %}

所以：

$$
\text{nullity}(A) = 2
$$

$A$ 是一個 $2 \times 3 $ 的矩陣，由以上計算可知：

$$
\text{rank}(A) = 1
$$

$$
\text{nullity}(A) = 2
$$

矩陣 $A$ 遵守 **秩－零化度定理(Rank-Nullity Theorem)**：

$$
\text{rank}(A) + \text{nullity}(A) = 3
$$

## 與特徵值的關係

在 [特徵向量與特徵值](/2025/09/20/特徵向量與特徵值/) 中：

$$
(A - \lambda I)\mathbf{x} = 0
$$

這個方程的解空間（即 $\mathrm{Null}(A - \lambda I)$），就是 [特徵空間(eigenspace)](https://youtu.be/1RyHRIP8QGg?si=8CeMpfmz0F51Bv94&t=1496)，其維度為該特徵值 $\lambda$ 的 [幾何重數(geometric multiplicity)](/2025/10/13/代數重數與幾何重數/#幾何重數（Geometric-Multiplicity）)

## 補充資料

### 1. Nullity 定義

矩陣 $A$ 的 **零化度(Nullity)** 就是這個 **零空間(Null space)** 的 [維度](https://zh.wikipedia.org/zh-tw/%E5%90%91%E9%87%8F%E7%A9%BA%E9%97%B4%E7%9A%84%E7%BB%B4%E6%95%B0)：

$$
\text{nullity}(A) = \dim(\mathrm{Null}(A))
$$

## 相關連結

[矩陣中的軸元與秩](/2025/09/24/矩陣中的軸元與秩/)
[特徵向量與特徵值](/2025/09/20/特徵向量與特徵值/)
[代數重數與幾何重數](/2025/10/13/代數重數與幾何重數/)
[線性生成空間-span](/2025/10/13/線性生成空間-span/)

## 參考資料

[wiki - 零空間](https://zh.wikipedia.org/zh-tw/%E9%9B%B6%E7%A9%BA%E9%97%B4)
[wiki - 秩—零化度定理](https://zh.wikipedia.org/zh-tw/%E7%A7%A9%E2%80%94%E9%9B%B6%E5%8C%96%E5%BA%A6%E5%AE%9A%E7%90%86)
[Linear Algebra Lecture 18: Subspace](https://www.youtube.com/watch?v=pXtXnY2b2-E&list=PLJV_el3uVTsNmr39gwbyV-0KjULUsN7fW&index=19)
[Linear Algebra Lecture 20: Column Space, Null Space, Row Space](https://www.youtube.com/watch?v=aW0JVmpIxas&list=PLJV_el3uVTsNmr39gwbyV-0KjULUsN7fW&index=21)
[Linear Algebra Lecture 25: Eigenvalues and Eigenvectors](https://www.youtube.com/watch?v=1RyHRIP8QGg&list=PLJV_el3uVTsNmr39gwbyV-0KjULUsN7fW&index=25)