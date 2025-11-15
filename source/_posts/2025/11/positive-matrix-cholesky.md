---
title: 正定矩陣與半正定矩陣 (3) - Cholesky 分解
date: 2025-11-16 01:18:45
updated: 2025-11-16 01:18:45
categories: 線性代數
mathjax: true
---

###### 此為 **正定矩陣與半正定矩陣** 系列文章 - 第 3 篇：

1. <a href="/2025/11/03/正定矩陣與半正定矩陣-1-定義與性質/" target="_blank">正定矩陣與半正定矩陣 (1) - 定義與性質</a>
2. <a href="/2025/11/12/正定矩陣與半正定矩陣-2-橢球體的形狀/" target="_blank">正定矩陣與半正定矩陣 (2) - 橢球體的形狀</a>
3. <a href="/2025/11/16/正定矩陣與半正定矩陣-3-cholesky-分解/" target="_blank">正定矩陣與半正定矩陣 (3) - Cholesky 分解</a>

## 前言

Cholesky 分解是線性代數中的矩陣分解方法，專門用於正定矩陣，它將一個正定矩陣分解為下三角矩陣與其轉置的乘積

## 定義

對於一個 $n \times n$ 的正定矩陣 $A$，存在唯一的下三角矩陣 $L$，使得：

$$A = LL^T$$

其中：

- $L$ 是下三角矩陣（對角線以下元素為零）
- $L^T$ 是 $L$ 的轉置矩陣
- $L$ 的對角線元素都是正數

<!-- more -->

## 計算範例

讓我們用一個具體的例子來說明 Cholesky 分解的計算過程，考慮對稱正定矩陣：

{% raw %}

$$
A = \begin{bmatrix}
4 & 2 & 1 \\
2 & 5 & 2 \\
1 & 2 & 3
\end{bmatrix}
$$

{% endraw %}

我們要找到下三角矩陣 $L$ 使得 $A = LL^T$

設 {% raw %} $L = \begin{bmatrix}
l_{11} & 0 & 0 \\
l_{21} & l_{22} & 0 \\
l_{31} & l_{32} & l_{33}
\end{bmatrix}$
{% endraw %}

根據 $A = LL^T$，我們有：

{% raw %}

$$
\begin{bmatrix}
4 & 2 & 1 \\
2 & 5 & 2 \\
1 & 2 & 3
\end{bmatrix} = \begin{bmatrix}
l_{11} & 0 & 0 \\
l_{21} & l_{22} & 0 \\
l_{31} & l_{32} & l_{33}
\end{bmatrix} \begin{bmatrix}
l_{11} & l_{21} & l_{31} \\
0 & l_{22} & l_{32} \\
0 & 0 & l_{33}
\end{bmatrix}
$$

{% endraw %}

逐個元素比較：

1. $a_{11} = 4 = l_{11}^2$，所以 $l_{11} = 2$
2. $a_{21} = 2 = l_{21} \cdot l_{11}$，所以 $l_{21} = 1$
3. $a_{31} = 1 = l_{31} \cdot l_{11}$，所以 $l_{31} = 0.5$
4. $a_{22} = 5 = l_{21}^2 + l_{22}^2$，所以 $l_{22} = 2$
5. $a_{32} = 2 = l_{31} \cdot l_{21} + l_{32} \cdot l_{22}$，所以 $l_{32} = 0.75$
6. $a_{33} = 3 = l_{31}^2 + l_{32}^2 + l_{33}^2$，所以 $l_{33} = 1.5$

因此：

{% raw %}

$$
L = \begin{bmatrix}
2 & 0 & 0 \\
1 & 2 & 0 \\
0.5 & 0.75 & 1.5
\end{bmatrix}
$$

{% endraw %}

## 存在性與唯一性證明

### 存在性證明

**定理**：每個對稱正定矩陣都有 Cholesky 分解

**證明**：使用數學歸納法

**基礎情況**：對於 $1 \times 1$ 矩陣 $A = [a]$，如果 $a > 0$，則 $L = [\sqrt{a}]$ 滿足 $A = LL^T$

