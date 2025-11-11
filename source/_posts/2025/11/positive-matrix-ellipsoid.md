---
title: 正定矩陣與半正定矩陣 (2) - 橢球體的形狀
date: 2025-11-12 00:23:19
updated: 2025-11-12 00:23:19
categories: 線性代數
mathjax: true
---

## 前言

在上一篇文章中，我們討論了正定矩陣的定義和性質。這次我們要探討正定矩陣在幾何上的意義：它們如何描述 **橢球體(ellipsoid)** 的形狀，透過二次型 $\mathbf{x}^T \mathbf{A} \mathbf{x} = 1$，我們可以將線性代數與橢球體的幾何圖形連結起來

## 橢球體的數學表示

對於一個 $n \times n$ 的正定對稱矩陣 $\mathbf{A}$，方程式

$$
\mathbf{x}^T \mathbf{A} \mathbf{x} = 1
$$

定義了一個在 $n$ 維空間中的**橢球體**（或在二維空間中的橢圓）

這個簡單的方程式蘊含了豐富的幾何資訊，包括：

- 橢球體的形狀和大小
- 各個主軸的方向
- 各個主軸的長度

下面我們用各種例子來探討二次型 $\mathbf{x}^T \mathbf{A} \mathbf{x} = 1$ 是如何代表橢球體的幾何圖形

<!-- more -->

## 圓形：最簡單的情況

讓我們從最簡單的情況開始，考慮二維平面上的單位矩陣：

{% raw %}

$$
\mathbf{A} = \mathbf{I}_2 = \begin{bmatrix} 1 & 0 \\ 0 & 1 \end{bmatrix}
$$

{% endraw %}

此時，方程式變成：

$$
\mathbf{x}^T \mathbf{I}_2 \mathbf{x} = x_1^2 + x_2^2 = 1
$$

這是一個 **半徑為 1 的圓**

在這個特殊情況下：

- 所有方向都是對稱的
- 兩個主軸長度相等，都是 1
- 主軸方向沿著標準座標軸（$x_1$ 和 $x_2$ 軸）

## 圓球體：三維的對稱

推廣到三維空間，考慮 $3 \times 3$ 單位矩陣：

{% raw %}

$$
\mathbf{A} = \mathbf{I}_3 = \begin{bmatrix} 1 & 0 & 0 \\ 0 & 1 & 0 \\ 0 & 0 & 1 \end{bmatrix}
$$

{% endraw %}

方程式變成：

$$
\mathbf{x}^T \mathbf{I}_3 \mathbf{x} = x_1^2 + x_2^2 + x_3^2 = 1
$$

這是一個 **半徑為 1 的圓球**

同樣地：

- 所有方向完全對稱
- 三個主軸長度相等，都是 1
- 主軸方向沿著標準座標軸（$x_1$、$x_2$ 和 $x_3$ 軸）

## 橢球體：不同的軸長

現在讓我們來看另一個橢球體的主軸不同長度的例子，考慮對角矩陣：

{% raw %}

$$
\mathbf{A} = \begin{bmatrix} 4 & 0 & 0 \\ 0 & 1 & 0 \\ 0 & 0 & \frac{1}{9} \end{bmatrix}
$$

{% endraw %}

展開 $\mathbf{x}^T \mathbf{A} \mathbf{x} = 1$：

$$
4x_1^2 + x_2^2 + \frac{1}{9}x_3^2 = 1
$$

我們可以改寫成：

$$
\frac{x_1^2}{(1/2)^2} + \frac{x_2^2}{1^2} + \frac{x_3^2}{3^2} = 1
$$

這是一個**沒有旋轉的橢球體**，其主軸沿著座標軸方向：

- $x_1$ 方向的半軸長：$\frac{1}{\sqrt{4}} = \frac{1}{2}$
- $x_2$ 方向的半軸長：$\frac{1}{\sqrt{1}} = 1$
- $x_3$ 方向的半軸長：$\frac{1}{\sqrt{1/9}} = 3$

這時我們可以發現，其半軸長度是 **矩陣對角元素(特徵值)** 平方根的倒數

<img src="same-axis.png" width="80%" height="80%">

