---
title: 子式 - minor
date: 2025-10-19 00:21:46
updated: 2025-11-03 00:21:46
categories: 線性代數
mathjax: true
---

## 子式

對於一個 $n \times n$ 的矩陣 $A$，我們可以通過取出 $A$ 的某些行與列來得到較小的子矩陣，這些子矩陣的行列式就稱為**子式(Minor)**

<!-- 設 $A$ 是一個 $n \times n$ 矩陣，$I$ 和 $J$ 是 $\{1, 2, \ldots, n\}$ 的子集，則 $A$ 的 $(I, J)$ 子式記為 $M_{I,J}(A)$，定義為： -->

$$M_{I,J}(A) = \det(A_{I,J})$$

<!-- 其中 $A_{I,J}$ 是由 $A$ 的第 $i$ 列（$i \in I$）和第 $j$ 行（$j \in J$）組成的 $k \times k$ 子矩陣 -->

其中 $I, J$ 代表從列與行中的元素取出來的 **索引集合**：

- 列的集合：{% raw %} $I = \{i_1, i_2, \dots, i_r\}$ {% endraw %}
- 行的集合：{% raw %} $J = \{j_1, j_2, \dots, j_c\}$ {% endraw %}

<!-- $M_{I,J}$ 表示從 $A$ 中選出第 $I$ 列與第 $J$ 行形成的子矩陣 -->

下面來看實際的例子會比較好理解

<!-- more -->

### 具體例子

考慮一個 $3 \times 3$ 矩陣：

{% raw %}

$$
A = \begin{bmatrix}
1 & 2 & 3 \\
4 & 5 & 6 \\
7 & 8 & 9
\end{bmatrix}
$$

{% endraw %}

一些子式的例子：

1. **一階子式**：

{% raw %}

$$M_{\{1\},\{1\}}(A) = 1$$

$$M_{\{2\},\{3\}}(A) = 6$$

{% endraw %}

2. **二階子式**：

{% raw %}

- 取列 $I = \{1, 2\}$，取行 $J = \{1, 2\}$

$$M_{\{1,2\},\{1,2\}}(A) = \det \begin{bmatrix} 1 & 2 \\ 4 & 5 \end{bmatrix} = 1 \cdot 5 - 2 \cdot 4 = 5 - 8 = -3$$

- 取列 $I = \{1, 3\}$，取行 $J = \{2, 3\}$

$$M_{\{1,3\},\{2,3\}}(A) = \det \begin{bmatrix} 2 & 3 \\ 8 & 9 \end{bmatrix} = 2 \cdot 9 - 3 \cdot 8 = 18 - 24 = -6$$

{% endraw %}

3. **三階子式**：

{% raw %}

$$
\begin{align}
M_{\{1,2,3\},\{1,2,3\}}(A) &= \det(A) \\
&= 1(5 \cdot 9 - 6 \cdot 8) - 2(4 \cdot 9 - 6 \cdot 7) + 3(4 \cdot 8 - 5 \cdot 7) \\
&= 1(45-48) - 2(36-42) + 3(32-35) \\
&= 1(-3) - 2(-6) + 3(-3) \\
&= -3 + 12 - 9 = 0
\end{align}
$$

{% endraw %}

## 主子式

**主子式(Principal Minor)** 是一種特殊的子式，它要求 **選取的行和列的索引完全相同**，表示為：

$$M_I(A) = \det(A_{I})$$

<!-- 其中 $A_{I,I}$ 是由 $A$ 的第 $i$ 行和第 $i$ 列（$i \in I$）組成的 $k \times k$ 子矩陣 -->

### 關鍵特徵

主子式的特徵是：

- 選取的行索引集合 = 選取的列索引集合
- 即 $I = J$，所以記為 $M_I(A)$ 而不是 $M_{I,J}(A)$

### 具體例子

對於上面的 $3 \times 3$ 矩陣 $A$：

1. **一階主子式**：

{% raw %}

$$M_{\{1\}}(A) = 1$$

{% endraw %}

2. **二階主子式**：

{% raw %}

$$M_{\{1,2\}}(A) = \begin{bmatrix} 1 & 2 \\ 4 & 5 \end{bmatrix} = 1 \cdot 5 - 2 \cdot 4 = -3$$

$$M_{\{1,3\}}(A) = \begin{bmatrix} 1 & 3 \\ 7 & 9 \end{bmatrix} = 1 \cdot 9 - 3 \cdot 7 = -12$$

$$M_{\{2,3\}}(A) = \begin{bmatrix} 5 & 6 \\ 8 & 9 \end{bmatrix} = 5 \cdot 9 - 6 \cdot 8 = -3$$

{% endraw %}

3. **三階主子式**：

{% raw %}

$$M_{\{1,2,3\}}(A) = \det(A) = 0$$

{% endraw %}

## 順序主子式

**順序主子式(Sequential Principal Minor)**，或者英文也叫做 **Leading Principal Minor** 是主子式的一個特殊子類別，它要求 **選取的行和列索引必須是連續的**，且從第 1 行第 1 列開始

對於 $n \times n$ 矩陣 $A$，其 $k$ 階順序主子式記為 $D_k(A)$，定義為：

{% raw %}

$$D_k(A) = \det(A_{1:k, 1:k})$$

{% endraw %}

其中 {% raw %} $A_{1:k, 1:k}$ {% endraw %} 是由 $A$ 的前 $k$ 行和前 $k$ 列組成的 $k \times k$ 子矩陣

### 關鍵特徵

順序主子式的特徵是：

- 選取的行索引 = 選取的列索引 = {% raw %} $\{1, 2, \ldots, k\}$ {% endraw %}
- 索引必須是連續的，從 1 開始
- 一個 $n \times n$ 矩陣有 $n$ 個順序主子式：$D_1, D_2, \ldots, D_n$

### 具體例子

對於上面的 $3 \times 3$ 矩陣 $A$：

1. **一階順序主子式**：

{% raw %}

$$D_1(A) = \det([1]) = 1$$

{% endraw %}

2. **二階順序主子式**：

{% raw %}

$$D_2(A) = \det\begin{bmatrix} 1 & 2 \\ 4 & 5 \end{bmatrix} = 1 \cdot 5 - 2 \cdot 4 = -3$$

{% endraw %}

3. **三階順序主子式**：

{% raw %}

$$D_3(A) = \det(A) = 0$$

{% endraw %}

由於順序主子式是從左上角一路取元素下來，所以也可以稱作為 **左上子矩陣(upper left submatrix)**

## 參考資料

[wiki - Minor (linear algebra)](<https://en.wikipedia.org/wiki/Minor_(linear_algebra)>)
