---
title: LU 分解
date: 2025-10-20 23:03:41
updated: 2025-11-03 23:03:41
categories: 線性代數
mathjax: true
---

## LU 分解的定義

對於一個 $n \times n$ 的方陣 $A$，如果存在下三角矩陣 $L$ 和上三角矩陣 $U$，使得：

$$A = LU$$

其中：

- $L$ 是下三角矩陣，對角線元素為 1
- $U$ 是上三角矩陣

這就是 LU 分解

<!-- more -->

### 矩陣形式

{% raw %}

$$
L = \begin{bmatrix}
1 & 0 & 0 & \cdots & 0 \\
l_{21} & 1 & 0 & \cdots & 0 \\
l_{31} & l_{32} & 1 & \cdots & 0 \\
\vdots & \vdots & \vdots & \ddots & \vdots \\
l_{n1} & l_{n2} & l_{n3} & \cdots & 1
\end{bmatrix}
$$

$$
U = \begin{bmatrix}
u_{11} & u_{12} & u_{13} & \cdots & u_{1n} \\
0 & u_{22} & u_{23} & \cdots & u_{2n} \\
0 & 0 & u_{33} & \cdots & u_{3n} \\
\vdots & \vdots & \vdots & \ddots & \vdots \\
0 & 0 & 0 & \cdots & u_{nn}
\end{bmatrix}
$$

{% endraw %}

## LU 分解的計算方法

對於矩陣 $A$，我們通過高斯消去法將其轉化為上三角矩陣 $U$，同時記錄這些變換中的消去係數，最終藉由消去係數得到下三角矩陣 $L$

**高斯消去法步驟**

1. 選擇第一個軸元(pivot)：$a_{11}$
2. 對第 $i$ 行（$i > 1$），計算消去係數：$l_{i1} = \frac{a_{i1}}{a_{11}}$
3. 執行消去
4. 重複此過程直到將 $A$ 轉化為上三角矩陣 $U$

## LU 分解範例

讓我們通過一個具體的 $3 \times 3$ 矩陣來演示 LU 分解的計算過程

### 範例矩陣

考慮矩陣：

{% raw %}

$$
A = \begin{bmatrix}
2 & 1 & 1 \\
4 & 3 & 3 \\
8 & 7 & 9
\end{bmatrix}
$$

{% endraw %}

### 第一步：消去第一列

**目標**：將第一列下方元素變為 0

**計算消去係數**：

- $l_{21} = \frac{a_{21}}{a_{11}} = \frac{4}{2} = 2$
- $l_{31} = \frac{a_{31}}{a_{11}} = \frac{8}{2} = 4$

**執行消去**：

{% raw %}

- $R_2 \leftarrow R_2 - 2 \cdot R_1$：
  $$\begin{bmatrix} 4 & 3 & 3 \end{bmatrix} - 2 \cdot \begin{bmatrix} 2 & 1 & 1 \end{bmatrix} = \begin{bmatrix} 0 & 1 & 1 \end{bmatrix}$$

- $R_3 \leftarrow R_3 - 4 \cdot R_1$：
  $$\begin{bmatrix} 8 & 7 & 9 \end{bmatrix} - 4 \cdot \begin{bmatrix} 2 & 1 & 1 \end{bmatrix} = \begin{bmatrix} 0 & 3 & 5 \end{bmatrix}$$

{% endraw %}

**第一次消去後的矩陣**：

{% raw %}

$$
A^{(1)} = \begin{bmatrix}
2 & 1 & 1 \\
0 & 1 & 1 \\
0 & 3 & 5
\end{bmatrix}
$$

{% endraw %}

### 第二步：消去第二列

**目標**：將第二列下方元素變為 0

**計算消去係數**：

- $l_{32} = \frac{a_{32}^{(1)}}{a_{22}^{(1)}} = \frac{3}{1} = 3$

**執行消去**：

- $R_3 \leftarrow R_3 - 3 \cdot R_2$：
  $$\begin{bmatrix} 0 & 3 & 5 \end{bmatrix} - 3 \cdot \begin{bmatrix} 0 & 1 & 1 \end{bmatrix} = \begin{bmatrix} 0 & 0 & 2 \end{bmatrix}$$

