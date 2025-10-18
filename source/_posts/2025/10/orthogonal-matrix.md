---
title: 正交矩陣
date: 2025-10-06 23:04:35
updated: 2025-10-18 23:04:35
categories: 線性代數
mathjax: true
---

## 定義

對於 $n \times n$ 實矩陣 $Q$，如果滿足：

$$Q^T Q = Q Q^T = I$$

其中 $I$ 是 $n \times n$ 單位矩陣，則稱 $Q$ 為**正交矩陣**

## 例子

{% raw %}

$$
Q_1 = \begin{bmatrix}
\cos\theta & -\sin\theta \\
\sin\theta & \cos\theta
\end{bmatrix}
$$

$$
Q_2 = \begin{bmatrix}
1 & 0 \\
0 & -1
\end{bmatrix}
$$

$$
Q_3 = \begin{bmatrix}
\frac{1}{\sqrt{2}} & \frac{1}{\sqrt{2}} \\
-\frac{1}{\sqrt{2}} & \frac{1}{\sqrt{2}}
\end{bmatrix}
$$

{% endraw %}

<!-- more -->

## 基本性質

### 1. 逆矩陣性質

對於正交矩陣 $Q$：

$$Q^{-1} = Q^T$$

**證明：**
由定義 $Q^T Q = I$，根據逆矩陣的定義，$Q^T$ 就是 $Q$ 的逆矩陣

### 2. 行列式性質

正交矩陣的行列式滿足：

$$|\det(Q)| = 1$$

即 $\det(Q) = \pm 1$

**證明：**
由於 $Q^T Q = I$，所以：
$$\det(Q^T Q) = \det(I) = 1$$

另一方面根據 [行列式的乘法定理](https://zh.wikipedia.org/zh-tw/%E8%A1%8C%E5%88%97%E5%BC%8F#:~:text=%7C-,%E8%A1%8C%E5%88%97%E5%BC%8F%E7%9A%84%E4%B9%98%E6%B3%95%E5%AE%9A%E7%90%86,-%EF%BC%9A%E6%96%B9%E5%A1%8A%E7%9F%A9%E9%99%A3)：

$$
\det(Q^T Q)=\det(Q^T)\det(Q)
$$

根據 [轉置不改變行列式的性質](https://zh.wikipedia.org/zh-tw/%E8%A1%8C%E5%88%97%E5%BC%8F#:~:text=%E4%B8%80%E5%80%8B%E7%9F%A9%E9%99%A3%E7%9A%84%E8%A1%8C%E5%88%97%E5%BC%8F%E7%AD%89%E6%96%BC%E5%AE%83%E7%9A%84%E8%BD%89%E7%BD%AE%E7%9F%A9%E9%99%A3%E7%9A%84%E8%A1%8C%E5%88%97%E5%BC%8F)，上式變為：

$$
\det(Q^T Q)=\det(Q^T)\det(Q)=\det(Q)\det(Q)=\det(Q)^2
$$

可求得 $\det(Q)^2 = 1$，證明 $|\det(Q)| = 1$

### 3. 保持向量長度

正交矩陣保持向量的歐幾里得長度：

$$\|Q\mathbf{x}\| = \|\mathbf{x}\|$$

**證明：**

根據 [內積的矩陣乘法形式](/2025/10/18/向量內積/#定義二：矩陣乘法形式)：

$$
x \cdot y = x^Ty \quad\Rightarrow\quad |x|^2 = x^Tx
$$

所以：

$$\|Q\mathbf{x}\|^2 = (Q\mathbf{x})^T (Q\mathbf{x})$$

根據 [矩陣乘法的轉置](/2025/09/27/轉置矩陣/#4-矩陣乘法的轉置)：

$$(Q\mathbf{x})^T (Q\mathbf{x}) = \mathbf{x}^T Q^T Q \mathbf{x} = \mathbf{x}^T I \mathbf{x} = \mathbf{x}^T \mathbf{x} = \|\mathbf{x}\|^2$$

因此可得 $\|Q\mathbf{x}\| = \|\mathbf{x}\|$

### 4. 保持向量夾角

正交矩陣保持兩個向量之間的夾角：

$$\cos\theta = \frac{\mathbf{u} \cdot \mathbf{v}}{\|\mathbf{u}\|\|\mathbf{v}\|} = \frac{Q\mathbf{u} \cdot Q\mathbf{v}}{\|Q\mathbf{u}\|\|Q\mathbf{v}\|}$$

**證明：**
分母的部分可由上面 [正交矩陣保持向量長度特性](./#3-保持向量長度) 證明

分子則可證明如下：

$$Q\mathbf{u} \cdot Q\mathbf{v} = (Q\mathbf{u})^T (Q\mathbf{v}) = \mathbf{u}^T Q^T Q \mathbf{v} = \mathbf{u}^T I \mathbf{v} = \mathbf{u} \cdot \mathbf{v}$$

最終證明兩個向量都乘以正交矩陣後，夾角保持不變

### 5. 列向量性質

正交矩陣的列向量構成 [標準正交基](./#1-標準正交基)：

- 每個列向量的長度為 1
- 不同列向量之間互相正交

### 6. 行向量性質

正交矩陣的行向量也構成 [標準正交基](./#1-標準正交基)：

- 每個行向量的長度為 1
- 不同行向量之間互相正交

## 補充資料

### 1. 標準正交基

**定義：**
在 $n$ 維向量空間中，如果一組向量 $\{\mathbf{e}_1, \mathbf{e}_2, \ldots, \mathbf{e}_n\}$ 滿足：

1. **正交性**：任意兩個不同向量互相正交
   $$\mathbf{e}_i \cdot \mathbf{e}_j = 0 \quad \text{當 } i \neq j$$

2. **單位長度**：每個向量的長度為 1
   $$\|\mathbf{e}_i\| = 1 \quad \text{對所有 } i$$

則稱這組向量為 **標準正交基(orthonormal basis)**

**例子：**

1. **二維標準正交基：**
   {% raw %}
   $$\mathbf{e}_1 = \begin{bmatrix} 1 \\ 0 \end{bmatrix}, \\ \mathbf{e}_2 = \begin{bmatrix} 0 \\ 1 \end{bmatrix}$$
   {% endraw %}

2. **三維標準正交基：**
   {% raw %}
   $$\mathbf{e}_1 = \begin{bmatrix} 1 \\ 0 \\ 0 \end{bmatrix}, \\ \mathbf{e}_2 = \begin{bmatrix} 0 \\ 1 \\ 0 \end{bmatrix}, \\ \mathbf{e}_3 = \begin{bmatrix} 0 \\ 0 \\ 1 \end{bmatrix}$$
   {% endraw %}

3. **旋轉後的標準正交基：**
   {% raw %}
   $$\mathbf{e}_1 = \begin{bmatrix} \frac{1}{\sqrt{2}} \\ \frac{1}{\sqrt{2}} \end{bmatrix}, \\ \mathbf{e}_2 = \begin{bmatrix} -\frac{1}{\sqrt{2}} \\ \frac{1}{\sqrt{2}} \end{bmatrix}$$
   {% endraw %}

## 相關連結

[轉置矩陣](/2025/09/27/轉置矩陣/)
[向量內積](/2025/10/18/向量內積/)

## 參考資料

[wiki - 正交矩陣](https://zh.wikipedia.org/zh-tw/%E6%AD%A3%E4%BA%A4%E7%9F%A9%E9%99%A3)
[通俗易懂：什么是正交矩阵](https://zhuanlan.zhihu.com/p/684677360)
