---
title: 矩陣中的軸元與秩
date: 2025-09-24 23:03:41
updated: 2025-11-03 23:03:41
categories: 線性代數
mathjax: true
---

## 定義

在矩陣中，**軸元(pivot)** 是指每列中的第一個非零元素

考慮以下矩陣：

{% raw %}

$$A = \begin{bmatrix} 2 & 1 & 3 \\ 0 & 4 & 1 \\ 0 & 0 & 5 \end{bmatrix}$$

{% endraw %}

在這個矩陣中：

- 第 1 列的軸元是 $a_{11} = 2$（位置 (1,1)）
- 第 2 列的軸元是 $a_{22} = 4$（位置 (2,2)）
- 第 3 列的軸元是 $a_{33} = 5$（位置 (3,3)）

因此對於矩陣 $A$ 來說有三個軸元分別是 $2, 4, 5$

<!-- more -->

## 性質

### 1. 軸元與矩陣的秩

矩陣的 [秩(rank)](<https://zh.wikipedia.org/zh-tw/%E7%A7%A9_(%E7%BA%BF%E6%80%A7%E4%BB%A3%E6%95%B0)>) 等於其軸元的個數。這意味著：

- 如果矩陣有 $r$ 個軸元，則 $\text{rank}(A) = r$
- 如果矩陣沒有軸元（零矩陣），則 $\text{rank}(A) = 0$

## 計算

計算軸元最常使用的是高斯消去法

### 範例 1.

考慮矩陣：

{% raw %}

$$A = \begin{bmatrix} 1 & 2 & 3 \\ 2 & 4 & 6 \\ 3 & 6 & 9 \end{bmatrix}$$

{% endraw %}

**步驟 1**：第一列的軸元是 $a_{11} = 1$

**步驟 2**：消去第二列第一行的元素

{% raw %}

$$\begin{bmatrix} 1 & 2 & 3 \\ 0 & 0 & 0 \\ 3 & 6 & 9 \end{bmatrix}$$

{% endraw %}

**步驟 3**：消去第三列第一行的元素

{% raw %}

$$\begin{bmatrix} 1 & 2 & 3 \\ 0 & 0 & 0 \\ 0 & 0 & 0 \end{bmatrix}$$

{% endraw %}

**最終結果**：只有一個軸元 $a_{11} = 1$，因此 $\text{rank}(A) = 1$

### 範例 2.

考慮矩陣：

{% raw %}

$$A = \begin{bmatrix} 1 & -1 & 1 \\ 4 & -2 & 1 \\ 1 & -3 & 2 \end{bmatrix}$$

{% endraw %}

**步驟 1**：第一列的軸元是 $a_{11} = 1$

**步驟 2**：消去第二列第一行的元素 (4)

{% raw %}

$$\begin{bmatrix} 1 & -1 & 1 \\ 0 & 2 & -3 \\ 1 & -3 & 2 \end{bmatrix}$$

{% endraw %}

**步驟 3**：消去第三列第一行的元素 (1)

{% raw %}

$$\begin{bmatrix} 1 & -1 & 1 \\ 0 & 2 & -3 \\ 0 & -2 & 1 \end{bmatrix}$$

{% endraw %}

**步驟 4**：第二列的軸元是 $a_{22} = 2$

**步驟 5**：消去第三列第二行的元素 (-2)

{% raw %}

$$\begin{bmatrix} 1 & -1 & 1 \\ 0 & 2 & -3 \\ 0 & 0 & -2 \end{bmatrix}$$

{% endraw %}

**最終結果**：有三個軸元 $a_{11} = 1$，$a_{22} = 2$，$a_{33} = -2$，因此 $\text{rank}(A) = 3$

## 滿秩矩陣

### 定義

對於 $m \times n$ 矩陣 $A$，如果其秩等於矩陣的列數或行數中的較小值，則稱該矩陣為 **滿秩矩陣(full rank matrix)**

具體來說：

- 如果 $m \leq n$ 且 $\text{rank}(A) = m$，則 $A$ 是 **列滿秩(row full rank)**
- 如果 $n \leq m$ 且 $\text{rank}(A) = n$，則 $A$ 是 **行滿秩(column full rank)**
- 對於 $n \times n$ 方陣，如果 $\text{rank}(A) = n$，則 $A$ 是 **滿秩方陣**

### 滿秩方陣與線性獨立性

對於 $n \times n$ 的方陣 $A$，若 $\text{rank}(A) = n$，其為 **滿秩方陣**

此時：

- 行向量線性獨立
- 列向量線性獨立
- $\det(A) \ne 0$
- $A$ 可逆（invertible）

### 範例 1：滿秩方陣

考慮 $3 \times 3$ 矩陣：

{% raw %}

$$A = \begin{bmatrix} 1 & 2 & 0 \\ 0 & 1 & 1 \\ 1 & 0 & 1 \end{bmatrix}$$

{% endraw %}

通過高斯消去法得到：

{% raw %}

$$\begin{bmatrix} 1 & 2 & 0 \\ 0 & 1 & 1 \\ 0 & 0 & 3 \end{bmatrix}$$

{% endraw %}

此矩陣有三個軸元：$a_{11} = 1$，$a_{22} = 1$，$a_{33} = 3$

- $\text{rank}(A) = 3 = n$，因此 $A$ 是**滿秩方陣**
- **列向量線性獨立**：$\begin{bmatrix} 1 & 2 & 0 \end{bmatrix}$，$\begin{bmatrix} 0 & 1 & 1 \end{bmatrix}$，$\begin{bmatrix} 1 & 0 & 1 \end{bmatrix}$ 彼此線性獨立
- **行向量線性獨立**：$\begin{bmatrix} 1 & 0 & 1 \end{bmatrix}^T$，$\begin{bmatrix} 2 & 1 & 0 \end{bmatrix}^T$，$\begin{bmatrix} 0 & 1 & 1 \end{bmatrix}^T$ 彼此線性獨立
- $\det(A) = 3 \ne 0$，因此 $A$ 可逆

### 範例 2：列滿秩矩陣

考慮 $2 \times 3$ 矩陣（列數大於行數）：

{% raw %}

$$B = \begin{bmatrix} 1 & 0 & 1 \\ 0 & 1 & 1 \end{bmatrix}$$

{% endraw %}

此矩陣有兩個軸元：$b_{11} = 1$，$b_{22} = 1$

- $\text{rank}(B) = 2 = m$（列數），因此 $B$ 是**列滿秩**
- 列向量 {% raw %} $\begin{bmatrix} 1 & 0 & 1 \end{bmatrix}$ 和 $\begin{bmatrix} 0 & 1 & 1 \end{bmatrix}$ {% endraw %} 線性獨立

### 範例 3：行滿秩矩陣

考慮 $3 \times 2$ 矩陣（列數大於行數）：

{% raw %}

$$C = \begin{bmatrix} 1 & 2 \\ 0 & 1 \\ 0 & 0 \end{bmatrix}$$

{% endraw %}

此矩陣有兩個軸元：$c_{11} = 1$，$c_{22} = 1$

- $\text{rank}(C) = 2 = n$（行數），因此 $C$ 是**行滿秩**
- 行向量 {% raw %} $\begin{bmatrix} 1 \\ 0 \\ 0 \end{bmatrix}$ 和 $\begin{bmatrix} 2 \\ 1 \\ 0 \end{bmatrix}$ {% endraw %} 線性獨立

### 範例 4：非滿秩矩陣

考慮 $3 \times 3$ 矩陣：

{% raw %}

$$D = \begin{bmatrix} 1 & 2 & 3 \\ 2 & 4 & 6 \\ 3 & 6 & 9 \end{bmatrix}$$

{% endraw %}

通過高斯消去法：

{% raw %}

$$\begin{bmatrix} 1 & 2 & 3 \\ 0 & 0 & 0 \\ 0 & 0 & 0 \end{bmatrix}$$

{% endraw %}

- 只有一個軸元 $d_{11} = 1$，因此 $\text{rank}(D) = 1 < 3$
- $D$ **不是滿秩矩陣**
- 列向量線性相關：$\begin{bmatrix} 2 \\ 4 \\ 6 \end{bmatrix} = 2\begin{bmatrix} 1 \\ 2 \\ 3 \end{bmatrix}$，$\begin{bmatrix} 3 \\ 6 \\ 9 \end{bmatrix} = 3\begin{bmatrix} 1 \\ 2 \\ 3 \end{bmatrix}$
- $D$ 不可逆

## 相關連結

[奇異矩陣](/2025/09/20/奇異矩陣-singular-matrix/)

## 參考資料

[wiki - 軸元](https://zh.wikipedia.org/zh-tw/%E4%B8%BB%E5%85%83)
[高斯消去法](https://ccjou.wordpress.com/2013/02/20/%E9%AB%98%E6%96%AF%E6%B6%88%E5%8E%BB%E6%B3%95/)
[高斯消去法與高斯 ─ 約當法的運算量](https://ccjou.wordpress.com/2014/08/12/%E9%AB%98%E6%96%AF%E6%B6%88%E5%8E%BB%E6%B3%95%E8%88%87%E9%AB%98%E6%96%AF%E2%94%80%E7%B4%84%E7%95%B6%E6%B3%95%E7%9A%84%E9%81%8B%E7%AE%97%E9%87%8F/)

- 秩
  [如何理解矩阵的「秩」？](https://www.zhihu.com/question/21605094)
  [你不能不知道的矩陣秩](https://ccjou.wordpress.com/2010/03/02/%E4%BD%A0%E4%B8%8D%E8%83%BD%E4%B8%8D%E7%9F%A5%E9%81%93%E7%9A%84%E7%9F%A9%E9%99%A3%E7%A7%A9/)