**最終的上三角矩陣**：

{% raw %}

$$
U = \begin{bmatrix}
2 & 1 & 1 \\
0 & 1 & 1 \\
0 & 0 & 2
\end{bmatrix}
$$

{% endraw %}

### 構建下三角矩陣 L

根據消去係數，我們得到：

{% raw %}

$$
L = \begin{bmatrix}
1 & 0 & 0 \\
2 & 1 & 0 \\
4 & 3 & 1
\end{bmatrix}
$$

{% endraw %}

### 驗證分解

讓我們驗證 $A = LU$：

{% raw %}

$$
LU = \begin{bmatrix}
1 & 0 & 0 \\
2 & 1 & 0 \\
4 & 3 & 1
\end{bmatrix}
\begin{bmatrix}
2 & 1 & 1 \\
0 & 1 & 1 \\
0 & 0 & 2
\end{bmatrix}
$$

$$
= \begin{bmatrix}
1 \cdot 2 + 0 \cdot 0 + 0 \cdot 0 & 1 \cdot 1 + 0 \cdot 1 + 0 \cdot 0 & 1 \cdot 1 + 0 \cdot 1 + 0 \cdot 2 \\
2 \cdot 2 + 1 \cdot 0 + 0 \cdot 0 & 2 \cdot 1 + 1 \cdot 1 + 0 \cdot 0 & 2 \cdot 1 + 1 \cdot 1 + 0 \cdot 2 \\
4 \cdot 2 + 3 \cdot 0 + 1 \cdot 0 & 4 \cdot 1 + 3 \cdot 1 + 1 \cdot 0 & 4 \cdot 1 + 3 \cdot 1 + 1 \cdot 2
\end{bmatrix}
$$

$$
= \begin{bmatrix}
2 & 1 & 1 \\
4 & 3 & 3 \\
8 & 7 & 9
\end{bmatrix} = A
$$

{% endraw %}

<!-- ### 計算行列式

根據 LU 分解與行列式的關係：
$$\det(A) = \det(L) \cdot \det(U) = 1 \cdot (2 \cdot 1 \cdot 2) = 4$$ -->

<!-- ### Pivot 的行列式表示驗證

讓我們驗證 pivot 的行列式表示：

**第一個 pivot**：$u_{11} = 2$

- $A^{(1)} = \begin{bmatrix} 2 \end{bmatrix}$，所以 $\det(A^{(1)}) = 2$
- $A^{(0)} = 1$，所以 $\det(A^{(0)}) = 1$
- 因此：$u_{11} = \frac{\det(A^{(1)})}{\det(A^{(0)})} = \frac{2}{1} = 2$ ✓

**第二個 pivot**：$u_{22} = 1$

- $A^{(2)} = \begin{bmatrix} 2 & 1 \\ 4 & 3 \end{bmatrix}$，所以 $\det(A^{(2)}) = 2 \cdot 3 - 1 \cdot 4 = 2$
- $A^{(1)} = \begin{bmatrix} 2 \end{bmatrix}$，所以 $\det(A^{(1)}) = 2$
- 因此：$u_{22} = \frac{\det(A^{(2)})}{\det(A^{(1)})} = \frac{2}{2} = 1$ ✓

**第三個 pivot**：$u_{33} = 2$

- $A^{(3)} = A = \begin{bmatrix} 2 & 1 & 1 \\ 4 & 3 & 3 \\ 8 & 7 & 9 \end{bmatrix}$
- 計算 $\det(A^{(3)})$：
  $$\det(A^{(3)}) = 2 \cdot (3 \cdot 9 - 3 \cdot 7) - 1 \cdot (4 \cdot 9 - 3 \cdot 8) + 1 \cdot (4 \cdot 7 - 3 \cdot 8)$$
  $$= 2 \cdot (27 - 21) - 1 \cdot (36 - 24) + 1 \cdot (28 - 24)$$
  $$= 2 \cdot 6 - 1 \cdot 12 + 1 \cdot 4 = 12 - 12 + 4 = 4$$
