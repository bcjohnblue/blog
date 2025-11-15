---
title: 正定矩陣與半正定矩陣 (1) - 定義與性質
date: 2025-11-03 00:23:19
updated: 2025-11-16 00:23:19
categories: 線性代數
mathjax: true
---

###### 此為 **正定矩陣與半正定矩陣** 系列文章 - 第 1 篇：

1. <a href="/2025/11/03/正定矩陣與半正定矩陣-1-定義與性質/" target="_blank">正定矩陣與半正定矩陣 (1) - 定義與性質</a>
2. <a href="/2025/11/12/正定矩陣與半正定矩陣-2-橢球體的形狀/" target="_blank">正定矩陣與半正定矩陣 (2) - 橢球體的形狀</a>
3. <a href="/2025/11/16/正定矩陣與半正定矩陣-3-cholesky-分解/" target="_blank">正定矩陣與半正定矩陣 (3) - Cholesky 分解</a>

## 前言

我們知道數字可以分為正數、負數和零。對於矩陣來說是否也有類似「正負性」的概念呢？

**正定矩陣(Positive definite matrix)** 和 **半正定矩陣(Positive semi-definite matrix)** 就是用來描述矩陣「正負性」的重要概念。簡單來說：

- **正定矩陣**：就像「恆正的矩陣」，對應的二次函數圖形是碗狀的（有最小值）
- **半正定矩陣**：就像「非負的矩陣」，對應的二次函數圖形可能是碗狀的，也可能是平的

下面將從二次型的角度出發，深入探討這兩個概念的定義、等價條件，以及如何判斷一個矩陣是否為正定或半正定矩陣

## 二次型（Quadratic Form）

### 定義

對於 $n \times n$ 實對稱矩陣 $A$ 和 $n$ 維向量 $\mathbf{x}$，表達式：

$$Q(\mathbf{x}) = \mathbf{x}^T A \mathbf{x}$$

稱為矩陣 $A$ 的**二次型**

<!-- more -->

### 展開形式

一般地，對於 $n \times n$ 矩陣 $A = (a_{ij})$，二次型可以表示為：

{% raw %}

$$
f = \sum_{i=1}^n \sum_{j=1}^n a_{ij} x_i x_j
   = a_{11}x_1^2 + a_{12}x_1x_2 + a_{12}x_2x_1 + \cdots + a_{nn}x_n^2
$$

$$
= \begin{bmatrix}
x_1 & x_2 & \cdots & x_n
\end{bmatrix}
\begin{bmatrix}
a_{11} & a_{12} & \cdots & a_{1n} \\
a_{21} & a_{22} & \cdots & a_{2n} \\
\vdots & \vdots & \ddots & \vdots \\
a_{n1} & a_{n2} & \cdots & a_{nn}
\end{bmatrix}
\begin{bmatrix}
x_1 \\
x_2 \\
\vdots \\
x_n
\end{bmatrix}
= \mathbf{x}^T A \mathbf{x}
$$

{% endraw %}

以 $2 \times 2$ 的矩陣舉例：

{% raw %}

$$
A = \begin{bmatrix} 3 & 4 \\ 4 & 6 \end{bmatrix}
$$

$$
f = \mathbf{x}^T A \mathbf{x} =
\begin{bmatrix} x_1 & x_2 \end{bmatrix}
\begin{bmatrix} 3 & 4 \\ 4 & 6 \end{bmatrix}
\begin{bmatrix} x_1 \\ x_2 \end{bmatrix}
$$

$$
\begin{align*}
f &= 3x_1^2 + 4x_1x_2 + 4x_2x_1 + 6x_2^2 \\[4pt]
&= 3x_1^2 + 8x_1x_2 + 6x_2^2 \\[4pt]
\end{align*}
$$

{% endraw %}

以 $3 \times 3$ 的矩陣舉例：

{% raw %}

$$
A =
\begin{bmatrix}
2 & -1 & 0 \\
-1 & 2 & -1 \\
0 & -1 & 2
\end{bmatrix}
$$

$$
f = \mathbf{x}^T A \mathbf{x} =
\begin{bmatrix}
x_1 & x_2 & x_3
\end{bmatrix}
\begin{bmatrix}
2 & -1 & 0 \\
-1 & 2 & -1 \\
0 & -1 & 2
\end{bmatrix}
\begin{bmatrix}
x_1 \\
x_2 \\
x_3
\end{bmatrix}
$$

$$
\begin{align*}
f &= 2x_1^2 - x_1x_2 - x_2x_1 + \cdots + 2x_3^2  \\[4pt]
&= 2x_1^2 - 2x_1x_2 + 2x_2^2 - 2x_2x_3 + 2x_3^2 \\[4pt]
\end{align*}
$$

{% endraw %}

## 正定矩陣與半正定矩陣

以上我們了解了 **二次型** 的定義與寫法，而 **正定矩陣** 與 **半正定矩陣** 其實就是判斷 **二次型** 是 大於 0 或 大於等於 0

### 定義

#### - 正定矩陣

對於一個 $n \times n$ 的實對稱矩陣 $A$，如果對於所有非零向量 $\mathbf{x} \in \mathbb{R}^n$，都有：

$$\mathbf{x}^T A \mathbf{x} > 0$$

則稱矩陣 $A$ 為**正定矩陣**，記作 $A > 0$。

#### - 半正定矩陣

對於一個 $n \times n$ 的實對稱矩陣 $A$，如果對於所有向量 $\mathbf{x} \in \mathbb{R}^n$，都有：

$$\mathbf{x}^T A \mathbf{x} \geq 0$$

則稱矩陣 $A$ 為**半正定矩陣**，記作 $A \geq 0$。

以 $2 \times 2$ 的矩陣舉例：

{% raw %}

$$
A = \begin{bmatrix} 3 & 4 \\ 4 & 6 \end{bmatrix}
$$

$$
\begin{align*}
f &= \mathbf{x}^T A \mathbf{x} =
\begin{bmatrix} x_1 & x_2 \end{bmatrix}
\begin{bmatrix} 3 & 4 \\ 4 & 6 \end{bmatrix}
\begin{bmatrix} x_1 \\ x_2 \end{bmatrix} \\
&= 3x_1^2 + 8x_1x_2 + 6x_2^2
\end{align*}
$$

