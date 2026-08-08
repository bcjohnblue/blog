---
title: Jacobian 矩陣 (1) - 定義
date: 2026-08-08 22:29:55
updated: 2026-08-08 22:29:55
categories: Math
mathjax: true
---

###### 此為 **Jacobian 矩陣** 系列文章 - 第 1 篇：

1. <a href="/2026/08/08/jacobian-矩陣-1-定義/" target="_blank">Jacobian 矩陣 (1) - 定義</a>
2. <a href="/2026/08/08/jacobian-矩陣-2-在-ewa-volume-splatting-中的應用/" target="_blank">Jacobian 矩陣 (2) - 在 EWA Volume Splatting 中的應用</a>

## 前言

在微積分中，函數的變化率稱為「導數」

- 單變數函數 $f(x)$ 的導數，是一個數字
- 多變數函數 $f(x, y)$ 的導數，是一個向量（梯度）

那如果函數同時有多個輸入與多個輸出呢？這時，我們將所有的偏導數排列在一起，就會形成一個矩陣，這就是 **Jacobian 矩陣 (雅可比矩陣)**，簡單來說 **Jacobian 矩陣就是「多變數向量函數的導數」**，它能幫助我們掌握複雜函數在多維空間中的變化率

<!-- more -->

## 定義

假設有一個函數 $\mathbf{f} : \mathbb{R}^n \to \mathbb{R}^m$，它的所有一階偏導數在 $\mathbb{R}^n$ 中皆存在。這個函數接受一個向量 $\mathbf{x} = (x_1, \dots, x_n) \in \mathbb{R}^n$ 作為輸入，並產生一個向量 $\mathbf{f}(\mathbf{x}) = (f_1(\mathbf{x}), \dots, f_m(\mathbf{x})) \in \mathbb{R}^m$ 作為輸出：

{% raw %}

$$
\mathbf{f}(\mathbf{x}) =
\begin{bmatrix}
f_1(x_1, \dots, x_n) \\
f_2(x_1, \dots, x_n) \\
\vdots \\
f_m(x_1, \dots, x_n)
\end{bmatrix}
$$

{% endraw %}

則函數 $\mathbf{f}$ 的 **Jacobian 矩陣**（記為 $\mathbf{J_f}$）是一個 $m \times n$ 的矩陣，其第 $(i, j)$ 個元素為 $\frac{\partial f_i}{\partial x_j}$。具體展開如下：

{% raw %}

$$
\mathbf{J_f} =
\begin{bmatrix}
\dfrac{\partial f_1}{\partial x_1} & \dfrac{\partial f_1}{\partial x_2} & \dots & \dfrac{\partial f_1}{\partial x_n} \\[0.8em]
\dfrac{\partial f_2}{\partial x_1} & \dfrac{\partial f_2}{\partial x_2} & \dots & \dfrac{\partial f_2}{\partial x_n} \\[0.8em]
\vdots & \vdots & \ddots & \vdots \\[0.8em]
\dfrac{\partial f_m}{\partial x_1} & \dfrac{\partial f_m}{\partial x_2} & \dots & \dfrac{\partial f_m}{\partial x_n}
\end{bmatrix}
$$

{% endraw %}

Jacobian 矩陣有許多種常見的表示符號，除了 $\mathbf{J_f}$ 之外，還常寫作 $\displaystyle D\mathbf{f}$、$\displaystyle \nabla \mathbf{f}$ 或 $\displaystyle \frac{\partial (f_1, \dots, f_m)}{\partial (x_1, \dots, x_n)}$

## Jacobian 矩陣與泰勒展開

要深刻理解 Jacobian 矩陣的本質，最好的切入點是回顧微積分中極為重要的「泰勒展開式 (Taylor Expansion)」

### 單變數的泰勒展開與局部線性近似

泰勒展開式的核心思想是：**用多項式來逼近一個複雜的函數**。對於一個在點 $p$ 可微的單變數函數 $f(x)$，它在 $p$ 附近的一階泰勒展開式為：

{% raw %}

$$
f(x) \approx f(p) + f'(p)(x - p)
$$

{% endraw %}

這個公式在幾何上的意義稱為 **「局部線性近似 (Local Linear Approximation)」**。它告訴我們，當我們把函數圖形在 $p$ 點無限放大時，曲線看起來會越來越像一條直線，而這條直線（切線）的斜率就是一階導數 $f'(p)$。只要輸入的微小變化量 $\Delta x = (x - p)$ 足夠小，輸出的變化量 $\Delta y = f(x) - f(p)$ 就可以簡單地用乘法 $f'(p) \Delta x$ 來估算。