[圖片程式碼](https://github.com/bcjohnblue/python-plot/blob/main/2025/11/ellipsoid/same-axis.py)

## 旋轉的橢圓

接著讓我們來看看二維空間中旋轉的橢圓圖形。考慮：

{% raw %}

$$
\mathbf{A} = \begin{bmatrix} 5 & 4 \\ 4 & 5 \end{bmatrix}
$$

{% endraw %}

由於 $\mathbf{A}$ 是對稱矩陣，我們可以對其做 [正交對角化](/2025/10/15/對稱矩陣/#4-1-正交對角化)：

首先寫下特徵多項式：

{% raw %}

$$
\det(\mathbf{A} - \lambda \mathbf{I}) =
\begin{vmatrix}
5 - \lambda & 4 \\
4 & 5 - \lambda
\end{vmatrix}
= (5-\lambda)^2 - 16 = \lambda^2 - 10\lambda + 9 = 0
$$

{% endraw %}

可以求出特徵值 $\lambda_{1} = 9$ 與 $\lambda_{2} = 1$，接著求對應的單位特徵向量：

{% raw %}

$$
\mathbf{v}_{1} = \frac{1}{\sqrt{2}}
\begin{bmatrix}
1 \\ 1
\end{bmatrix} \qquad
\mathbf{v}_{2} = \frac{1}{\sqrt{2}}
\begin{bmatrix}
1 \\ -1
\end{bmatrix}
$$

{% endraw %}

把特徵向量排成正交矩陣 $\mathbf{Q}$，特徵值放入對角矩陣 $\mathbf{D}$：

{% raw %}

$$
\mathbf{Q} =
\begin{bmatrix}
\frac{1}{\sqrt{2}} & \frac{1}{\sqrt{2}} \\
\frac{1}{\sqrt{2}} & -\frac{1}{\sqrt{2}}
\end{bmatrix} \qquad
\mathbf{D} =
\begin{bmatrix}
9 & 0 \\
0 & 1
\end{bmatrix}
$$

{% endraw %}

即可得到正交對角化：

{% raw %}

$$
\mathbf{A} = \mathbf{Q} \mathbf{D} \mathbf{Q}^T =
\begin{bmatrix}
\frac{1}{\sqrt{2}} & \frac{1}{\sqrt{2}} \\
\frac{1}{\sqrt{2}} & -\frac{1}{\sqrt{2}}
\end{bmatrix}
\begin{bmatrix}
9 & 0 \\
0 & 1
\end{bmatrix}
\begin{bmatrix}
\frac{1}{\sqrt{2}} & \frac{1}{\sqrt{2}} \\
\frac{1}{\sqrt{2}} & -\frac{1}{\sqrt{2}}
\end{bmatrix}^T
$$

{% endraw %}

接著考慮橢球體的數學表示 $\mathbf{x}^T \mathbf{A} \mathbf{x} = 1$：

令 {% raw %} $\mathbf{x} = \begin{bmatrix} x_1 \\ x_2 \end{bmatrix}$ {% endraw %}，展開後可得：

{% raw %}

$$
\begin{align*}
\mathbf{x}^T \mathbf{A} \mathbf{x}
&= \begin{bmatrix} x_1 & x_2 \end{bmatrix}
\begin{bmatrix}
5 & 4 \\
4 & 5
\end{bmatrix}
\begin{bmatrix}
x_1 \\
x_2
\end{bmatrix} \\[6pt]
&= \begin{bmatrix} x_1 & x_2 \end{bmatrix}
\begin{bmatrix}
5x_1 + 4x_2 \\[6pt]
4x_1 + 5x_2
\end{bmatrix} \\[6pt]
&= x_1(5x_1 + 4x_2) + x_2(4x_1 + 5x_2) \\[6pt]
&= 5x_1^2 + 8x_1x_2 + 5x_2^2 = 1 \\[6pt]
\end{align*}
$$

{% endraw %}

同樣地，我們使用 $\mathbf{A} = \mathbf{Q} \mathbf{D} \mathbf{Q}^T$ 將二次型 $\mathbf{x}^T \mathbf{A} \mathbf{x}$ 展開：

{% raw %}

$$
\begin{align*}
\mathbf{x}^T \mathbf{A} \mathbf{x}
&= \mathbf{x}^T \mathbf{Q} \mathbf{D} \mathbf{Q}^T \mathbf{x} \\[6pt]
&= (\mathbf{Q}^T \mathbf{x})^T \mathbf{D} (\mathbf{Q}^T \mathbf{x})
\end{align*}
$$

{% endraw %}

令 $\mathbf{y} = \mathbf{Q}^T \mathbf{x}$，其中

{% raw %}

$$
\mathbf{Q}^T = \frac{1}{\sqrt{2}}
\begin{bmatrix}
1 & 1 \\
1 & -1
\end{bmatrix}
\qquad
\mathbf{y} =
\begin{bmatrix}
\frac{x_1 + x_2}{\sqrt{2}} \\
\frac{x_1 - x_2}{\sqrt{2}}
\end{bmatrix}
$$

{% endraw %}

令 {% raw %} $\mathbf{y} = \begin{bmatrix} y_1 \\ y_2 \end{bmatrix}$ {% endraw %}，可得：

{% raw %}

$$
\begin{align*}
\mathbf{x}^T \mathbf{A} \mathbf{x}
&= \mathbf{y}^T \mathbf{D} \mathbf{y} \\[6pt]
&= \begin{bmatrix} y_1 & y_2 \end{bmatrix}
\begin{bmatrix}
9 & 0 \\
0 & 1
\end{bmatrix}
\begin{bmatrix}
y_1 \\
y_2
\end{bmatrix} \\[6pt]
&= 9 y_1^2 + 1 \cdot y_2^2 \\[6pt]
&=\frac{y_1^2}{(1/3)^2} + \frac{y_2^2}{1^2} = 1 \\[6pt]
\end{align*}
$$

{% endraw %}

### 座標變換與對角化

從上面的推導可以看出，原本複雜的二次型 $5x_1^2 + 8x_1x_2 + 5x_2^2 = 1$ 實際上只是標準形式 $9 y_1^2 + 1 \cdot y_2^2 = 1$ 經過座標轉換 $\mathbf{y} = \mathbf{Q}^T \mathbf{x}$ 後的結果。兩者描述的是**同一個橢圓體**，只是在不同座標系下的表示方式不同：在 $(x_1, x_2)$ 座標系中，橢圓的主軸是旋轉的；而在 $(y_1, y_2)$ 座標系中，橢圓的主軸與座標軸對齊

$(x_1, x_2)$ 座標系
<img src="rotate-axis-x.png" width="80%" height="80%">

[圖片程式碼](https://github.com/bcjohnblue/python-plot/blob/main/2025/11/ellipsoid/rotate-axis-x.py)

<br />

$(y_1, y_2)$ 座標系
<img src="rotate-axis-y.png" width="80%" height="80%">

[圖片程式碼](https://github.com/bcjohnblue/python-plot/blob/main/2025/11/ellipsoid/rotate-axis-y.py)

## 總結

對於任意正定對稱矩陣 $\mathbf{A} = \mathbf{Q} \mathbf{D} \mathbf{Q}^T$，方程式 $\mathbf{x}^T \mathbf{A} \mathbf{x} = 1$ 定義的橢球體具有以下性質：

### 1. 主軸方向

橢球體的**主軸方向**由 $\mathbf{A}$ 的**特徵向量**決定：

- 在原始的 $\mathbf{x}$ 座標系中，這些特徵向量指出橢球體主軸的方向

### 2. 主軸長度

橢球體的**主軸長度**由 $\mathbf{A}$ 的**特徵值**決定：

$$
\text{第 } i \text{ 個主軸的半徑} = \frac{1}{\sqrt{\lambda_i}}
$$

- **特徵值越大** → 對應方向的主軸半徑**越短**
- **特徵值越小** → 對應方向的主軸半徑**越長**

### 3. 座標變換

通過旋轉變換 $\mathbf{y} = \mathbf{Q}^T \mathbf{x}$，複雜的二次型：

$$
\mathbf{x}^T \mathbf{A} \mathbf{x} = 1
$$

可以簡化為標準形式：

$$
\mathbf{y}^T \mathbf{D} \mathbf{y} = \lambda_1 y_1^2 + \lambda_2 y_2^2 + \cdots + \lambda_n y_n^2 = 1
$$

即：

$$
\frac{y_1^2}{(1/\sqrt{\lambda_1})^2} + \frac{y_2^2}{(1/\sqrt{\lambda_2})^2} + \cdots + \frac{y_n^2}{(1/\sqrt{\lambda_n})^2} = 1
$$

這正是 $n$ 維橢球體的標準方程式

## 相關連結

[正定矩陣與半正定矩陣 (1) - 定義與性質](/2025/11/03/正定矩陣與半正定矩陣-1-定義與性質/)
[對稱矩陣](/2025/10/15/對稱矩陣/)

## 參考資料

[陳晏笙老師開放課程：單元 14．特徵值與特徵向量-正定矩陣](https://youtu.be/BWhmlvI75W0?si=sJMfdy_m2M-x9301&t=5521)