{% endraw %}

我們可以用高中數學的配方法將上式排列一下：

{% raw %}

$$
\begin{align*}
f &= 3x_1^2 + 8x_1x_2 + 6x_2^2 \\[4pt]
&= 3(x_1+\frac{4}{3}x_2)^2 + \frac{2}{3}x_2^2 > 0 \\[4pt]
\end{align*}
$$

{% endraw %}

由於 $[x_1, x_2]$ 是非零向量，上式中兩個平方的算式，平方後數值必定大於 0，可以得知整體的函數 $f$ 一定也都大於 0

=> 所以矩陣 {% raw %} $A = \begin{bmatrix} 3 & 4 \\ 4 & 6 \end{bmatrix}$ {% endraw %} 就是 **正定矩陣**

### 幾何形式

同樣以上面的式子來看，為了之後的幾何圖形比較好呈現，我們將其轉換成以 $x, y$ 進行表示：

{% raw %}

$$
\begin{align*}
f &= 3x_1^2 + 8x_1x_2 + 6x_2^2 \\[4pt]
&= 3x^2 + 8xy + 6y^2 \\[4pt]
&= 3(x+\frac{4}{3}y)^2 + \frac{2}{3}y^2
\end{align*}
$$

{% endraw %}

我們可以將這種二次函數以通用形式表示：

{% raw %}

$$
\begin{align*}
f &= ax^2 + 2bxy + cy^2 \\[4pt]
&= a\left(x + \frac{b}{a}y\right)^2 + \left(c - \frac{b^2}{a}\right)y^2 \\[4pt]
&= \begin{bmatrix} x & y \end{bmatrix} \begin{bmatrix} a & b \\ b & c \end{bmatrix} \begin{bmatrix} x \\ y \end{bmatrix} = \mathbf{x}^T A \mathbf{x}
\end{align*}
$$

{% endraw %}

以下我們考慮函數 $f$ 可能會有的幾種狀況：

#### 1. $f$ 始終為正

首先 $a$ 一定大於 0，然後 $\displaystyle c - \frac{b^2}{a}$ 也必須大於 0，由此得到的兩個條件為：

- $a > 0$
- $ac > b^2$

假設 $a=1, b=0, c=1$ 畫出來的是形似碗狀的圖，最小值在 $(0, 0)$：

<img src="quadratic_bowl_main.png" width="500">

