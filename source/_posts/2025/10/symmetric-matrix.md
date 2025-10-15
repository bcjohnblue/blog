---
title: 對稱矩陣
date: 2025-10-15 23:35:44
updated: 2025-10-15 23:35:44
categories: 線性代數
mathjax: true
---

## 定義

對於 $n \times n$ 矩陣 $A$，如果滿足：

$$A^T = A$$

則稱 $A$ 為對稱矩陣

## 例子

{% raw %}

$$
A = \begin{bmatrix}
2 & 1 \\
1 & 3
\end{bmatrix}
$$

$$
B = \begin{bmatrix}
1 & 2 & 3 \\
2 & 4 & 5 \\
3 & 5 & 6
\end{bmatrix}
$$

任何對角矩陣都是對稱的：

$$
C = \begin{bmatrix}
1 & 0 & 0 \\
0 & 2 & 0 \\
0 & 0 & 3
\end{bmatrix}
$$

{% endraw %}

<!-- more -->

## 特性

### 1. 結構特性

- **主對角線元素**：可以任意取值
- **非主對角線元素**：必須滿足 $a_{ij} = a_{ji}$
- **矩陣維度**：必須是方陣（$n \times n$）

### 2. 數學運算性質

#### 2-1. 對稱矩陣的和與純量倍數

如果 $A$ 和 $B$ 都是對稱矩陣，則：

- $A + B$ 是對稱矩陣
- $cA$（$c$ 為純量）是對稱矩陣

**證明：**
$$(A + B)^T = A^T + B^T = A + B$$
$$(cA)^T = cA^T = cA$$

#### 2-2. 對稱矩陣的乘積

兩個對稱矩陣相乘的結果 **不一定是對稱矩陣**。只有當這兩個對稱矩陣的乘法可交換（即 $AB = BA$）時，其乘積才會是對稱矩陣

**證明：**

- 一般情況下，矩陣乘法不滿足交換律，即 $AB ≠ BA$

- 當兩個矩陣相乘的結果是 $A$ 和 $B$ 的乘積，其轉置會是 $$(AB)^T = B^T A^T$$

- 由於 $A$ 和 $B$ 都是對稱矩陣，所以 $A^T = A，B^T = B$，因此 $(AB)^T = BA$

- 為了讓乘積 $AB$ 也是對稱矩陣，必須滿足 $AB = (AB)^T$

- $AB = (AB)^T = BA$ => 得證需要滿足交換律，其乘積才會是對稱矩陣

### 3. 特徵值性質

#### 3-1. 實特徵值