**歸納步驟**：假設對於 $(n-1) \times (n-1)$ 對稱正定矩陣，Cholesky 分解存在

對於 $n \times n$ 對稱正定矩陣 $A$，我們可以將其分塊為：

{% raw %}

$$
A = \begin{bmatrix}
\tilde{A} & a \\
a^T & \alpha
\end{bmatrix}
$$

{% endraw %}

其中 $\tilde{A} \in \mathbb{R}^{(n-1) \times (n-1)}$，$a \in \mathbb{R}^{n-1}$，$\alpha \in \mathbb{R}$。

因為 <a href="/2025/11/03/正定矩陣與半正定矩陣-1-定義與性質/#證明-3-順序主子式條件：矩陣-A-的所有左上角順序主子式-A-k-的行列式都為正數" style="display: inline; vertical-align: initial" target="_blank">正定矩陣的順序主子式矩陣也是正定的</a>，所以 {% raw %} $\tilde{A}$ {% endraw %} 是正定矩陣，根據歸納假設，$\tilde{A}$ 有 Cholesky 分解 $\tilde{A} = \tilde{L}\tilde{L}^T$

考慮以下 [合同變換](./#2-合同變換-Congruence-Transformation)：

{% raw %}

$$
L_1^{-1}AL_1^{-T} := \begin{bmatrix}
\tilde{L}^{-1} & 0 \\
0 & 1
\end{bmatrix} \begin{bmatrix}
\tilde{A} & a \\
a^T & \alpha
\end{bmatrix} \begin{bmatrix}
\tilde{L}^{-T} & 0 \\
0 & 1
\end{bmatrix} = \begin{bmatrix}
I & b \\
b^T & \alpha
\end{bmatrix}
$$

{% endraw %}

其中 $b := \tilde{L}^{-1}a$

接下來消除 $b$：

{% raw %}

$$
L_2^{-1}BL_2^{-T} := \begin{bmatrix}
I & 0 \\
-b^T & 1
\end{bmatrix} \begin{bmatrix}
I & b \\
b^T & \alpha
\end{bmatrix} \begin{bmatrix}
I & -b \\
0 & 1
\end{bmatrix} = \begin{bmatrix}
I & 0 \\
0 & \alpha - b^Tb
\end{bmatrix}
$$

{% endraw %}

右側的對角矩陣是對 $A$ 進行合同變換的結果，因此是正定的。現在我們詳細計算對角線上的元素 $\alpha - b^Tb$：

**步驟 1**：展開 $b^Tb$

由於 $b := \tilde{L}^{-1}a$，我們有：

$$b^Tb = (\tilde{L}^{-1}a)^T(\tilde{L}^{-1}a) = a^T(\tilde{L}^{-1})^T\tilde{L}^{-1}a = a^T(\tilde{L}\tilde{L}^T)^{-1}a$$

因為 $\tilde{A} = \tilde{L}\tilde{L}^T$，所以 $(\tilde{L}\tilde{L}^T)^{-1} = \tilde{A}^{-1}$，因此：

$$b^Tb = a^T\tilde{A}^{-1}a$$

**步驟 2**：證明 $\alpha - b^Tb > 0$

因為合同變換保持正定性，而右側的對角矩陣：

{% raw %}

$$
\begin{bmatrix}
I & 0 \\
0 & \alpha - b^Tb
\end{bmatrix}
$$

{% endraw %}

是正定的，所以其 [對角線元素必須都為正數](/2025/11/03/正定矩陣與半正定矩陣-1-定義與性質/#證明-4-軸元條件：矩陣-A-的所有軸元都大於-0) (即軸元都大於 0)，於是：

$$\alpha - b^Tb > 0$$

**步驟 3**：定義 $\lambda$

因為 $\alpha - b^Tb > 0$，我們可以定義：

$$\lambda := \sqrt{\alpha - b^Tb} = \sqrt{\alpha - a^T\tilde{A}^{-1}a}$$

其中 $\lambda \in \mathbb{R}$ 且 $\lambda > 0$，因此：

$$0 < \alpha - b^Tb = \alpha - a^T\tilde{A}^{-1}a = \lambda^2$$

設 {% raw %} $L_3 := \begin{bmatrix}
I & 0 \\
0 & \lambda
\end{bmatrix}$
{% endraw %} ，則 $L_2^{-1}BL_2^{-T} = L_3L_3^T$

從上述變換可得：

$$L_2^{-1}L_1^{-1}AL_1^{-T}L_2^{-T} = L_3L_3^T$$

**反向推導**：為了得到 $A$ 的 Cholesky 分解，我們需要反向操作。兩邊同時左乘 $L_1L_2$ 並右乘 $L_2^TL_1^T$：

$$A = (L_1L_2) L_3L_3^T (L_2^TL_1^T) = (L_1L_2L_3)(L_1L_2L_3)^T$$

定義 $L := L_1L_2L_3$，則 $A = LL^T$，這就是 Cholesky 分解！

<br />

**構造 $L$ 矩陣**：現在我們詳細計算 $L = L_1L_2L_3$

回顧三個變換矩陣的定義：

{% raw %}

$$
L_1 = \begin{bmatrix}
\tilde{L} & 0 \\
0 & 1
\end{bmatrix}, \quad
L_2 = \begin{bmatrix}
I & 0 \\
b^T & 1
\end{bmatrix}, \quad
L_3 = \begin{bmatrix}
I & 0 \\
0 & \lambda
\end{bmatrix}
$$

{% endraw %}

**步驟 1**：先計算 $L_2L_3$

{% raw %}

$$
L_2L_3 = \begin{bmatrix}
I & 0 \\
b^T & 1
\end{bmatrix} \begin{bmatrix}
I & 0 \\
0 & \lambda
\end{bmatrix} = \begin{bmatrix}
I & 0 \\
b^T & \lambda
\end{bmatrix}
$$

{% endraw %}

**步驟 2**：再計算 $L_1(L_2L_3)$

{% raw %}

$$
L = L_1L_2L_3 = \begin{bmatrix}
\tilde{L} & 0 \\
0 & 1
\end{bmatrix} \begin{bmatrix}
I & 0 \\
b^T & \lambda
\end{bmatrix} = \begin{bmatrix}
\tilde{L} & 0 \\
b^T & \lambda
\end{bmatrix}
$$

{% endraw %}

**步驟 3**：將 $b^T$ 用原始變數表示

回憶 $b = \tilde{L}^{-1}a$，取轉置得：

$$b^T = (\tilde{L}^{-1}a)^T = a^T(\tilde{L}^{-1})^T = a^T\tilde{L}^{-T}$$

因此最終的 Cholesky 因子為：

{% raw %}

$$
L = \begin{bmatrix}
\tilde{L} & 0 \\
b^T & \lambda
\end{bmatrix} = \begin{bmatrix}
\tilde{L} & 0 \\
a^T\tilde{L}^{-T} & \lambda
\end{bmatrix}
$$

{% endraw %}

其中：

- 左上角是 $\tilde{A}$ 的 Cholesky 因子 $\tilde{L}$（來自歸納假設）
- 左下角是 $a^T\tilde{L}^{-T}$（由第一次變換中的 $b$ 得出）
- 右下角是 $\lambda = \sqrt{\alpha - a^T\tilde{A}^{-1}a}$（由第二次變換保證為正）

以上證明來自 [How to prove the existence and uniqueness of Cholesky decomposition?
](https://math.stackexchange.com/questions/2509810/how-to-prove-the-existence-and-uniqueness-of-cholesky-decomposition) 加上 AI 修飾幫助理解，但看完後還是覺得霧煞煞的，下面不如直接以實際的 $3 \times 3$ 矩陣演示證明的過程

#### 存在性證明的具體例子

讓我們用一個 $3 \times 3$ 矩陣來說明上述證明過程。考慮上面例子中的矩陣：

{% raw %}

$$
A = \begin{bmatrix}
4 & 2 & 1 \\
2 & 5 & 2 \\
1 & 2 & 3
\end{bmatrix}
$$

{% endraw %}

**步驟 1：矩陣分塊**

將 $A$ 分塊為：

{% raw %}

$$
A = \begin{bmatrix}
\tilde{A} & a \\
a^T & \alpha
\end{bmatrix} = \begin{bmatrix}
\begin{bmatrix}
4 & 2 \\
2 & 5
\end{bmatrix} & \begin{bmatrix}
1 \\
2
\end{bmatrix} \\
\begin{bmatrix}
1 & 2
\end{bmatrix} & 3
\end{bmatrix}
$$

{% endraw %}

其中 {% raw %} $\tilde{A} = \begin{bmatrix}
4 & 2 \\
2 & 5
\end{bmatrix}$，$a = \begin{bmatrix}
1 \\
2
\end{bmatrix}$，$\alpha = 3$
{% endraw %}

**步驟 2：對 $\tilde{A}$ 進行 Cholesky 分解**

$\tilde{A}$ 是 $2 \times 2$ 正定矩陣，我們可以找到它的 Cholesky 分解：

{% raw %}

$$
\tilde{A} = \tilde{L}\tilde{L}^T = \begin{bmatrix}
2 & 0 \\
1 & 2
\end{bmatrix} \begin{bmatrix}
2 & 1 \\
0 & 2
\end{bmatrix}
$$

{% endraw %}

驗證：{% raw %} $\begin{bmatrix}
2 & 0 \\
1 & 2
\end{bmatrix} \begin{bmatrix}
2 & 1 \\
0 & 2
\end{bmatrix} = \begin{bmatrix}
4 & 2 \\
2 & 5
\end{bmatrix}$
{% endraw %}

**步驟 3：第一次合同變換**

首先計算 {% raw %} $\tilde{L}^{-1} = \begin{bmatrix}
0.5 & 0 \\
-0.25 & 0.5
\end{bmatrix}$
{% endraw %} 和 {% raw %} $\tilde{L}^{-T} = \begin{bmatrix}
0.5 & -0.25 \\
0 & 0.5
\end{bmatrix}$
{% endraw %}

計算 $b = \tilde{L}^{-1}a$：

{% raw %}

$$
b = \begin{bmatrix}
0.5 & 0 \\
-0.25 & 0.5
\end{bmatrix} \begin{bmatrix}
1 \\
2
\end{bmatrix} = \begin{bmatrix}
0.5 \\
0.75
\end{bmatrix}
$$

{% endraw %}

現在詳細計算 $L_1^{-1}AL_1^{-T}$，其中：

{% raw %}

$$
L_1^{-1} = \begin{bmatrix}
\tilde{L}^{-1} & 0 \\
0 & 1
\end{bmatrix} = \begin{bmatrix}
0.5 & 0 & 0 \\
-0.25 & 0.5 & 0 \\
0 & 0 & 1
\end{bmatrix}, \quad
L_1^{-T} = \begin{bmatrix}
\tilde{L}^{-T} & 0 \\
0 & 1
\end{bmatrix} = \begin{bmatrix}
0.5 & -0.25 & 0 \\
0 & 0.5 & 0 \\
0 & 0 & 1
\end{bmatrix}
$$

{% endraw %}

**第一步**：計算 $AL_1^{-T}$

{% raw %}

$$
AL_1^{-T} = \begin{bmatrix}
4 & 2 & 1 \\
2 & 5 & 2 \\
1 & 2 & 3
\end{bmatrix} \begin{bmatrix}
0.5 & -0.25 & 0 \\
0 & 0.5 & 0 \\
0 & 0 & 1
\end{bmatrix} = \begin{bmatrix}
2 & 0 & 1 \\
1 & 2 & 2 \\
0.5 & 0.75 & 3
\end{bmatrix}
$$

{% endraw %}

**第二步**：計算 $L_1^{-1}(AL_1^{-T})$

{% raw %}

$$
L_1^{-1}AL_1^{-T} = \begin{bmatrix}
0.5 & 0 & 0 \\
-0.25 & 0.5 & 0 \\
0 & 0 & 1
\end{bmatrix} \begin{bmatrix}
2 & 0 & 1 \\
1 & 2 & 2 \\
0.5 & 0.75 & 3
\end{bmatrix} = \begin{bmatrix}
1 & 0 & 0.5 \\
0 & 1 & 0.75 \\
0.5 & 0.75 & 3
\end{bmatrix}
$$

{% endraw %}

**結果**：左上角的 $2 \times 2$ 子矩陣確實變成了單位矩陣 $I$

{% raw %}

$$
L_1^{-1}AL_1^{-T} = \begin{bmatrix}
I & b \\
b^T & 3
\end{bmatrix} = \begin{bmatrix}
1 & 0 & 0.5 \\
0 & 1 & 0.75 \\
0.5 & 0.75 & 3
\end{bmatrix}
$$

{% endraw %}

**步驟 4：第二次合同變換（消除 $b$）**

目標是消除 {% raw %} $b = \begin{bmatrix} 0.5 \\ 0.75 \end{bmatrix}$ {% endraw %}，我們定義矩陣：

{% raw %}

$$
L_2^{-1} = \begin{bmatrix}
1 & 0 & 0 \\
0 & 1 & 0 \\
-0.5 & -0.75 & 1
\end{bmatrix}, \quad
L_2^{-T} = \begin{bmatrix}
1 & 0 & -0.5 \\
0 & 1 & -0.75 \\
0 & 0 & 1
\end{bmatrix}
$$

{% endraw %}

其中 $B$ 是步驟 3 的結果：

{% raw %}

$$
B = \begin{bmatrix}
1 & 0 & 0.5 \\
0 & 1 & 0.75 \\
0.5 & 0.75 & 3
\end{bmatrix}
$$

{% endraw %}

**第一步**：計算 $BL_2^{-T}$

{% raw %}

$$
BL_2^{-T} = \begin{bmatrix}
1 & 0 & 0.5 \\
0 & 1 & 0.75 \\
0.5 & 0.75 & 3
\end{bmatrix} \begin{bmatrix}
1 & 0 & -0.5 \\
0 & 1 & -0.75 \\
0 & 0 & 1
\end{bmatrix} = \begin{bmatrix}
1 & 0 & 0 \\
0 & 1 & 0 \\
0.5 & 0.75 & 2.1875
\end{bmatrix}
$$

{% endraw %}

**第二步**：計算 $L_2^{-1}(BL_2^{-T})$

{% raw %}

$$
L_2^{-1}BL_2^{-T} = \begin{bmatrix}
1 & 0 & 0 \\
0 & 1 & 0 \\
-0.5 & -0.75 & 1
\end{bmatrix} \begin{bmatrix}
1 & 0 & 0 \\
0 & 1 & 0 \\
0.5 & 0.75 & 2.1875
\end{bmatrix} = \begin{bmatrix}
1 & 0 & 0 \\
0 & 1 & 0 \\
0 & 0 & 2.1875
\end{bmatrix}
$$

{% endraw %}

**結果**：非對角元素全部被消除，得到對角矩陣！

{% raw %}

$$
L_2^{-1}BL_2^{-T} = \begin{bmatrix}
1 & 0 & 0 \\
0 & 1 & 0 \\
0 & 0 & 2.1875
\end{bmatrix}
$$

{% endraw %}

現在驗證 $\alpha - b^Tb = \alpha - a^T\tilde{A}^{-1}a$：

**方法 1**：直接計算 $b^Tb$

$$b^Tb = 0.5^2 + 0.75^2 = 0.25 + 0.5625 = 0.8125$$

$$\alpha - b^Tb = 3 - 0.8125 = 2.1875$$

**方法 2**：計算 $a^T\tilde{A}^{-1}a$

首先計算 {% raw %} $\tilde{A}^{-1} = \begin{bmatrix}
4 & 2 \\
2 & 5
\end{bmatrix}^{-1} = \frac{1}{16} \begin{bmatrix}
5 & -2 \\
-2 & 4
\end{bmatrix} = \begin{bmatrix}
0.3125 & -0.125 \\
-0.125 & 0.25
\end{bmatrix}$
{% endraw %}

計算 $a^T\tilde{A}^{-1}a$：

{% raw %}

$$
a^T\tilde{A}^{-1}a = \begin{bmatrix} 1 & 2 \end{bmatrix} \begin{bmatrix}
0.3125 & -0.125 \\
-0.125 & 0.25
\end{bmatrix} \begin{bmatrix}
1 \\
2
\end{bmatrix} = 0.8125
$$

{% endraw %}

因此：

$$\alpha - a^T\tilde{A}^{-1}a = 3 - 0.8125 = 2.1875$$

可以看到兩種方法得到相同結果！

**步驟 5：構造 Cholesky 因子**

根據證明的反向推導，$A = (L_1L_2L_3)(L_1L_2L_3)^T$，我們需要計算 $L = L_1L_2L_3$

將具體的數值代入：

{% raw %}

$$
L_1 = \begin{bmatrix}
2 & 0 & 0 \\
1 & 2 & 0 \\
0 & 0 & 1
\end{bmatrix} \quad
L_2 = \begin{bmatrix}
1 & 0 & 0 \\
0 & 1 & 0 \\
0.5 & 0.75 & 1
\end{bmatrix} \quad
L_3 = \begin{bmatrix}
1 & 0 & 0 \\
0 & 1 & 0 \\
0 & 0 & 1.5
\end{bmatrix}
$$

{% endraw %}

**第一步**：計算 $L_2L_3$

{% raw %}

$$
L_2L_3 = \begin{bmatrix}
1 & 0 & 0 \\
0 & 1 & 0 \\
0.5 & 0.75 & 1
\end{bmatrix} \begin{bmatrix}
1 & 0 & 0 \\
0 & 1 & 0 \\
0 & 0 & 1.5
\end{bmatrix} = \begin{bmatrix}
1 & 0 & 0 \\
0 & 1 & 0 \\
0.5 & 0.75 & 1.5
\end{bmatrix}
$$

{% endraw %}

**第二步**：計算 $L_1(L_2L_3)$

{% raw %}

$$
L = L_1L_2L_3 = \begin{bmatrix}
2 & 0 & 0 \\
1 & 2 & 0 \\
0 & 0 & 1
\end{bmatrix} \begin{bmatrix}
1 & 0 & 0 \\
0 & 1 & 0 \\
0.5 & 0.75 & 1.5
\end{bmatrix} = \begin{bmatrix}
2 & 0 & 0 \\
1 & 2 & 0 \\
0.5 & 0.75 & 1.5
\end{bmatrix}
$$

{% endraw %}

**結果**：得到最終的 Cholesky 因子

{% raw %}

$$
L = \begin{bmatrix}
2 & 0 & 0 \\
1 & 2 & 0 \\
0.5 & 0.75 & 1.5
\end{bmatrix}
$$

{% endraw %}

這正是我們在 [計算範例](./#計算範例) 中得到的結果！

**驗證 $A = LL^T$**：

{% raw %}

$$
LL^T = \begin{bmatrix}
2 & 0 & 0 \\
1 & 2 & 0 \\
0.5 & 0.75 & 1.5
\end{bmatrix} \begin{bmatrix}
2 & 1 & 0.5 \\
0 & 2 & 0.75 \\
0 & 0 & 1.5
\end{bmatrix} = \begin{bmatrix}
4 & 2 & 1 \\
2 & 5 & 2 \\
1 & 2 & 3
\end{bmatrix} = A
$$

{% endraw %}

這個例子展示了證明的核心思想：

1. 先對較小的子矩陣 $\tilde{A}$ 進行 Cholesky 分解（歸納假設）
2. 利用合同變換將矩陣簡化為對角形式
3. 合同變換保持正定性，確保 $\lambda$ 是實數
4. 將所有變換矩陣相乘得到最終的 Cholesky 因子

### 唯一性證明

**定理**：Cholesky 分解是唯一的（在對角線元素為正的前提下）

**證明**：假設存在兩個分解 $A = LL^T = MM^T$，其中 $L$ 和 $M$ 都是下三角矩陣且對角線元素為正

從 $LL^T = MM^T$ 出發，我們進行以下變換：

**步驟 1**：左右同時乘以 $L^{-1}$

$$L^{-1}(LL^T) = L^{-1}(MM^T)$$

$$L^T = L^{-1}MM^T$$

**步驟 2**：右邊同時乘以 $L^{-T}$

$$L^TL^{-T} = (L^{-1}MM^T)L^{-T}$$

$$I = L^{-1}MM^TL^{-T}$$

**步驟 3**：利用矩陣乘法的結合律重新分組

$$I = L^{-1}MM^TL^{-T} = (L^{-1}M)(M^TL^{-T})$$

現在注意到 $(M^TL^{-T})$ 可以寫成轉置形式。根據轉置的性質：

$$(L^{-1}M)^T = M^T(L^{-1})^T = M^TL^{-T}$$

因此：

$$I = (L^{-1}M)(M^TL^{-T}) = (L^{-1}M)(L^{-1}M)^T$$

**步驟 4**：推導 $D$ 的性質

設 $D := L^{-1}M$，則上式變為：

$$I = DD^T$$

從 $D = L^{-1}M$ 的定義，兩邊左乘 $L$ 得到：

$$LD = L(L^{-1}M) = (LL^{-1})M = IM = M$$

因此 $M = LD$。這個關係在後面會用到。

回到 $I = DD^T$，這意味著 $D^T$ 是 $D$ 的逆矩陣，即：

$$D^T = D^{-1}$$

等價地：

$$D = D^{-T}$$

也就是：

$$L^{-1}M = (L^{-1}M)^{-T}$$

**步驟 5**：利用矩陣類型的性質

注意到：

- $L^{-1}M$ 是兩個下三角矩陣的乘積，因此是**下三角矩陣**
- $(L^{-1}M)^{-T}$ 是下三角矩陣的逆的轉置，因此是**上三角矩陣**

因為 $D$ 必須同時是下三角和上三角矩陣，所以 $D$ 只能是**對角矩陣**！

**步驟 6**：確定 $D$ 的值

從 $I = DD^T$ 可知，對於對角矩陣 $D$：

$$I = DD^T = D^2$$

這意味著 $D$ 的每個對角線元素 $d_i$ 滿足 $d_i^2 = 1$，因此 $d_i = \pm 1$

但是，因為 $M = LD$，而：

- $L$ 的對角線元素都為正（Cholesky 分解的要求）
- $M$ 的對角線元素都為正（Cholesky 分解的要求）
- $D$ 是對角矩陣

所以 $D$ 的對角線元素必須都為正，即 $d_i = +1$，因此：

$$D = I$$

**結論**：從 $M = LD = LI = L$ 得知 $M = L$，證明了唯一性

## 補充資料

### 1. 符號 `:=` 的意義

在數學中，符號 `:=` 表示「定義為」（defined as）或「令...等於」，用來引入新的變數或表達式

例如在證明中：

$$b := \tilde{L}^{-1}a$$

意思是「定義 $b$ 為 $\tilde{L}^{-1}a$」，這是第一次引入變數 $b$，而不是說 $b$ 原本就存在並且等於 $\tilde{L}^{-1}a$

**與 `=` 的區別**：

- `=`：表示相等關係，兩邊的量已經存在且相等
- `:=`：表示定義，左邊是新引入的符號，右邊是它的定義

在證明中使用 `:=` 可以讓讀者清楚知道：「這是我們為了方便而定義的新符號」

例如：

- $D := L^{-1}M$ 表示「定義 $D$ 為 $L^{-1}M$」
- $L_3 := {% raw %} \begin{bmatrix} I & 0 \\ 0 & \lambda \end{bmatrix}$ {% endraw %} 表示「定義 $L_3$ 為這個矩陣」

[What does := mean in math?](https://www.quora.com/What-does-mean-in-math-2)

### 2. 合同變換 (Congruence Transformation)

#### 定義

對於矩陣 $A$ 和可逆矩陣 $P$，合同變換是指：

$$B = P^T A P$$

其中 $P^T$ 是 $P$ 的轉置。如果存在可逆矩陣 $P$ 使得 $B = P^T A P$，我們稱矩陣 $A$ 和 $B$ **合同 (congruent)**

**等價形式**：合同變換也可以寫成 $B = QAQ^T$ 的形式（其中 $Q$ 可逆）。為什麼？

如果設 $Q = P^T$，則 $Q^T = (P^T)^T = P$，因此：

$$B = P^TAP = Q A Q^T$$

兩種形式本質上相同。在 Cholesky 分解的證明中，我們使用的 $L_1^{-1}AL_1^{-T}$ 就屬於第二種形式（設 $Q = L_1^{-1}$，則 $Q^T = L_1^{-T}$）

#### 關鍵性質

**1. 保持對稱性**

如果 $A$ 是對稱矩陣（$A = A^T$），那麼 $P^T A P$ 也是對稱矩陣：

$$(P^T A P)^T = (AP)^T (P^T)^T = P^T A^T (P^T)^T = P^T A P$$

**2. 保持正定性**（最重要的性質）

如果 $A$ 是正定矩陣，那麼 $P^T A P$ 也是正定矩陣

證明：對於任意非零向量 $y$，令 $x = Py$。因為 $P$ 可逆，所以 $x \neq 0$，則：

$$y^T B y = y^T (P^T A P) y = (Py)^T A (Py) = x^T A x > 0$$

因為 $A$ 正定且 $x \neq 0$，所以 $x^T A x > 0$

#### 在 Cholesky 分解證明中的作用

在存在性證明中，合同變換有三個重要作用：

**1. 簡化矩陣結構**

- 第一次變換 $L_1^{-1}AL_1^{-T}$ 將左上角的 $\tilde{A}$ 變成單位矩陣 $I$
- 第二次變換 $L_2^{-1}BL_2^{-T}$ 將非對角元素消除，得到對角矩陣

**2. 保證正定性**

- 每次變換後的矩陣仍然是正定的
- 這確保了 $\alpha - b^Tb > 0$
- 因此 $\lambda = \sqrt{\alpha - b^Tb}$ 是實數且為正

**3. 可逆性**

- 因為變換是可逆的，我們可以反向操作：從 $L_2^{-1}L_1^{-1}AL_1^{-T}L_2^{-T} = L_3L_3^T$ 得到 $A = (L_1L_2L_3)(L_1L_2L_3)^T$

#### 具體例子

在存在性證明的例子中，我們看到通過合同變換：

{% raw %}

$$
\begin{bmatrix}
1 & 0 & 0.5 \\
0 & 1 & 0.75 \\
0.5 & 0.75 & 3
\end{bmatrix} \xrightarrow{\text{合同變換}} \begin{bmatrix}
1 & 0 & 0 \\
0 & 1 & 0 \\
0 & 0 & 2.1875
\end{bmatrix}
$$

{% endraw %}

變換後的矩陣仍然正定（對角元素 $2.1875 > 0$），這就是合同變換保持正定性的體現！

[合同变换\_百度百科](https://baike.baidu.com/item/%E5%90%88%E5%90%8C%E8%AE%8A%E6%8F%9B/2723650)
[合同矩陣](https://zh.wikipedia.org/zh-tw/%E5%90%88%E5%90%8C%E7%9F%A9%E9%98%B5)

## 相關連結

[轉置矩陣](/2025/09/27/轉置矩陣/)

## 參考資料

[Cholesky 分解](https://o-o-sudo.github.io/numerical-methods/cholesky-cholesky-decomposition.html)
[How to prove the existence and uniqueness of Cholesky decomposition?](https://math.stackexchange.com/questions/2509810/how-to-prove-the-existence-and-uniqueness-of-cholesky-decomposition)