[圖片程式碼](https://github.com/bcjohnblue/python-plot/blob/main/2025/09/quadratic_function/greater-0.py)

這種碗狀的幾何圖形對應到的即是 **正定矩陣(Positive definite matrix)**：$A > 0$

#### 2. $f$ 始終為負

首先 $a$ 一定小於 0，然後 $\displaystyle c - \frac{b^2}{a}$ 也必須小於 0，由此得到的兩個條件為：

- $a < 0$
- $ac > b^2$

假設 $a=-1, b=0, c=-1$ 畫出來的是倒過來的碗狀，最大值在 $(0, 0)$：

<img src="quadratic_inverted_bowl.png" width="500">

[圖片程式碼](https://github.com/bcjohnblue/python-plot/blob/main/2025/09/quadratic_function/lower-0.py)

這種倒過來碗狀的幾何圖形對應到的即是 [負定矩陣(Negative definite matrix)](https://zh.wikipedia.org/zh-tw/%E6%AD%A3%E5%AE%9A%E7%9F%A9%E9%98%B5#%E8%B4%9F%E5%AE%9A%E3%80%81%E5%8D%8A%E5%AE%9A%E5%8F%8A%E4%B8%8D%E5%AE%9A%E7%9F%A9%E9%98%B5)：$A < 0$

#### 3. $f$ 大於等於 0

首先 $a$ 也大於等於 0，然後 $\displaystyle c - \frac{b^2}{a}$ 也必須大於等於 0，由此得到的兩個條件為：

- $a \geq 0$
- $ac \geq b^2$

假設 $a=0, b=0, c=1$ 畫出來的是形似彩帶的圖，最小值位在 y 軸等於 0 的地方：

<img src="quadratic_bowl_semidefinite.png" width="500">

[圖片程式碼](https://github.com/bcjohnblue/python-plot/blob/main/2025/09/quadratic_function/greater-equal-0.py)

這種彩帶狀的幾何圖形對應到的即是 **半正定矩陣(Positive semi-definite matrix)**：$A \geq 0$

#### 4. $f$ 小於等於 0

首先 $a$ 也小於等於 0，然後 $\displaystyle c - \frac{b^2}{a}$ 也必須小於等於 0，由此得到的兩個條件為：

- $a \leq 0$
- $ac \geq b^2$

假設 $a=0, b=0, c=-1$ 畫出來的是形似拱橋的圖，最大值位在 y 軸等於 0 的地方：

<img src="quadratic_inverted_bowl_semidefinite.png" width="500">

[圖片程式碼](https://github.com/bcjohnblue/python-plot/blob/main/2025/09/quadratic_function/lower-equal-0.py)

這種拱橋的幾何圖形對應到的即是 [半負定矩陣(Negative semi-definite matrix)](https://zh.wikipedia.org/zh-tw/%E6%AD%A3%E5%AE%9A%E7%9F%A9%E9%98%B5#%E8%B4%9F%E5%AE%9A%E3%80%81%E5%8D%8A%E5%AE%9A%E5%8F%8A%E4%B8%8D%E5%AE%9A%E7%9F%A9%E9%98%B5)：$A \leq 0$

#### 5. 其他狀況

最後是 a 不一定是正或負值，然後 $ac < b^2$ 的狀況：

- $ac < b^2$

假設 $a=1, b=2, c=1$ 畫出來的圖案形似馬鞍，沒有全域最小值也沒有全域最大值：

<img src="quadratic_saddle.gif" width="500">

[圖片程式碼](https://github.com/bcjohnblue/python-plot/blob/main/2025/09/quadratic_function/ambiguous.py)

這種馬鞍的幾何圖形對應到的即是 [不定矩陣(Indefinite matrix)](https://zh.wikipedia.org/zh-tw/%E6%AD%A3%E5%AE%9A%E7%9F%A9%E9%98%B5#%E8%B4%9F%E5%AE%9A%E3%80%81%E5%8D%8A%E5%AE%9A%E5%8F%8A%E4%B8%8D%E5%AE%9A%E7%9F%A9%E9%98%B5)

### 小結

目前為止我們藉由研究 $2 \times 2$ 的矩陣了解正定矩陣、半正定矩陣、不定矩陣等，他們對應的幾何形狀。在實務上我們常需要判斷一個 $n \times n$ 矩陣是否為正定 or 半正定矩陣，$2 \times 2$ 矩陣可以用上面提到的配方法輕鬆的檢查出 $A$ 是哪一種矩陣，但如果是三維、四維甚至 n 維矩陣時就不可能用這種方式了，還好已經有人統整出矩陣的特性，讓我們可以用其他的方式判定某個 $n \times n$ 維矩陣是否為正定矩陣，下面我們來學習這些特性

---

### 正定矩陣的等價條件

給定對稱矩陣 $A$ 我們可以判斷其是否為正定矩陣，只要其符合下列五項中的其中一項：

1. **原始定義**：$\mathbf{x}^T A \mathbf{x} > 0$ 對所有非零向量 $\mathbf{x}$ 成立（正定矩陣的定義）

2. **特徵值條件**：矩陣 $A$ 的所有特徵值皆大於 $0$

3. **順序主子式條件**：矩陣 $A$ 的所有[左上角順序主子式](/2025/10/19/子式-minor/#順序主子式) $A_k$ 的行列式都為正數

4. **軸元條件**：矩陣 $A$ 的所有[軸元](/2025/09/24/矩陣中的軸元與秩)都大於 $0$

5. **分解條件**：存在行向量線性獨立的矩陣 $R$，使得 $A = R^T R$

#### 證明 1. 原始定義：$\mathbf{x}^T A \mathbf{x} > 0$

正定矩陣的定義，不需特別證明

<br />

#### 證明 2. 特徵值條件：矩陣 $A$ 的所有特徵值皆大於 $0$

我們需要分兩點來證明這件事：

1. 當 $\mathbf{x}^T A \mathbf{x} > 0$ 成立時，特徵值都會大於 0
2. 反過來說當特徵值都大於 0 時，$\mathbf{x}^T A \mathbf{x} > 0$ 成立

=> 當這兩點都成立時，代表 **1. 原始定義** 跟 **2. 特徵值條件** 只要其一符合就必定是正定矩陣

##### 證明 2-1. $\mathbf{x}^T A \mathbf{x} > 0$ 成立時，特徵值都會大於 0

**步驟 1：正定矩陣的定義**

假設矩陣 $A$ 是 $n \times n$ 的對稱矩陣，且正定：

$$
\mathbf{x}^T A \mathbf{x} > 0
$$

**步驟 2：考慮一個特徵向量**

設 $\mathbf{v}$ 是 $A$ 的一個特徵向量，對應特徵值 $\lambda$：

$$
A\mathbf{v} = \lambda \mathbf{v}, \quad \mathbf{v} \neq 0
$$

**步驟 3：代入二次型**

我們需要從 **步驟 1** 推導到 **步驟 2**，所以首先將步驟 1 的 $\mathbf{x}$ 以 $\mathbf{v}$ 代替：

$$
\mathbf{v}^T A \mathbf{v} = \mathbf{v}^T (\lambda \mathbf{v})
= \lambda (\mathbf{v}^T \mathbf{v})
= \lambda ||\mathbf{v}||^2
$$

**步驟 4：應用正定條件**

因為 $A$ 是正定矩陣，所以：

$$
\mathbf{v}^T A \mathbf{v} > 0
$$

由 **步驟 3** 可推斷出：

$$
\lambda ||\mathbf{v}||^2 > 0
$$

=> 得證 $\mathbf{x}^T A \mathbf{x} > 0$ 成立時，特徵值都會大於 0

##### 證明 2-2. 當特徵值都大於 0 時，$\mathbf{x}^T A \mathbf{x} > 0$ 成立

**步驟 1：特徵向量的線性組合**

假設矩陣 $A$ 是 $n \times n$ 的對稱矩陣，且所有特徵值都大於 0。由於 $A$ 是對稱矩陣，$A$ 有 $n$ 個線性無關的特徵向量 $\mathbf{v}_1, \mathbf{v}_2, \ldots, \mathbf{v}_n$，對應特徵值 $\lambda_1, \lambda_2, \ldots, \lambda_n > 0$

對於任意非零向量 $\mathbf{x}$，可以表示為這些特徵向量的線性組合：

$$\mathbf{x} = c_1\mathbf{v}_1 + c_2\mathbf{v}_2 + \cdots + c_n\mathbf{v}_n$$

其中 $c_1, c_2, \ldots, c_n$ 是常數

為什麼任意非零向量 $\mathbf{x}$，可以用特徵向量的線性組合表示呢？原因是 [對稱矩陣的特徵向量互相正交](/2025/10/15/對稱矩陣/#3-2-正交特徵向量)，因此能夠構築出 n 維空間中的任意向量，以三維空間來說就像 [範例 3：三維空間的 span](/2025/10/13/線性生成空間-span/#範例-3：三維空間的-span) 中的狀況，只要三條互相正交的特徵向量即可生成整個空間中的任意向量

**步驟 2：計算二次型**

將 $\mathbf{x}$ 代入二次型：

$$\mathbf{x}^T A \mathbf{x} = (c_1\mathbf{v}_1 + c_2\mathbf{v}_2 + \cdots + c_n\mathbf{v}_n)^T A (c_1\mathbf{v}_1 + c_2\mathbf{v}_2 + \cdots + c_n\mathbf{v}_n)$$

**步驟 3：展開並利用特徵值性質**

由於 $A\mathbf{v}_i = \lambda_i \mathbf{v}_i$，我們有：

$$\mathbf{x}^T A \mathbf{x} = (c_1\mathbf{v}_1 + c_2\mathbf{v}_2 + \cdots + c_n\mathbf{v}_n)^T (c_1\lambda_1\mathbf{v}_1 + c_2\lambda_2\mathbf{v}_2 + \cdots + c_n\lambda_n\mathbf{v}_n)$$

**步驟 4：利用特徵向量的正交性**

由於對稱矩陣的特徵向量是正交的，即 $\mathbf{v}_i^T \mathbf{v}_j = 0$ 當 $i \neq j$，且 {% raw %} $\mathbf{v}_i^T \mathbf{v}_i = \|\mathbf{v}_i\|^2$ {% endraw %}，我們得到：

{% raw %}

$$\mathbf{x}^T A \mathbf{x} = c_1^2\lambda_1\|\mathbf{v}_1\|^2 + c_2^2\lambda_2\|\mathbf{v}_2\|^2 + \cdots + c_n^2\lambda_n\|\mathbf{v}_n\|^2$$

{% endraw %}

**步驟 5：應用正定條件**

由於：

- 對稱矩陣所有特徵值 $\lambda_i > 0$ ([對稱矩陣實特徵值](/2025/10/15/對稱矩陣/#3-1-實特徵值))
- 所有 {% raw %} $\|\mathbf{v}_i\|^2 > 0$ {% endraw %}（因為特徵向量非零）
- 至少有一個 $c_i \neq 0$（因為 $\mathbf{x}$ 非零）

因此：

{% raw %}

$$\mathbf{x}^T A \mathbf{x} = \sum_{i=1}^n c_i^2\lambda_i\|\mathbf{v}_i\|^2 > 0$$

{% endraw %}

=> 得證當特徵值都大於 0 時，$\mathbf{x}^T A \mathbf{x} > 0$ 成立

<br />

#### 證明 3. 順序主子式條件：矩陣 $A$ 的所有左上角順序主子式 $A_k$ 的行列式都為正數

**步驟 1：定義順序主子式**

對於 $n \times n$ 對稱矩陣 $A$，其 $k$ 階[左上角順序主子式](/2025/10/19/子式-minor/#順序主子式)定義為：

{% raw %}

$$
A_k = \begin{bmatrix}
a_{11} & a_{12} & \cdots & a_{1k} \\
a_{21} & a_{22} & \cdots & a_{2k} \\
\vdots & \vdots & \ddots & \vdots \\
a_{k1} & a_{k2} & \cdots & a_{kk}
\end{bmatrix}, \quad k = 1, 2, \ldots, n
$$

{% endraw %}

**步驟 2：定義前 k 個分量有值的向量**

由於 $A$ 是正定矩陣，對於任意向量 $x$ 都符合 $\mathbf{x}^T A \mathbf{x} > 0$，所以我們可以技巧性的選擇一個特別的向量：

{% raw %}

$$
\mathbf{x} = \begin{bmatrix} x_1 & x_2 & \cdots & x_k & 0 & \cdots & 0 \end{bmatrix}^T \in \mathbb{R}^n
$$

{% endraw %}

這個向量的前 $k$ 個分量是任意值，而後面的 $n-k$ 個分量為 0

**步驟 3：計算二次型**

對於非零向量 $\mathbf{x}$，由於 $A$ 正定：

{% raw %}

$$
\mathbf{x}^T A \mathbf{x} = \begin{bmatrix} x_1 & x_2 & \cdots & x_k & 0 & \cdots & 0 \end{bmatrix}
\begin{bmatrix}
A_k & B \\
B^T & C
\end{bmatrix}
\begin{bmatrix}
x_1 \\ x_2 \\ \vdots \\ x_k \\ 0 \\ \vdots \\ 0
\end{bmatrix}
$$

{% endraw %}

其中矩陣 $A$ 被分塊為：$A_k$ 是左上角 $k \times k$ 子矩陣，$B$ 是右上角部分，$C$ 是右下角部分

P.S. 參考 [補充資料 1. 矩陣分塊](./#1-矩陣分塊)

**步驟 4：簡化計算**

由於後 $n-k$ 個分量為 0，我們得到：

{% raw %}

$$
\mathbf{x}^T A \mathbf{x} = \begin{bmatrix} x_1 & x_2 & \cdots & x_k \end{bmatrix} A_k \begin{bmatrix} x_1 \\ x_2 \\ \vdots \\ x_k \end{bmatrix} > 0
$$

{% endraw %}

P.S. 參考 [補充資料 2. 範例：矩陣分塊後面分量為 0 的計算](./#2-範例：矩陣分塊後面分量為-0-的計算)

**步驟 5：結論**

這表明 **$A_k$ 本身也是正定矩陣**。上面條件 2. 得到 **正定矩陣的所有特徵值皆大於 0**，因此這些特徵值的乘積也都大於 0：

$$
\lambda_1 \lambda_2 \cdots \lambda_k > 0
$$

再加上 [補充資料 3. 矩陣特徵值乘積等於行列式值](./#3-矩陣特徵值乘積等於行列式值) 的推導，可得：

$$
\det(A_k) = \lambda_1 \lambda_2 \cdots \lambda_k > 0
$$

對所有 $k = 1, 2, \ldots, n$ 成立

=> 得證正定矩陣的所有左上角順序主子式的行列式都為正數

<br />

#### 證明 4. 軸元條件：矩陣 $A$ 的所有軸元都大於 $0$

**步驟 1：軸元與主子式的關係**

根據 [LU 分解與軸元的關係](/2025/10/20/lu-分解/#LU-分解與軸元的關係)，矩陣 $A$ 的第 $k$ 個軸元 $u_{kk}$ 可以表示為：

$$u_{kk} = \frac{\det(A^{(k)})}{\det(A^{(k-1)})}$$

其中 $A^{(k)}$ 表示 $A$ 的前 $k$ 行前 $k$ 列組成的子矩陣，也就是我們在 **證明 3** 中定義的左上角順序主子式 $A_k$。因此上述公式可以寫為：

$$u_{kk} = \frac{\det(A_k)}{\det(A_{k-1})}$$

(約定 $\det(A_0) = 1$)

**步驟 2：應用順序主子式條件**

根據 **證明 3**，正定矩陣 $A$ 的所有左上角順序主子式的行列式都為正數，即：

$$
\det(A_k) = \lambda_1 \lambda_2 \cdots \lambda_k > 0
$$

對所有 $k = 1, 2, \ldots, n$ 成立

**步驟 3：推導軸元為正**

由於 $\det(A_k) > 0$ 且 $\det(A_{k-1}) > 0$（對於 $k \geq 2$），我們得到：

$$u_{kk} = \frac{\det(A_k)}{\det(A_{k-1})} > 0$$

對於 $k = 1$，有：

$$u_{11} = \frac{\det(A_1)}{\det(A_0)} = \frac{\det(A_1)}{1} = \det(A_1) > 0$$

=> 得證正定矩陣的所有軸元 $u_{kk} > 0$

<br />

#### 證明 5. 分解條件：存在行向量線性獨立的矩陣 $R$，使得 $A = R^T R$

**步驟 1：考慮二次型**

對於任意非零向量 $\mathbf{x}$，考慮二次型：

{% raw %}

$$\mathbf{x}^T A \mathbf{x} = \mathbf{x}^T R^T R \mathbf{x}$$

{% endraw %}

套用 [矩陣乘法的轉置](/2025/09/27/轉置矩陣/#4-矩陣乘法的轉置)：

{% raw %}

$$\mathbf{x}^T R^T R \mathbf{x} = (R\mathbf{x})^T (R\mathbf{x})$$

{% endraw %}

套用 [向量內積與範數的關係](/2025/10/18/向量內積/#1-與範數的關係)：

{% raw %}

$$(R\mathbf{x})^T (R\mathbf{x}) = \|R\mathbf{x}\|^2$$

{% endraw %}

**步驟 2：結論**

對所有非零向量 $\mathbf{x}$，都有：

{% raw %}

$$\mathbf{x}^T A \mathbf{x} = \|R\mathbf{x}\|^2 > 0$$

{% endraw %}

=> 得證存在矩陣 $R$ 使得 $A = R^T R$ 時，矩陣 $A$ 是正定矩陣

---

### 範例：驗證正定矩陣的五個條件

我們以矩陣 {% raw %} $A = \begin{bmatrix} 3 & 1 \\ 1 & 2 \end{bmatrix}$ {% endraw %} 為例，逐一驗證這五個條件：

#### 條件 1：原始定義 $\mathbf{x}^T A \mathbf{x} > 0$

對於任意非零向量 {% raw %} $\mathbf{x} = \begin{bmatrix} x_1 \\ x_2 \end{bmatrix}$ {% endraw %}：

{% raw %}

$$
\begin{align*}
\mathbf{x}^T A \mathbf{x} &= \begin{bmatrix} x_1 & x_2 \end{bmatrix} \begin{bmatrix} 3 & 1 \\ 1 & 2 \end{bmatrix} \begin{bmatrix} x_1 \\ x_2 \end{bmatrix} \\[4pt]
&= \begin{bmatrix} x_1 & x_2 \end{bmatrix} \begin{bmatrix} 3x_1 + x_2 \\ x_1 + 2x_2 \end{bmatrix} \\[4pt]
&= 3x_1^2 + x_1x_2 + x_2x_1 + 2x_2^2 \\[4pt]
&= 3x_1^2 + 2x_1x_2 + 2x_2^2
\end{align*}
$$

{% endraw %}

使用配方法：

{% raw %}

$$
\begin{align*}
3x_1^2 + 2x_1x_2 + 2x_2^2 &= 3\left(x_1^2 + \frac{2}{3}x_1x_2\right) + 2x_2^2 \\[4pt]
&= 3\left[\left(x_1 + \frac{1}{3}x_2\right)^2 - \frac{1}{9}x_2^2\right] + 2x_2^2 \\[4pt]
&= 3\left(x_1 + \frac{1}{3}x_2\right)^2 - \frac{1}{3}x_2^2 + 2x_2^2 \\[4pt]
&= 3\left(x_1 + \frac{1}{3}x_2\right)^2 + \frac{5}{3}x_2^2 > 0
\end{align*}
$$

{% endraw %}

當 $\mathbf{x} \neq \mathbf{0}$ 時，上式恆大於 0，因此條件 1 成立

#### 條件 2：特徵值條件 $\lambda_i > 0$

計算特徵值：

{% raw %}

$$
\det(A - \lambda I) = \det\begin{bmatrix} 3-\lambda & 1 \\ 1 & 2-\lambda \end{bmatrix} = (3-\lambda)(2-\lambda) - 1 = \lambda^2 - 5\lambda + 5
$$

{% endraw %}

解特徵方程 $\lambda^2 - 5\lambda + 5 = 0$：

$$
\lambda = \frac{5 \pm \sqrt{25-20}}{2} = \frac{5 \pm \sqrt{5}}{2}
$$

計算數值：

- $\lambda_1 = \frac{5 + \sqrt{5}}{2} \approx \frac{5 + 2.236}{2} = 3.618 > 0$
- $\lambda_2 = \frac{5 - \sqrt{5}}{2} \approx \frac{5 - 2.236}{2} = 1.382 > 0$

兩個特徵值都大於 0，因此條件 2 成立

#### 條件 3：順序主子式條件 $\det(A_k) > 0$

計算所有左上角順序主子式：

**1 階主子式**：

$$
\det(A_1) = \det[3] = 3 > 0
$$

**2 階主子式**：

{% raw %}

$$
\det(A_2) = \det\begin{bmatrix} 3 & 1 \\ 1 & 2 \end{bmatrix} = 3 \times 2 - 1 \times 1 = 6 - 1 = 5 > 0
$$

{% endraw %}

所有主子式都大於 0，因此條件 3 成立

#### 條件 4：軸元條件 $u_{kk} > 0$

使用高斯消去法求軸元：

{% raw %}

$$
\begin{bmatrix} 3 & 1 \\ 1 & 2 \end{bmatrix} \xrightarrow{R_2 - \frac{1}{3}R_1} \begin{bmatrix} 3 & 1 \\ 0 & \frac{5}{3} \end{bmatrix}
$$

{% endraw %}

軸元為：$d_1 = 3 > 0$，$d_2 = \frac{5}{3} > 0$

所有軸元都大於 0，因此條件 4 成立

#### 條件 5：分解條件 $A = R^T R$

我們需要找到矩陣 $R$ 使得 $A = R^T R$

設 {% raw %} $R = \begin{bmatrix} a & b \\ 0 & c \end{bmatrix}$ {% endraw %}，則：

{% raw %}

$$
R^T R = \begin{bmatrix} a & 0 \\ b & c \end{bmatrix} \begin{bmatrix} a & b \\ 0 & c \end{bmatrix} = \begin{bmatrix} a^2 & ab \\ ab & b^2 + c^2 \end{bmatrix}
$$

{% endraw %}

令其等於 $A$：

{% raw %}

$$
\begin{bmatrix} a^2 & ab \\ ab & b^2 + c^2 \end{bmatrix} = \begin{bmatrix} 3 & 1 \\ 1 & 2 \end{bmatrix}
$$

{% endraw %}

解得：

- $a^2 = 3 \Rightarrow a = \sqrt{3}$
- $ab = 1 \Rightarrow b = \frac{1}{\sqrt{3}}$
- $b^2 + c^2 = 2 \Rightarrow c^2 = 2 - \frac{1}{3} = \frac{5}{3} \Rightarrow c = \sqrt{\frac{5}{3}}$

因此：

{% raw %}

$$
R = \begin{bmatrix} \sqrt{3} & \frac{1}{\sqrt{3}} \\ 0 & \sqrt{\frac{5}{3}} \end{bmatrix}
$$

{% endraw %}

驗證：

{% raw %}

$$
R^T R = \begin{bmatrix} \sqrt{3} & 0 \\ \frac{1}{\sqrt{3}} & \sqrt{\frac{5}{3}} \end{bmatrix} \begin{bmatrix} \sqrt{3} & \frac{1}{\sqrt{3}} \\ 0 & \sqrt{\frac{5}{3}} \end{bmatrix} = \begin{bmatrix} 3 & 1 \\ 1 & 2 \end{bmatrix} = A
$$

{% endraw %}

矩陣 $R$ 的行向量線性獨立（因為 $R$ 是上三角矩陣且對角元素非零），因此條件 5 成立

#### 範例 - 小結

矩陣 {% raw %} $A = \begin{bmatrix} 3 & 1 \\ 1 & 2 \end{bmatrix}$ {% endraw %} 滿足所有五個正定矩陣的等價條件，因此它是一個正定矩陣。這五個條件相互等價，只要滿足其中一個，就必然滿足其他四個條件

---

### 半正定矩陣的等價條件

如同正定矩陣的五個等價條件，半正定矩陣也有以下五項等價條件：

1. **原始定義**：$\mathbf{x}^T A \mathbf{x} \geq 0$ 對所有向量 $\mathbf{x}$ 成立（半正定矩陣的定義）

2. **特徵值條件**：矩陣 $A$ 的所有特徵值皆大於等於 $0$

3. **順序主子式條件**：矩陣 $A$ 的所有[左上角順序主子式](/2025/10/19/子式-minor/#順序主子式) $A_k$ 的行列式都大於等於 $0$

4. **軸元條件**：矩陣 $A$ 的所有[軸元](/2025/09/24/矩陣中的軸元與秩)都大於等於 $0$

5. **分解條件**：存在行向量可能線性相關的矩陣 $R$，使得 $A = R^T R$

### 範例：驗證半正定矩陣的五個條件

我們以矩陣 {% raw %} $A = \begin{bmatrix} 1 & 1 \\ 1 & 1 \end{bmatrix}$ {% endraw %} 為例，逐一驗證這五個條件：

#### 條件 1：原始定義 $\mathbf{x}^T A \mathbf{x} \geq 0$

對於任意向量 {% raw %} $\mathbf{x} = \begin{bmatrix} x_1 \\ x_2 \end{bmatrix}$ {% endraw %}：

{% raw %}

$$
\begin{align*}
\mathbf{x}^T A \mathbf{x} &= \begin{bmatrix} x_1 & x_2 \end{bmatrix} \begin{bmatrix} 1 & 1 \\ 1 & 1 \end{bmatrix} \begin{bmatrix} x_1 \\ x_2 \end{bmatrix} \\[4pt]
&= \begin{bmatrix} x_1 & x_2 \end{bmatrix} \begin{bmatrix} x_1 + x_2 \\ x_1 + x_2 \end{bmatrix} \\[4pt]
&= x_1(x_1 + x_2) + x_2(x_1 + x_2) \\[4pt]
&= x_1^2 + x_1x_2 + x_2x_1 + x_2^2 \\[4pt]
&= x_1^2 + 2x_1x_2 + x_2^2
\end{align*}
$$

{% endraw %}

使用配方法：

{% raw %}

$$
\begin{align*}
x_1^2 + 2x_1x_2 + x_2^2 &= (x_1 + x_2)^2 \geq 0
\end{align*}
$$

{% endraw %}

對所有向量 $\mathbf{x}$，上式恆大於等於 0，因此條件 1 成立

#### 條件 2：特徵值條件 $\lambda_i \geq 0$

計算特徵值：

{% raw %}

$$
\det(A - \lambda I) = \det\begin{bmatrix} 1-\lambda & 1 \\ 1 & 1-\lambda \end{bmatrix} = (1-\lambda)^2 - 1 = \lambda^2 - 2\lambda = \lambda(\lambda - 2)
$$

{% endraw %}

解特徵方程 $\lambda(\lambda - 2) = 0$：

- $\lambda_1 = 0 \geq 0$
- $\lambda_2 = 2 \geq 0$

兩個特徵值都大於等於 0，因此條件 2 成立

#### 條件 3：順序主子式條件 $\det(A_k) \geq 0$

計算所有左上角順序主子式：

**1 階主子式**：

$$
\det(A_1) = \det[1] = 1 \geq 0
$$

**2 階主子式**：

{% raw %}

$$
\det(A_2) = \det\begin{bmatrix} 1 & 1 \\ 1 & 1 \end{bmatrix} = 1 \times 1 - 1 \times 1 = 1 - 1 = 0 \geq 0
$$

{% endraw %}

所有主子式都大於等於 0，因此條件 3 成立

#### 條件 4：軸元條件 $u_{kk} \geq 0$

使用高斯消去法求軸元：

{% raw %}

$$
\begin{bmatrix} 1 & 1 \\ 1 & 1 \end{bmatrix} \xrightarrow{R_2 - R_1} \begin{bmatrix} 1 & 1 \\ 0 & 0 \end{bmatrix}
$$

{% endraw %}

軸元為：$d_1 = 1 \geq 0$，$d_2 = 0 \geq 0$

所有軸元都大於等於 0，因此條件 4 成立

#### 條件 5：分解條件 $A = R^T R$

我們需要找到矩陣 $R$ 使得 $A = R^T R$

設 {% raw %} $R = \begin{bmatrix} a & b \end{bmatrix}$ {% endraw %}（一個 $1 \times 2$ 矩陣），則：

{% raw %}

$$
R^T R = \begin{bmatrix} a \\ b \end{bmatrix} \begin{bmatrix} a & b \end{bmatrix} = \begin{bmatrix} a^2 & ab \\ ab & b^2 \end{bmatrix}
$$

{% endraw %}

令其等於 $A$：

{% raw %}

$$
\begin{bmatrix} a^2 & ab \\ ab & b^2 \end{bmatrix} = \begin{bmatrix} 1 & 1 \\ 1 & 1 \end{bmatrix}
$$

{% endraw %}

解得：

- $a^2 = 1 \Rightarrow a = 1$（取正值）
- $ab = 1 \Rightarrow b = 1$
- $b^2 = 1 \Rightarrow b = \pm 1$，取 $b = 1$

因此：

{% raw %}

$$
R = \begin{bmatrix} 1 & 1 \end{bmatrix}
$$

{% endraw %}

驗證：

{% raw %}

$$
R^T R = \begin{bmatrix} 1 \\ 1 \end{bmatrix} \begin{bmatrix} 1 & 1 \end{bmatrix} = \begin{bmatrix} 1 & 1 \\ 1 & 1 \end{bmatrix} = A
$$

{% endraw %}

矩陣 $R$ 的行向量線性相關（因為 $R$ 是 $1 \times 2$ 矩陣，只有一行），因此條件 5 成立

#### 範例 - 小結

矩陣 {% raw %} $A = \begin{bmatrix} 1 & 1 \\ 1 & 1 \end{bmatrix}$ {% endraw %} 滿足所有五個半正定矩陣的等價條件，因此它是一個半正定矩陣。注意到這個矩陣有一個特徵值為 0，且行列式為 0，因此它不是正定矩陣，但符合半正定矩陣的條件。這五個條件相互等價，只要滿足其中一個，就必然滿足其他四個條件

---

## 補充資料

### 1. 矩陣分塊

假設：

{% raw %}

$$
A =
\begin{bmatrix}
1 & 2 & 3 \\
2 & 5 & 6 \\
3 & 6 & 9
\end{bmatrix}
$$

{% endraw %}

這個矩陣是對稱矩陣，因為上三角與下三角相同

我們將它分塊成：

{% raw %}

$$
A =
\begin{bmatrix}
A_k & B \\
B^T & C
\end{bmatrix}
=
\begin{bmatrix}
\begin{bmatrix}1 & 2\\2 & 5\end{bmatrix} &
\begin{bmatrix}3\\6\end{bmatrix} \\
\begin{bmatrix}3 & 6\end{bmatrix} &
[9]
\end{bmatrix}
$$

{% endraw %}

這裡的

{% raw %}

$$
B = \begin{bmatrix}3\\6\end{bmatrix}
$$

{% endraw %}

而左下角是它的轉置：

{% raw %}

$$
B^T = [3 \ 6]
$$

{% endraw %}

因此整個對稱矩陣可以表示為：

{% raw %}

$$
A =
\begin{bmatrix}
A_k & B \\
B^T & C
\end{bmatrix}, \quad A^T = A
$$

{% endraw %}

### 2. 範例：矩陣分塊後面分量為 0 的計算

假設我們有一個 $3 \times 3$ 的對稱矩陣 $A$，分塊為：

{% raw %}

$$
A = \begin{bmatrix}
A_2 & B \\
B^T & C
\end{bmatrix}
=
\begin{bmatrix}
2 & 1 & 3 \\
1 & 3 & 2 \\
3 & 2 & 5
\end{bmatrix}
=
\begin{bmatrix}
\begin{bmatrix}2 & 1\\1 & 3\end{bmatrix} & \begin{bmatrix}3\\2\end{bmatrix} \\
\begin{bmatrix}3 & 2\end{bmatrix} & [5]
\end{bmatrix}
$$

{% endraw %}

其中：

- $A_2$ 是左上角 $2 \times 2$ 子矩陣
- $B$ 是右上角的向量
- $C$ 是右下角的向量

現在考慮向量 {% raw %} $\mathbf{x} = \begin{bmatrix} x_1 \\ x_2 \\ 0 \end{bmatrix}$ {% endraw %}，最後一個分量為 0

完整計算（不簡化）：

{% raw %}

$$
\begin{align*}
\mathbf{x}^T A \mathbf{x} &= \begin{bmatrix} x_1 & x_2 & 0 \end{bmatrix}
\begin{bmatrix}
2 & 1 & 3 \\
1 & 3 & 2 \\
3 & 2 & 5
\end{bmatrix}
\begin{bmatrix}
x_1 \\ x_2 \\ 0
\end{bmatrix} \\[8pt]
&= \begin{bmatrix} x_1 & x_2 & 0 \end{bmatrix}
\begin{bmatrix}
2x_1 + x_2 + 3 \cdot 0 \\
x_1 + 3x_2 + 2 \cdot 0 \\
3x_1 + 2x_2 + 5 \cdot 0
\end{bmatrix} \\[8pt]
&= \begin{bmatrix} x_1 & x_2 & 0 \end{bmatrix}
\begin{bmatrix}
2x_1 + x_2 \\
x_1 + 3x_2 \\
3x_1 + 2x_2
\end{bmatrix} \\[8pt]
&= x_1(2x_1 + x_2) + x_2(x_1 + 3x_2) + 0 \cdot (3x_1 + 2x_2) \\[8pt]
&= 2x_1^2 + x_1x_2 + x_2x_1 + 3x_2^2 \\[8pt]
&= 2x_1^2 + 2x_1x_2 + 3x_2^2
\end{align*}
$$

{% endraw %}

使用分塊矩陣簡化：

{% raw %}

$$
\begin{align*}
\mathbf{x}^T A \mathbf{x} &= \begin{bmatrix} x_1 & x_2 & 0 \end{bmatrix}
\begin{bmatrix}
A_2 & B \\
B^T & C
\end{bmatrix}
\begin{bmatrix}
x_1 \\ x_2 \\ 0
\end{bmatrix} \\[8pt]
&= \begin{bmatrix}
\begin{bmatrix} x_1 & x_2 \end{bmatrix} & 0
\end{bmatrix}
\begin{bmatrix}
A_2 \begin{bmatrix} x_1 \\ x_2 \end{bmatrix} + B \cdot 0 \\
B^T \begin{bmatrix} x_1 \\ x_2 \end{bmatrix} + C \cdot 0
\end{bmatrix} \\[8pt]
&= \begin{bmatrix} x_1 & x_2 \end{bmatrix} A_2 \begin{bmatrix} x_1 \\ x_2 \end{bmatrix} + 0 \cdot \left(B^T \begin{bmatrix} x_1 \\ x_2 \end{bmatrix}\right) \\[8pt]
&= \begin{bmatrix} x_1 & x_2 \end{bmatrix} A_2 \begin{bmatrix} x_1 \\ x_2 \end{bmatrix}
\end{align*}
$$

{% endraw %}

驗證簡化結果：

{% raw %}

$$
\begin{align*}
\begin{bmatrix} x_1 & x_2 \end{bmatrix} A_2 \begin{bmatrix} x_1 \\ x_2 \end{bmatrix}
&= \begin{bmatrix} x_1 & x_2 \end{bmatrix}
\begin{bmatrix}
2 & 1 \\
1 & 3
\end{bmatrix}
\begin{bmatrix}
x_1 \\ x_2
\end{bmatrix} \\[8pt]
&= \begin{bmatrix} x_1 & x_2 \end{bmatrix}
\begin{bmatrix}
2x_1 + x_2 \\
x_1 + 3x_2
\end{bmatrix} \\[8pt]
&= 2x_1^2 + 2x_1x_2 + 3x_2^2
\end{align*}
$$

{% endraw %}

結論：

兩種計算方式得到相同的結果 $2x_1^2 + 2x_1x_2 + 3x_2^2$，這證明了：

當向量 $\mathbf{x}$ 的後 $n-k$ 個分量為 0 時，二次型 $\mathbf{x}^T A \mathbf{x}$ 的計算可以簡化為只考慮左上角 $k \times k$ 子矩陣 $A_k$ 和前 $k$ 個分量：

{% raw %}

$$
\mathbf{x}^T A \mathbf{x} = \begin{bmatrix} x_1 & \cdots & x_k \end{bmatrix} A_k \begin{bmatrix} x_1 \\ \vdots \\ x_k \end{bmatrix}
$$

{% endraw %}

### 3. 矩陣特徵值乘積等於行列式值

對於 $n \times n$ 矩陣 $A$，設其特徵值為 $\lambda_1, \lambda_2, \ldots, \lambda_n$（包含重根），則：

$$
\det(A) = \lambda_1 \lambda_2 \cdots \lambda_n = \prod_{i=1}^n \lambda_i
$$

#### 證明

**步驟 1：特徵多項式**

矩陣 $A$ 的特徵多項式定義為：

$$
p(\lambda) = \det(A - \lambda I)
$$

其中 $I$ 是 $n \times n$ 單位矩陣

**步驟 2：特徵方程的根**

特徵值 $\lambda_1, \lambda_2, \ldots, \lambda_n$ 是特徵方程 $p(\lambda) = 0$ 的根，因此特徵多項式可以因式分解為：

$$
p(\lambda) = \det(A - \lambda I) = (\lambda_1 - \lambda)(\lambda_2 - \lambda) \cdots (\lambda_n - \lambda)
$$

**步驟 3：比較常數項**

特徵多項式的常數項（即 $\lambda = 0$ 時的值）為：

$$
p(0) = \det(A - 0 \cdot I) = \det(A)
$$

另一方面，將因式分解形式代入 $\lambda = 0$：

$$
p(0) = (\lambda_1 - 0)(\lambda_2 - 0) \cdots (\lambda_n - 0) = \lambda_1 \lambda_2 \cdots \lambda_n
$$

**步驟 4：結論**

因此：

$$
\det(A) = \lambda_1 \lambda_2 \cdots \lambda_n = \prod_{i=1}^n \lambda_i
$$

#### 參考資料

[方陣 A 的行列式等於其特徵值相乘](https://teshenglin.github.io/post/2023_la_det_trace_zh/)
[为何矩阵特征值乘积等于矩阵行列式值？](https://www.zhihu.com/question/304671751)

## 相關連結

[特徵向量與特徵值](/2025/09/20/特徵向量與特徵值/)
[轉置矩陣](/2025/09/27/轉置矩陣/)
[對稱矩陣](/2025/10/15/對稱矩陣/)
[向量內積](/2025/10/18/向量內積/)
[線性生成空間 - span](/2025/10/13/線性生成空間-span/)
[矩陣中的軸元與秩](/2025/09/24/矩陣中的軸元與秩/)
[子式 - minor](/2025/10/19/子式-minor/)
[LU 分解](/2025/10/20/lu-分解/)

## 參考資料

[陳晏笙老師開放課程：單元 14．特徵值與特徵向量-正定矩陣](https://youtu.be/BWhmlvI75W0?si=2qtTl9LViUGVJB3I&t=2462)
[wiki 正定矩陣](https://zh.wikipedia.org/zh-tw/%E6%AD%A3%E5%AE%9A%E7%9F%A9%E9%98%B5)