- $A^{(2)} = \begin{bmatrix} 2 & 1 \\ 4 & 3 \end{bmatrix}$，所以 $\det(A^{(2)}) = 2$
- 因此：$u_{33} = \frac{\det(A^{(3)})}{\det(A^{(2)})} = \frac{4}{2} = 2$ ✓ -->

## LU 分解與行列式的關係

### 基本關係

由於 $A = LU$，根據 [行列式的乘法定理](https://zh.wikipedia.org/zh-tw/%E8%A1%8C%E5%88%97%E5%BC%8F#:~:text=%7C-,%E8%A1%8C%E5%88%97%E5%BC%8F%E7%9A%84%E4%B9%98%E6%B3%95%E5%AE%9A%E7%90%86,-%EF%BC%9A%E6%96%B9%E5%A1%8A%E7%9F%A9%E9%99%A3)：

$$\det(A) = \det(L) \cdot \det(U)$$

### 三角矩陣的行列式

對於三角矩陣，行列式等於對角線元素的乘積：

- 下三角矩陣 $L$：$\det(L) = 1$（因為對角線元素都是 1）
- 上三角矩陣 $U$：$\det(U) = u_{11} \cdot u_{22} \cdot \cdots \cdot u_{nn}$

因此：

$$\det(A) = u_{11} \cdot u_{22} \cdot \cdots \cdot u_{nn}$$

這表明矩陣 $A$ 的行列式等於 $U$ 矩陣對角線元素的乘積

### 範例矩陣

讓我們使用前面的範例矩陣來驗證 LU 分解與行列式的關係：

{% raw %}

$$
A = \begin{bmatrix}
2 & 1 & 1 \\
4 & 3 & 3 \\
8 & 7 & 9
\end{bmatrix}
$$

$$
A = LU = \begin{bmatrix}
1 & 0 & 0 \\
2 & 1 & 0 \\
4 & 3 & 1
\end{bmatrix}
\begin{bmatrix}
2 & 1 & 1 \\
0 & 1 & 1 \\
0 & 0 & 2
\end{bmatrix}
$$

{% endraw %}

直接計算 $\det(A)$：

{% raw %}

$$\det(A) = 2 \cdot (3 \cdot 9 - 3 \cdot 7) - 1 \cdot (4 \cdot 9 - 3 \cdot 8) + 1 \cdot (4 \cdot 7 - 3 \cdot 8)$$
$$= 2 \cdot (27 - 21) - 1 \cdot (36 - 24) + 1 \cdot (28 - 24)$$
$$= 2 \cdot 6 - 1 \cdot 12 + 1 \cdot 4 = 12 - 12 + 4 = 4$$

{% endraw %}

用 $U$ 矩陣對角線元素的乘積進行計算：

$$
\det(A) = u_{11} \cdot u_{22} \cdot u_{33} = 2 \cdot 1 \cdot 2 = 4
$$

與直接計算 $\det(A)$ 相同

## LU 分解與軸元的關係

接著我們來證明每個 [軸元(pivot)](/2025/09/24/矩陣中的軸元與秩/) 都可以用行列式來表示

### 定理

設 $A$ 是一個 $n \times n$ 的可逆矩陣，$A^{(k)}$ 表示 $A$ 的前 $k$ 行前 $k$ 列組成的子矩陣，則 LU 分解中的第 $k$ 個軸元 $u_{kk}$ 可以表示為：

$$u_{kk} = \frac{\det(A^{(k)})}{\det(A^{(k-1)})}$$

其中 $\det(A^{(0)}) = 1$

### 證明

我們使用數學歸納法來證明這個定理

#### 基礎情況（k = 1）

對於 $k = 1$，我們有：
$$u_{11} = a_{11} = \det(A^{(1)}) = \frac{\det(A^{(1)})}{\det(A^{(0)})}$$

基礎情況成立

#### 歸納假設

假設對於 $k = m$，定理成立，即：
$$u_{mm} = \frac{\det(A^{(m)})}{\det(A^{(m-1)})}$$

#### 歸納步驟

現在證明對於 $k = m + 1$ 也成立

考慮矩陣 $A^{(m+1)}$ 的 LU 分解：

$$A^{(m+1)} = L^{(m+1)} U^{(m+1)}$$

其中 $L^{(m+1)}$ 和 $U^{(m+1)}$ 分別是 $(m+1) \times (m+1)$ 的下三角和上三角矩陣。

由於 $L^{(m+1)}$ 是下三角矩陣且對角線元素為 1，有：
$$\det(L^{(m+1)}) = 1$$

因此：
$$\det(A^{(m+1)}) = \det(U^{(m+1)}) = u_{11} \cdot u_{22} \cdot \cdots \cdot u_{m+1,m+1}$$

同樣地：
$$\det(A^{(m)}) = u_{11} \cdot u_{22} \cdot \cdots \cdot u_{mm}$$

因此：
$$\frac{\det(A^{(m+1)})}{\det(A^{(m)})} = \frac{u_{11} \cdot u_{22} \cdot \cdots \cdot u_{m+1,m+1}}{u_{11} \cdot u_{22} \cdot \cdots \cdot u_{mm}} = u_{m+1,m+1}$$

這證明了：
$$u_{m+1,m+1} = \frac{\det(A^{(m+1)})}{\det(A^{(m)})}$$

歸納步驟完成

### 範例矩陣

讓我們使用前面的範例矩陣來驗證軸元的行列式表示：

{% raw %}

$$
A = \begin{bmatrix}
2 & 1 & 1 \\
4 & 3 & 3 \\
8 & 7 & 9
\end{bmatrix}
$$

$$
A = LU = \begin{bmatrix}
1 & 0 & 0 \\
2 & 1 & 0 \\
4 & 3 & 1
\end{bmatrix}
\begin{bmatrix}
2 & 1 & 1 \\
0 & 1 & 1 \\
0 & 0 & 2
\end{bmatrix}
$$

{% endraw %}

**第一個軸元**：$u_{11} = 2$

- $A^{(1)} = \begin{bmatrix} 2 \end{bmatrix}$，所以 $\det(A^{(1)}) = 2$
- $A^{(0)} = 1$，所以 $\det(A^{(0)}) = 1$
- 因此：$u_{11} = \frac{\det(A^{(1)})}{\det(A^{(0)})} = \frac{2}{1} = 2$ ✓

**第二個軸元**：$u_{22} = 1$

- $A^{(2)} = {% raw %} \begin{bmatrix} 2 & 1 \\ 4 & 3 \end{bmatrix}$ {% endraw %}，所以 $\det(A^{(2)}) = 2 \cdot 3 - 1 \cdot 4 = 2$
- $A^{(1)} = \begin{bmatrix} 2 \end{bmatrix}$，所以 $\det(A^{(1)}) = 2$
- 因此：$u_{22} = \frac{\det(A^{(2)})}{\det(A^{(1)})} = \frac{2}{2} = 1$ ✓

**第三個軸元**：$u_{33} = 2$

- {% raw %} $A^{(3)} = A = \begin{bmatrix} 2 & 1 & 1 \\ 4 & 3 & 3 \\ 8 & 7 & 9 \end{bmatrix}$ {% endraw %}
- 計算 $\det(A^{(3)})$：
  {% raw %} $$\det(A^{(3)}) = 2 \cdot (3 \cdot 9 - 3 \cdot 7) - 1 \cdot (4 \cdot 9 - 3 \cdot 8) + 1 \cdot (4 \cdot 7 - 3 \cdot 8)$$
  $$= 2 \cdot (27 - 21) - 1 \cdot (36 - 24) + 1 \cdot (28 - 24)$$
  $$= 2 \cdot 6 - 1 \cdot 12 + 1 \cdot 4 = 12 - 12 + 4 = 4$$ {% endraw %}
- {% raw %} $A^{(2)} = \begin{bmatrix} 2 & 1 \\ 4 & 3 \end{bmatrix}$ {% endraw %}，所以 $\det(A^{(2)}) = 2$
- 因此：$u_{33} = \frac{\det(A^{(3)})}{\det(A^{(2)})} = \frac{4}{2} = 2$ ✓

## 相關連結

[矩陣中的軸元與秩](/2025/09/24/矩陣中的軸元與秩/)

## 參考資料

[單元 3．矩陣的基礎操作-LU 分解、反矩陣](https://www.youtube.com/watch?v=fWd5FHR7j8k)