[實對稱矩陣](https://zh.wikipedia.org/zh-tw/%E5%B0%8D%E7%A8%B1%E7%9F%A9%E9%99%A3#%E5%AF%A6%E5%B0%8D%E7%A8%B1%E7%9F%A9%E9%99%A3) 的所有特徵值都是**實數**

**證明：**
設 $A$ 是實對稱矩陣，$\lambda$ 是 $A$ 的特徵值，$\mathbf{v}$ 是對應的特徵向量，則：

$$A\mathbf{v} = \lambda\mathbf{v}$$

取 [共軛轉置](https://zh.wikipedia.org/zh-tw/%E5%85%B1%E8%BD%AD%E8%BD%AC%E7%BD%AE)：

$$(A\mathbf{v})^* = (\lambda\mathbf{v})^*$$

$$\mathbf{v}^* A^* = \lambda^* \mathbf{v}^*$$

由於 $A$ 的每個元素都是實數，所以：

$$\mathbf{v}^* A^* = \mathbf{v}^* A^T$$

進一步由於 $A$ 是對稱矩陣，$A^T = A$，所以：

$$\mathbf{v}^* A^* = \mathbf{v}^* A$$

所以：
$$\mathbf{v}^* A^* = \mathbf{v}^* A = \lambda^* \mathbf{v}^*$$

將上式右乘 $\mathbf{v}$：
$$\mathbf{v}^* A \mathbf{v} = \lambda^* \mathbf{v}^* \mathbf{v}$$

{% raw %}

將一開始的式子 $A\mathbf{v} = \lambda\mathbf{v}$，左乘 $\mathbf{v}^*$：
$$\mathbf{v}^* A \mathbf{v} = \lambda \mathbf{v}^* \mathbf{v}$$

{% endraw %}

比較上述兩式成立條件為：$\lambda = \lambda^*$，即 $\lambda$ 是實數

#### 3-2. 正交特徵向量

對稱矩陣的不同特徵值對應的特徵向量是**正交**的

**證明：**
設 $A\mathbf{v}_1 = \lambda_1 \mathbf{v}_1$，$A\mathbf{v}_2 = \lambda_2 \mathbf{v}_2$，且 $\lambda_1 \neq \lambda_2$

考慮 $A\mathbf{v}_1 \cdot \mathbf{v}_2$：

**方法一：**
$$A\mathbf{v}_1 \cdot \mathbf{v}_2 = \lambda_1\mathbf{v}_1 \cdot \mathbf{v}_2 = \lambda_1(\mathbf{v}_1 \cdot \mathbf{v}_2)$$

**方法二：**
根據內積定義 $\mathbf{u} \cdot \mathbf{v} = \mathbf{u}^T \mathbf{v}$，因此：

$$A\mathbf{v}_1 \cdot \mathbf{v}_2 = (A\mathbf{v}_1)^T \mathbf{v}_2$$

套用 [矩陣乘法的轉置](/2025/09/27/轉置矩陣/#4-矩陣乘法的轉置)，右式可變成：

$$
(A\mathbf{v}_1)^T \mathbf{v}_2 = (\mathbf{v}_1^TA^T) \mathbf{v}_2
$$

利用矩陣乘法的結合律：

$$
(\mathbf{v}_1^TA^T) \mathbf{v}_2 = \mathbf{v}_1^T(A^T \mathbf{v}_2)
$$

再次利用內積定義公式 $\mathbf{u}^T \mathbf{v} = \mathbf{u} \cdot \mathbf{v}$，改寫右式將矩陣乘法轉換回內積：

$$
\mathbf{v}_1^T(A^T \mathbf{v}_2) = \mathbf{v}_1 \cdot (A^T\mathbf{v}_2)
$$

綜合以上推導，可得：

$$A\mathbf{v}_1 \cdot \mathbf{v}_2 = \mathbf{v}_1 \cdot A^T \mathbf{v}_2$$

利用對稱矩陣 $A^T = A$ 的性質：

$$\mathbf{v}_1 \cdot A^T \mathbf{v}_2 = \mathbf{v}_1 \cdot A\mathbf{v}_2$$

因此方法二最後可以寫成：

$$A\mathbf{v}_1 \cdot \mathbf{v}_2 = \mathbf{v}_1 \cdot A^T \mathbf{v}_2 = \mathbf{v}_1 \cdot A\mathbf{v}_2 = \mathbf{v}_1 \cdot \lambda_2\mathbf{v}_2 = \lambda_2(\mathbf{v}_1 \cdot \mathbf{v}_2)$$

比較方法一與方法二：

$$A\mathbf{v}_1 \cdot \mathbf{v}_2 = \lambda_1(\mathbf{v}_1 \cdot \mathbf{v}_2)$$

$$A\mathbf{v}_1 \cdot \mathbf{v}_2 = \lambda_2(\mathbf{v}_1 \cdot \mathbf{v}_2)$$

由於兩種方法計算的是同一個量，所以：
$$\lambda_1(\mathbf{v}_1 \cdot \mathbf{v}_2) = \lambda_2(\mathbf{v}_1 \cdot \mathbf{v}_2)$$

移項得到：
$$(\lambda_1 - \lambda_2)(\mathbf{v}_1 \cdot \mathbf{v}_2) = 0$$

由於 $\lambda_1 \neq \lambda_2$，所以 $\lambda_1 - \lambda_2 \neq 0$，因此必須有：
$$\mathbf{v}_1 \cdot \mathbf{v}_2 = 0$$

證明 $\mathbf{v}_1$ 和 $\mathbf{v}_2$ 必須是正交的

### 4. 對角化性質

#### 4-1. 正交對角化

任何對稱矩陣都可以**正交對角化**，即存在正交矩陣 $Q$ 使得：

$$A = Q D Q^T$$

其中 $D$ 是對角矩陣，對角線元素是 $A$ 的特徵值

##### **範例：**

正交對角化的過程其實跟 [矩陣對角化](/2025/10/07/矩陣對角化/#對角化步驟) 的步驟是差不多的，以下考慮對稱矩陣 $A$：

{% raw %}

$$A = \begin{bmatrix} 2 & -2 \\ -2 & 5 \end{bmatrix}$$

{% endraw %}

###### **步驟 1：求特徵值**

特徵方程：$\det(A - \lambda I) = 0$

{% raw %}

$$\det\begin{bmatrix} 2-\lambda & -2 \\ -2 & 5-\lambda \end{bmatrix} = (2-\lambda)(5-\lambda) - (-2)(-2) = 0$$

{% endraw %}

展開得到：
$$(2-\lambda)(5-\lambda) - 4 = 0$$
$$10 - 2\lambda - 5\lambda + \lambda^2 - 4 = 0$$
$$\lambda^2 - 7\lambda + 6 = 0$$

因式分解：$(\lambda - 1)(\lambda - 6) = 0$

因此特徵值為：$\lambda_1 = 1$，$\lambda_2 = 6$

###### **步驟 2：求特徵向量**

對於 $\lambda_1 = 1$：

{% raw %}

$$(A - I)\mathbf{v}_1 = 0$$
$$\begin{bmatrix} 1 & -2 \\ -2 & 4 \end{bmatrix}\begin{bmatrix} x_1 \\ x_2 \end{bmatrix} = \begin{bmatrix} 0 \\ 0 \end{bmatrix}$$

{% endraw %}

這給出方程組：

- $x_1 - 2x_2 = 0$
- $-2x_1 + 4x_2 = 0$

兩個方程等價，取 $x_2 = 1$，則 $x_1 = 2$

所以特徵向量為：{% raw %} $\mathbf{v}_1 = \begin{bmatrix} 2 \\ 1 \end{bmatrix}$ {% endraw %}

對於 $\lambda_2 = 6$：

{% raw %}

$$(A - 6I)\mathbf{v}_2 = 0$$
$$\begin{bmatrix} -4 & -2 \\ -2 & -1 \end{bmatrix}\begin{bmatrix} x_1 \\ x_2 \end{bmatrix} = \begin{bmatrix} 0 \\ 0 \end{bmatrix}$$

{% endraw %}

這給出方程組：

- $-4x_1 - 2x_2 = 0$
- $-2x_1 - x_2 = 0$

兩個方程等價，取 $x_1 = 1$，則 $x_2 = -2$

所以特徵向量為：{% raw %} $\mathbf{v}_2 = \begin{bmatrix} 1 \\ -2 \end{bmatrix}$ {% endraw %}

###### **步驟 3：正交化特徵向量**

檢查兩個特徵向量的內積：
$$\mathbf{v}_1 \cdot \mathbf{v}_2 = 2 \cdot 1 + 1 \cdot (-2) = 2 - 2 = 0$$

內積為 0 表示兩個特徵向量為正交，如同 [3-2. 正交特徵向量](./#3-2-正交特徵向量) 中所證明

###### **步驟 4：單位化特徵向量**

**單位化 $\mathbf{v}_1$：**

{% raw %}

$$\|\mathbf{v}_1\| = \sqrt{2^2 + 1^2} = \sqrt{5}$$
$$\mathbf{q}_1 = \frac{1}{\sqrt{5}}\begin{bmatrix} 2 \\ 1 \end{bmatrix} = \begin{bmatrix} \frac{2}{\sqrt{5}} \\ \frac{1}{\sqrt{5}} \end{bmatrix}$$

{% endraw %}

**單位化 $\mathbf{v}_2$：**

{% raw %}

$$\|\mathbf{v}_2\| = \sqrt{1^2 + (-2)^2} = \sqrt{5}$$
$$\mathbf{q}_2 = \frac{1}{\sqrt{5}}\begin{bmatrix} 1 \\ -2 \end{bmatrix} = \begin{bmatrix} \frac{1}{\sqrt{5}} \\ \frac{-2}{\sqrt{5}} \end{bmatrix}$$

{% endraw %}

###### **步驟 5：構建正交矩陣和對角矩陣**

正交矩陣：

{% raw %}

$$Q = \begin{bmatrix} \frac{2}{\sqrt{5}} & \frac{1}{\sqrt{5}} \\ \frac{1}{\sqrt{5}} & \frac{-2}{\sqrt{5}} \end{bmatrix}$$

{% endraw %}

對角矩陣：

{% raw %}

$$D = \begin{bmatrix} 1 & 0 \\ 0 & 6 \end{bmatrix}$$

{% endraw %}

###### **步驟 6：驗證正交對角化**

最終驗證 $A = QDQ^T$：

{% raw %}

$$
\begin{align*}
QDQ^T &= \begin{bmatrix} \frac{2}{\sqrt{5}} & \frac{1}{\sqrt{5}} \\ \frac{1}{\sqrt{5}} & \frac{-2}{\sqrt{5}} \end{bmatrix}\begin{bmatrix} 1 & 0 \\ 0 & 6 \end{bmatrix}\begin{bmatrix} \frac{2}{\sqrt{5}} & \frac{1}{\sqrt{5}} \\ \frac{1}{\sqrt{5}} & \frac{-2}{\sqrt{5}} \end{bmatrix} \\[6pt]
&= \begin{bmatrix} \frac{2}{\sqrt{5}} & \frac{6}{\sqrt{5}} \\ \frac{1}{\sqrt{5}} & \frac{-12}{\sqrt{5}} \end{bmatrix}\begin{bmatrix} \frac{2}{\sqrt{5}} & \frac{1}{\sqrt{5}} \\ \frac{1}{\sqrt{5}} & \frac{-2}{\sqrt{5}} \end{bmatrix} \\[6pt]
&= \begin{bmatrix} \frac{4}{5} + \frac{6}{5} & \frac{2}{5} - \frac{12}{5} \\ \frac{2}{5} - \frac{12}{5} & \frac{1}{5} + \frac{24}{5} \end{bmatrix} \\[6pt]
&= \begin{bmatrix} 2 & -2 \\ -2 & 5 \end{bmatrix} \\[6pt]
&= A
\end{align*}
$$

{% endraw %}

#### 4-2. 譜定理 (Spectral theorem)

上述所說的**正交對角化**，其實是對稱矩陣 [譜定理](https://zh.wikipedia.org/zh-tw/%E8%B0%B1%E5%AE%9A%E7%90%86) 中的一部分

對於 $n \times n$ 實對稱矩陣 $A$，存在正交矩陣 $Q$ 使得：

$$A = QDQ^T = \sum_{i=1}^{n} \lambda_i \mathbf{q}_i \mathbf{q}_i^T$$

前式即為**正交對角化**，後式則是**譜分解**

##### 譜分解 (Spectral Decomposition)

[譜分解](https://youtu.be/0ijUQ-RfN3I?si=MWHTUv5Um4WYx3Wj&t=1853) 指的是將對稱矩陣 $A$ 分解為特徵值和特徵向量的加權和，即：

$$A = \sum_{i=1}^{n} \lambda_i \mathbf{q}_i \mathbf{q}_i^T$$

這個分解有幾個重要特點：

1. **特徵值**：$\lambda_1, \lambda_2, \ldots, \lambda_n$ 是 $A$ 的所有特徵值
2. **特徵向量**：$\mathbf{q}_1, \mathbf{q}_2, \ldots, \mathbf{q}_n$ 是對應的單位特徵向量
3. **正交性**：$\mathbf{q}_i \cdot \mathbf{q}_j = 0$ 當 $i \neq j$，且 $\|\mathbf{q}_i\| = 1$

##### **譜定理的推導證明**

**步驟 1：構建正交矩陣**

對於每個特徵值 $\lambda_i$，我們可以找到對應的單位特徵向量 $\mathbf{q}_i$ (例如：[步驟 4：單位化特徵向量](./#步驟-4：單位化特徵向量))

在 [3-2. 正交特徵向量](./#3-2-正交特徵向量) 中已證明**不同特徵值對應的特徵向量正交**，且每個特徵向量都已單位化，所以：

{% raw %}

$$\mathbf{q}_i \cdot \mathbf{q}_j = \begin{cases} 1 & \text{if } i = j \\ 0 & \text{if } i \neq j \end{cases}$$

{% endraw %}

這意味著矩陣 {% raw %} $Q = [\mathbf{q}_1, \mathbf{q}_2, \ldots, \mathbf{q}_n]$ {% endraw %} 是正交矩陣 (例如：[步驟 5：構建正交矩陣和對角矩陣](./#步驟-5：構建正交矩陣和對角矩陣))，即 $Q^T Q = I$

**步驟 2：對角化**

由於 $A\mathbf{q}_i = \lambda_i \mathbf{q}_i$，我們有：

{% raw %}

$$AQ = A[\mathbf{q}_1, \mathbf{q}_2, \ldots, \mathbf{q}_n] = [A\mathbf{q}_1, A\mathbf{q}_2, \ldots, A\mathbf{q}_n] = [\lambda_1\mathbf{q}_1, \lambda_2\mathbf{q}_2, \ldots, \lambda_n\mathbf{q}_n] =  [\mathbf{q}_1, \mathbf{q}_2, \ldots, \mathbf{q}_n] \begin{bmatrix} \lambda_1 & 0 & \cdots & 0 \\ 0 & \lambda_2 & \cdots & 0 \\ \vdots & \vdots & \ddots & \vdots \\ 0 & 0 & \cdots & \lambda_n \end{bmatrix}$$

{% endraw %}

這可以寫成：
$$AQ = QD$$

其中 $D = \text{diag}(\lambda_1, \lambda_2, \ldots, \lambda_n)$ 是對角矩陣

**步驟 3：得到正交對角化公式**

由於 $Q$ 是正交矩陣，$Q^T Q = I$，所以 $Q^T = Q^{-1}$

從 $AQ = QD$ 兩邊右乘 $Q^T$：
$$AQQ^T = QDQ^T$$
$$A = QDQ^T$$

**步驟 4：推導譜分解**

將 $A = QDQ^T$ 展開：

{% raw %}

$$
\begin{align*}
A &= QDQ^T \\
&= [\mathbf{q}_1, \mathbf{q}_2, \ldots, \mathbf{q}_n] \begin{bmatrix} \lambda_1 & 0 & \cdots & 0 \\ 0 & \lambda_2 & \cdots & 0 \\ \vdots & \vdots & \ddots & \vdots \\ 0 & 0 & \cdots & \lambda_n \end{bmatrix} \begin{bmatrix} \mathbf{q}_1^T \\ \mathbf{q}_2^T \\ \vdots \\ \mathbf{q}_n^T \end{bmatrix} \\
&= [\lambda_1\mathbf{q}_1, \lambda_2\mathbf{q}_2, \ldots, \lambda_n\mathbf{q}_n] \begin{bmatrix} \mathbf{q}_1^T \\ \mathbf{q}_2^T \\ \vdots \\ \mathbf{q}_n^T \end{bmatrix} \\
&= \lambda_1\mathbf{q}_1\mathbf{q}_1^T + \lambda_2\mathbf{q}_2\mathbf{q}_2^T + \cdots + \lambda_n\mathbf{q}_n\mathbf{q}_n^T \\
&= \sum_{i=1}^{n} \lambda_i \mathbf{q}_i \mathbf{q}_i^T
\end{align*}
$$

{% endraw %}

##### **範例：使用前面的矩陣進行譜分解**

回到我們先前的例子

{% raw %}

$$A = \begin{bmatrix} 2 & -2 \\ -2 & 5 \end{bmatrix}$$

{% endraw %}

**已知：**

- 特徵值：$\lambda_1 = 1$，$\lambda_2 = 6$
- 單位特徵向量：{% raw %} $\mathbf{q}_1 = \begin{bmatrix} \frac{2}{\sqrt{5}} \\ \frac{1}{\sqrt{5}} \end{bmatrix}$，$\mathbf{q}_2 = \begin{bmatrix} \frac{1}{\sqrt{5}} \\ \frac{-2}{\sqrt{5}} \end{bmatrix}$ {% endraw %}

**計算：**

{% raw %}

$$\mathbf{q}_1 \mathbf{q}_1^T = \begin{bmatrix} \frac{2}{\sqrt{5}} \\ \frac{1}{\sqrt{5}} \end{bmatrix} \begin{bmatrix} \frac{2}{\sqrt{5}} & \frac{1}{\sqrt{5}} \end{bmatrix} = \begin{bmatrix} \frac{4}{5} & \frac{2}{5} \\ \frac{2}{5} & \frac{1}{5} \end{bmatrix}$$

$$\mathbf{q}_2 \mathbf{q}_2^T = \begin{bmatrix} \frac{1}{\sqrt{5}} \\ \frac{-2}{\sqrt{5}} \end{bmatrix} \begin{bmatrix} \frac{1}{\sqrt{5}} & \frac{-2}{\sqrt{5}} \end{bmatrix} = \begin{bmatrix} \frac{1}{5} & \frac{-2}{5} \\ \frac{-2}{5} & \frac{4}{5} \end{bmatrix}$$

{% endraw %}

**譜分解：**

{% raw %}

$$
\begin{align*}
A &= \lambda_1 \mathbf{q}_1 \mathbf{q}_1^T + \lambda_2 \mathbf{q}_2 \mathbf{q}_2^T \\
&= 1 \cdot \begin{bmatrix} \frac{4}{5} & \frac{2}{5} \\ \frac{2}{5} & \frac{1}{5} \end{bmatrix} + 6 \cdot \begin{bmatrix} \frac{1}{5} & \frac{-2}{5} \\ \frac{-2}{5} & \frac{4}{5} \end{bmatrix} \\
&= \begin{bmatrix} \frac{4}{5} & \frac{2}{5} \\ \frac{2}{5} & \frac{1}{5} \end{bmatrix} + \begin{bmatrix} \frac{6}{5} & \frac{-12}{5} \\ \frac{-12}{5} & \frac{24}{5} \end{bmatrix} \\
&= \begin{bmatrix} \frac{4}{5} + \frac{6}{5} & \frac{2}{5} - \frac{12}{5} \\ \frac{2}{5} - \frac{12}{5} & \frac{1}{5} + \frac{24}{5} \end{bmatrix} \\
&= \begin{bmatrix} 2 & -2 \\ -2 & 5 \end{bmatrix}
\end{align*}
$$

{% endraw %}

## 相關連結

[特徵向量與特徵值](/2025/09/20/特徵向量與特徵值/)
[轉置矩陣](/2025/09/27/轉置矩陣)
[正交矩陣](/2025/10/06/正交矩陣/)
[矩陣對角化](/2025/10/07/矩陣對角化/)

## 參考資料

[wiki - 對稱矩陣](https://zh.wikipedia.org/zh-tw/%E5%B0%8D%E7%A8%B1%E7%9F%A9%E9%99%A3)
[李宏毅_Linear Algebra Lecture 33: Symmetric Matrix](https://www.youtube.com/watch?v=0ijUQ-RfN3I)
[通俗易懂：对称矩阵](https://zhuanlan.zhihu.com/p/685078086)
[通俗易懂：正交对角化](https://zhuanlan.zhihu.com/p/685168691)