![Taylor Expansion](./taylor-expansion.webp)
[圖片來源](https://64.media.tumblr.com/d8ce7d45df6eacfdc1086ebc296aaf11/tumblr_mynd25NdlI1suhdw4o1_1280.gifv)

### 向量函數的泰勒展開與 Jacobian 矩陣

現在，如果我們把眼光放到多變數的向量函數 $\mathbf{f}(\mathbf{x})$（輸入是 $\mathbb{R}^n$ 中的向量 $\mathbf{x}$，輸出是 $\mathbb{R}^m$ 中的向量）。我們同樣希望能對它進行「局部線性近似」。

這時，單變數公式中的純量乘法 $f'(p)(x - p)$，就會自然地擴展為**矩陣乘法**。而這個扮演「一階導數」角色、負責進行線性映射的矩陣，正是 Jacobian 矩陣 $\mathbf{J_f}(\mathbf{p})$。

向量函數 $\mathbf{f}$ 在點 $\mathbf{p}$ 附近的一階泰勒展開式為：

{% raw %}

$$
\mathbf{f}(\mathbf{x}) \approx \mathbf{f}(\mathbf{p}) + \mathbf{J_f}(\mathbf{p})(\mathbf{x} - \mathbf{p})
$$

{% endraw %}

### 結論

Jacobian 矩陣 $\mathbf{J_f}$ 其實就是向量函數 $\mathbf{f}$ 的「一階導數」。不管原本的向量函數在空間中如何複雜地扭曲、形變，在 $\mathbf{p}$ 點極小的局部範圍內，這個變換都可以被視為一個簡單的**線性變換**。當輸入產生微小位移向量 $\Delta \mathbf{x} = (\mathbf{x} - \mathbf{p})$ 時，我們只要把它乘上 Jacobian 矩陣，就能得到輸出端相對應的微小位移向量 $\Delta \mathbf{y}$。

### Jacobian 行列式 (Jacobian Determinant)

當 Jacobian 矩陣是一個方陣（即 $m = n$）時，我們可以計算它的行列式，稱為 **Jacobian 行列式 (Jacobian Determinant)**，常簡記為 $\det(J)$ 或 $\frac{\partial(f_1, \dots, f_n)}{\partial(x_1, \dots, x_n)}$。

Jacobian 行列式有一個極其重要的幾何意義：**它代表了在該點附近空間經過映射後的「面積或體積縮放比例」 (Local Volume Expansion Factor)。**

- 如果 $\det(J) > 0$，代表局部空間在變換後保持了相同的方向性 (Orientation-preserving)。
- 如果 $\det(J) < 0$，代表局部空間的方向被翻轉了 (例如鏡像反射)。
- 絕對值 $|\det(J)|$ 就代表了原本微小的一塊體積（或面積）在經過函數 $\mathbf{f}$ 變換後，被放大了幾倍。

這也是為什麼在微積分的「多重積分變數代換」中，我們必須乘上 Jacobian 行列式的絕對值。因為當座標系轉換時，微小的面積或體積元素的大小會發生改變，我們需要用 Jacobian 行列式來修正這個縮放比例。

## 計算範例

為了讓概念更清晰，讓我們來看看幾個具體的 Jacobian 矩陣計算範例

### 範例 1：一維座標的線性變換

首先先讓我們從最簡單的一維座標來體會 Jacobian 矩陣的作用

![](./jacobian_input_axis.png)

上圖中是 $f(x)=x$ 的一維座標，我們想知道點 $a$ 在經過線性變換後，與鄰近點間的距離會變多長，這裡我們挑出兩側的 1.4 與 1.6 觀察，在經過 $f(x)=x^2$ 的線性變換後可以得到下圖，原本的點轉換為：

{% raw %}

$
\begin{aligned}
a &: \color{red}{1.5} \Rightarrow \color{red}{2.25} \\
\text{左側} &: \color{#00BFFF}{1.4} \Rightarrow \color{#00BFFF}{1.96} \\
\text{右側} &: \color{#00BFFF}{1.6} \Rightarrow \color{#00BFFF}{2.56}
\end{aligned}
$

{% endraw %}

<br />
<br />

![](./jacobian_output_axis.png)

此時我們可以觀察到原本 1.5 與鄰近點間的距離是一個單位 (0.1)，在經由線性變換後與鄰近點間的距離差不多是三個單位，也就是說點 $a$ 在套用 $f(x)=x^2$ 的線性變換後，與鄰近點間的距離會變為 3 倍左右

那這個 3 倍是怎麼算出來的呢？我們可以用 Jacobian 行列式的方式算出來，$f(x)=x^2$ 對 $x$ 取導數後是 $2x$，代入點 1.5 的位置時算出來就是 3 了！這代表了 Jacobian 行列式在一維空間的行為，它衡量了點在經過變換後與附近點的長度縮放比例，數學上表示為：

$$
\det J_f(a)=f'(a)
$$

在這個例子中：

$$
f(x)=x^2,\quad f'(x)=2x
$$

當 $a=1.5$ 時：

$$
\det J_f(1.5)=f'(1.5)=2\times1.5=3
$$

### 範例 2：極座標與直角座標的轉換

接著看一個經典的 2D $\to$ 2D 映射範例。假設我們要計算半徑為 $1$ 的圓盤區域的積分，我們知道圓的方程式為：

$$
x^2+y^2\le 1
$$

如果一開始直接用直角座標 $(x,y)$ 來寫，積分上下限會變成根號函數，形式很複雜：

{% raw %}

$$
\iint_D f(x,y)\,dx\,dy
=
\int_{-1}^{1}\int_{-\sqrt{1-x^2}}^{\sqrt{1-x^2}} f(x,y)\,dy\,dx
$$

{% endraw %}

此時我們可以改用極座標，將區域改寫成更簡潔的範圍：

{% raw %}

$$
\begin{aligned}
&0\le r\le 1 \\[0.6em]
&0\le \theta\le 2\pi
\end{aligned}
$$

{% endraw %}

此時的座標由原本的 $(x, y)$ 轉換成以 $(r, \theta)$ 表示，數學式表示為：

{% raw %}

$$
g(r, \theta) = (x, y) = (r \cos\theta, r \sin\theta)
$$

{% endraw %}

![](./polar_1.png)
[圖片來源](https://www.youtube.com/watch?v=wCZ1VEmVjVo)

而原本以 **直角座標 $(x,y)$ 的積分** 可以改為以 **極座標 $(r, \theta)$** 的方式表示：

{% raw %}

$$
\iint_D f(x,y)\,dx\,dy
=
\iint_T f\!\big(g(r,\theta)\big)\,|det(J)|\,dr\,d\theta
$$

{% endraw %}

需要注意的是，座標轉換後必須額外乘上 $|\det(J)|$ (也就是 $\det(J)$ 的絕對值)，它表示面積元素在轉換過程中的局部縮放比例，這個概念其實跟範例 1. 在點 1.5 時長度放大了 3 倍一樣，差別是目前求的是二維座標下的 $\det(J)$

在計算 $\det(J)$ 前，我們可以從幾何的角度來了解 Jacobian 行列式在二維中的物理意義，下圖右側顯示在 $(r,\theta)$ 平面上，一個微小區塊圍出的矩形面積是 $dr d\theta$，而當我們將其以函式 $g$ 映射到圓形的 $(x,y)$ 平面後：

{% raw %}

$$
x = r\cos\theta, \quad y = r\sin\theta
$$

{% endraw %}

如下圖左側所示，映射到 $(x,y)$ 平面後，會變成一個微小扇形區塊，它的面積約為 $rdrd\theta$，因此 Jacobian 行列式代表的面積縮放比例為：

{% raw %}

$$
\det(J)
=
\frac{r\,dr\,d\theta}{dr\,d\theta}
=
r
$$

{% endraw %}

從幾何的觀點出發，這告訴我們 - **極座標下每一小塊面積在轉換到直角座標後，都會被放大成原本的 $r$ 倍**

![](./polar_2.png)
[圖片來源](https://www.youtube.com/watch?v=wCZ1VEmVjVo)

接著我們來實際計算 Jacobian 行列式，看看是不是就如上面演示的等於 $r$，根據定義其 Jacobian 矩陣為：

{% raw %}

$$
\displaystyle
J =
\begin{bmatrix}
\displaystyle\frac{\partial x}{\partial r} & \displaystyle\frac{\partial x}{\partial \theta} \\
\displaystyle\frac{\partial y}{\partial r} & \displaystyle\frac{\partial y}{\partial \theta}
\end{bmatrix}
$$

{% endraw %}

逐項計算偏導數：

- $\displaystyle\frac{\partial x}{\partial r} = \cos\theta$
- $\displaystyle\frac{\partial x}{\partial \theta} = -r \sin\theta$
- $\displaystyle\frac{\partial y}{\partial r} = \sin\theta$
- $\displaystyle\frac{\partial y}{\partial \theta} = r \cos\theta$

將這些結果代入，得到 Jacobian 矩陣：

{% raw %}

$$
J =
\begin{bmatrix}
\cos\theta & -r \sin\theta \\
\sin\theta & r \cos\theta
\end{bmatrix}
$$

{% endraw %}

接下來，我們計算 $\det(J)$：

{% raw %}

$$
\det(J) = (\cos\theta)(r \cos\theta) - (-r \sin\theta)(\sin\theta) \\
= r \cos^2\theta + r \sin^2\theta \\
= r (\cos^2\theta + \sin^2\theta) \\
= r
$$

{% endraw %}

=> 如同幾何觀點出發的解釋，實際計算後代表面積縮放比例的 $\det(J)$ 一樣等於 $r$

最後我們用極座標來計算圓盤區域的面積：

{% raw %}

$$
\iint_D 1\,dx\,dy
=
\int_0^{2\pi}\int_0^R 1\cdot |\det(J)|\,dr\,d\theta
=
\int_0^{2\pi}\int_0^R r\,dr\,d\theta
$$

$$
=
\int_0^{2\pi}\left[\frac{1}{2}r^2\right]_0^R d\theta
=
\int_0^{2\pi}\frac{1}{2}R^2\,d\theta
=
\pi R^2
$$

{% endraw %}

就跟我們熟知圓形面積的公式是 $\pi R^2$ 一樣

### 範例 3：多維非線性函數 (3D 映射至 2D)

這次我們來看一個輸入維度和輸出維度不相等的例子。假設有一個從三維空間 $\mathbb{R}^3$ 映射到二維空間 $\mathbb{R}^2$ 的函數 $\mathbf{g}(x, y, z)$：

{% raw %}

$$
\mathbf{g}(x, y, z) =
\begin{bmatrix}
g_1(x, y, z) \\
g_2(x, y, z)
\end{bmatrix}
=
\begin{bmatrix}
x^2 y + z \\
e^x + y z^2
\end{bmatrix}
$$

{% endraw %}

因為輸入有 3 個變數 ($n=3$)，輸出有 2 個函數 ($m=2$)，所以它的 Jacobian 矩陣 $J$ 是一個 $2 \times 3$ 的非方陣：

{% raw %}

$$
J =
\begin{bmatrix}
\dfrac{\partial g_1}{\partial x} & \dfrac{\partial g_1}{\partial y} & \dfrac{\partial g_1}{\partial z} \\[0.8em]
\dfrac{\partial g_2}{\partial x} & \dfrac{\partial g_2}{\partial y} & \dfrac{\partial g_2}{\partial z}
\end{bmatrix}
$$

{% endraw %}

我們同樣逐項計算：

- 對於 $g_1(x, y, z) = x^2 y + z$：
  - $\dfrac{\partial g_1}{\partial x} = 2xy$
  - $\dfrac{\partial g_1}{\partial y} = x^2$
  - $\dfrac{\partial g_1}{\partial z} = 1$
- 對於 $g_2(x, y, z) = e^x + y z^2$：
  - $\dfrac{\partial g_2}{\partial x} = e^x$
  - $\dfrac{\partial g_2}{\partial y} = z^2$
  - $\dfrac{\partial g_2}{\partial z} = 2yz$

組合起來，得到的 Jacobian 矩陣為：

{% raw %}

$$
J =
\begin{bmatrix}
2xy & x^2 & 1 \\
e^x & z^2 & 2yz
\end{bmatrix}
$$

{% endraw %}

這個 $2 \times 3$ 矩陣沒有行列式，無法表示純粹的「體積縮放比例」（因為我們把三維空間壓縮成了二維平面，這已經不是體積對體積的映射了）。但它依然描述了這個非線性變換在空間中任意一點的局部線性變化率

## 結論

回顧一下這篇文章的重點：

1. **Jacobian 矩陣就是向量函數的「一階導數」**：把每個輸出分量對每個輸入變數的偏導數，排成一個 $m \times n$ 的矩陣
2. **它的核心意義是「局部線性近似」**：從泰勒展開的角度看，不管一個非線性函數整體上如何扭曲，在任何一點的極小範圍內，它的行為都可以用「乘上 Jacobian 矩陣」這樣一個簡單的線性變換來描述
3. **Jacobian 行列式代表局部的縮放比例**：當矩陣是方陣時，行列式的絕對值就是該點附近面積／體積被放大的倍數

其中第 2 點「把非線性函數在一點附近化成線性變換」，正是 Jacobian 矩陣在實際應用中最有威力的地方。[下一篇文章](/2026/08/08/jacobian-矩陣-2-在-ewa-volume-splatting-中的應用/) 我們就來看一個經典的應用 - **EWA Volume Splatting** 這篇論文如何利用 Jacobian 矩陣，把非線性的相機透視投影「局部線性化」，讓 3D 高斯能夠快速投影到 2D 螢幕上

## 參考資料

[What is Jacobian? | The right way of thinking derivatives and integrals](https://www.youtube.com/watch?v=wCZ1VEmVjVo)
[Jacobian matrix and determinant](https://en.wikipedia.org/wiki/Jacobian_matrix_and_determinant)
[Taylor series | Chapter 11, Essence of calculus](https://www.youtube.com/watch?v=3d6DsjIBzJ4)
[【直观详解】泰勒级数](https://charlesliuyx.github.io/2018/02/16/%E3%80%90%E7%9B%B4%E8%A7%82%E8%AF%A6%E8%A7%A3%E3%80%91%E6%B3%B0%E5%8B%92%E7%BA%A7%E6%95%B0/)
