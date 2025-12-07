---
title: 零空間 - Null space
date: 2025-10-13 22:46:54
updated: 2025-12-07 22:46:54
categories: 線性代數
mathjax: true
---

## 定義

對一個 $m \times n$ 的矩陣 $A$，我們定義它的 [零空間(Null space)](https://zh.wikipedia.org/zh-tw/%E9%9B%B6%E7%A9%BA%E9%97%B4) 為：

$$
\text{Null}(A) = \{\mathbf{v} \in V : A\mathbf{v} = \mathbf{0}\}
$$

也就是所有能讓 $A\mathbf{v}=0$ 的向量集合

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
  其中 $n$ 是 $\text{A}$ 的行數

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

---

## 左零空間

對一個 $m \times n$ 的矩陣 $A$，我們定義它的 **左零空間(Left null space)** 為：

$$
\text{Null}(A^T) = \{\mathbf{y} \in \mathbb{R}^m : A^T\mathbf{y} = \mathbf{0}\}
$$

也就是所有能讓 $A^T\mathbf{y} = \mathbf{0}$ 的向量 $\mathbf{y}$ 的集合

### 與零空間的關係

一開始最上面提到的 **零空間(Null space)**：$\text{Null}(A)$ 也被稱為 **右零空間(Right null space)**，因為它在 $A$ 的右邊乘以 $\mathbf{x}$，即 $A\mathbf{x} = \mathbf{0}$

而 **左零空間(Left null space)**：$\text{Null}(A^T)$ 滿足 $A^T\mathbf{y} = \mathbf{0}$，套用 [轉置矩陣的性質](/2025/09/27/轉置矩陣/#4-矩陣乘法的轉置)，等價地可以寫成：

$$
\mathbf{y}^T A = \mathbf{0}
$$

由於這是在矩陣 $A$ 的左邊乘以 $\mathbf{y}^T$，因此稱為左零空間

### 性質

- **左零空間也是一個子空間**

  因為 $\text{Null}(A^T)$ 是 $A^T$ 的零空間，而零空間是子空間，所以左零空間也是子空間

<br />

- **左零空間的維度**

  $$
  \dim(\text{Null}(A^T)) = m - \text{rank}(A)
  $$

  這是因為 $A^T$ 是一個 $n \times m$ 的矩陣，根據 **秩－零化度定理(Rank-Nullity Theorem)**：

  $$
  \text{rank}(A^T) + \text{nullity}(A^T) = m
  $$

  而 $\text{rank}(A^T) = \text{rank}(A)$，因此：

  $$
  \text{nullity}(A^T) = m - \text{rank}(A)
  $$

### 範例

沿用前面的例子，設

{% raw %}

$$
A =
\begin{bmatrix}
1 & 2 & 3 \\
2 & 4 & 6
\end{bmatrix}
$$

{% endraw %}

我們要求解 $A^T \mathbf{y} = \mathbf{0}$：

{% raw %}

$$
A^T =
\begin{bmatrix}
1 & 2 \\
2 & 4 \\
3 & 6
\end{bmatrix}
$$

{% endraw %}

{% raw %}

$$
\begin{bmatrix}
1 & 2 \\
2 & 4 \\
3 & 6
\end{bmatrix}

\begin{bmatrix}
y_1 \\ y_2
\end{bmatrix}
=
\begin{bmatrix}
0 \\ 0 \\ 0
\end{bmatrix}
$$

{% endraw %}

轉換為簡化列階梯形式(RREF)：

{% raw %}

$$
\begin{bmatrix}
1 & 2 \\
0 & 0 \\
0 & 0
\end{bmatrix}
$$

{% endraw %}

得到方程式：

$$
y_1 + 2y_2 = 0
$$

解出：

$$
y_1 = -2y_2
$$

{% raw %}

$$
\mathbf{y} = \begin{bmatrix}
y_1\\y_2
\end{bmatrix}
=
\begin{bmatrix}
-2y_2\\y_2
\end{bmatrix}
=
y_2
\begin{bmatrix}
-2\\1
\end{bmatrix}
$$

{% endraw %}

因此：

{% raw %}

$$
\text{Null}(A^T) = \text{span}\left\{
\begin{bmatrix}-2\\1\end{bmatrix}
\right\}
$$

{% endraw %}

所以：

$$
\dim(\text{Null}(A^T)) = 1
$$

驗證：$A$ 是一個 $2 \times 3$ 的矩陣，$\text{rank}(A) = 1$，因此：

$$
\dim(\text{Null}(A^T)) = 2 - 1 = 1
$$

符合計算結果

---

## 兩個零空間的正交關係

沿用前面的範例，設

{% raw %}

$$
A =
\begin{bmatrix}
1 & 2 & 3 \\
2 & 4 & 6
\end{bmatrix}
$$

{% endraw %}

### 右零空間：與列空間正交

**右零空間(Right null space)** $\text{Null}(A)$ 與矩陣 $A$ 的 **列空間(Row space)** 正交

由前面的計算可知：

{% raw %}

$$
\text{Null}(A) = \text{span}\left\{
\begin{bmatrix}-2\\1\\0\end{bmatrix},
\begin{bmatrix}-3\\0\\1\end{bmatrix}
\right\}
$$

{% endraw %}

矩陣 $A$ 的列向量為：

{% raw %}

$$
\mathbf{r}_1 = \begin{bmatrix}1 & 2 & 3\end{bmatrix}, \quad
\mathbf{r}_2 = \begin{bmatrix}2 & 4 & 6\end{bmatrix}
$$

{% endraw %}

由於 $\mathbf{r}_2 = 2\mathbf{r}_1$，列空間為：

{% raw %}

$$
\text{Row}(A) = \text{span}\left\{\begin{bmatrix}1 & 2 & 3\end{bmatrix}\right\}
$$

{% endraw %}

驗證正交性：對於右零空間的基底向量 {% raw %} $\begin{bmatrix}-2\\1\\0\end{bmatrix}$ {% endraw %} 和列空間的基底向量 {% raw %} $\begin{bmatrix}1 & 2 & 3\end{bmatrix}$ {% endraw %}：

{% raw %}

$$
\begin{bmatrix}1 & 2 & 3\end{bmatrix} \begin{bmatrix}-2\\1\\0\end{bmatrix} = (1)(-2) + (2)(1) + (3)(0) = -2 + 2 + 0 = 0
$$

{% endraw %}

同樣地，對於另一個基底向量：

{% raw %}

$$
\begin{bmatrix}1 & 2 & 3\end{bmatrix} \begin{bmatrix}-3\\0\\1\end{bmatrix} = (1)(-3) + (2)(0) + (3)(1) = -3 + 0 + 3 = 0
$$

{% endraw %}

**結論：** 右零空間 $\text{Null}(A)$ 與列空間 $\text{Row}(A)$ 正交

### 左零空間：與行空間正交

**左零空間(Left null space)** $\text{Null}(A^T)$ 與矩陣 $A$ 的 **行空間(Column space)** 正交

由前面的計算可知：

{% raw %}

$$
\text{Null}(A^T) = \text{span}\left\{
\begin{bmatrix}-2\\1\end{bmatrix}
\right\}
$$

{% endraw %}

矩陣 $A$ 的行向量為：

{% raw %}

$$
\mathbf{r}_1 = \begin{bmatrix}1 \\ 2\end{bmatrix}, \quad
\mathbf{r}_2 = \begin{bmatrix}2 \\ 4\end{bmatrix}, \quad
\mathbf{r}_3 = \begin{bmatrix}3 \\ 6\end{bmatrix}
$$

{% endraw %}

由於 $\mathbf{r}_2 = 2\mathbf{r}_1$ 且 $\mathbf{r}_3 = 3\mathbf{r}_1$，$A$ 的行空間為：

{% raw %}

$$
\text{Col}(A) = \text{span}\left\{\begin{bmatrix}1 \\ 2\end{bmatrix}\right\}
$$

{% endraw %}

驗證正交性：因為 $A^T\mathbf{y} = \mathbf{0}$，對於左零空間的基底向量 {% raw %} $\mathbf{y} = \begin{bmatrix}-2\\1\end{bmatrix}$ {% endraw %} 和 $A$ 的行空間基底向量 {% raw %} $\begin{bmatrix}1 \\ 2\end{bmatrix}$ {% endraw %}：

{% raw %}

$$
\begin{bmatrix}1 & 2\end{bmatrix} \begin{bmatrix}-2 \\ 1\end{bmatrix} = (-2)(1) + (1)(2) = -2 + 2 = 0
$$

{% endraw %}

**結論：** 左零空間 $\text{Null}(A^T)$ 與欄空間 $\text{Col}(A)$ 正交

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
[wiki - 列空間與行空間](https://zh.wikipedia.org/zh-tw/%E8%A1%8C%E7%A9%BA%E9%97%B4%E4%B8%8E%E5%88%97%E7%A9%BA%E9%97%B4)
[wiki - 秩—零化度定理](https://zh.wikipedia.org/zh-tw/%E7%A7%A9%E2%80%94%E9%9B%B6%E5%8C%96%E5%BA%A6%E5%AE%9A%E7%90%86)
[Linear Algebra Lecture 18: Subspace](https://www.youtube.com/watch?v=pXtXnY2b2-E&list=PLJV_el3uVTsNmr39gwbyV-0KjULUsN7fW&index=19)
[Linear Algebra Lecture 20: Column Space, Null Space, Row Space](https://www.youtube.com/watch?v=aW0JVmpIxas&list=PLJV_el3uVTsNmr39gwbyV-0KjULUsN7fW&index=21)
[Linear Algebra Lecture 25: Eigenvalues and Eigenvectors](https://www.youtube.com/watch?v=1RyHRIP8QGg&list=PLJV_el3uVTsNmr39gwbyV-0KjULUsN7fW&index=25)
[矩阵乘法核心思想（4）：左零空间](https://zhuanlan.zhihu.com/p/350127114)
[线性代数笔记 14——行空间和左零空间](https://www.cnblogs.com/bigmonkey/p/9703636.html)
