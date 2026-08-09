---
title: 高斯分布 (4) - 邊緣化
date: 2026-08-07 22:00:00
updated: 2026-08-07 22:00:00
categories: Math
mathjax: true
---

###### 此為 **高斯分布** 系列文章 - 第 4 篇：

1. <a href="/2026/03/18/高斯分布-1-從共變異數矩陣出發/" target="_blank">高斯分布 (1) - 從共變異數矩陣出發</a>
2. <a href="/2026/03/18/高斯分布-2-一維到多維的分布形式/" target="_blank">高斯分布 (2) - 一維到多維的分布形式</a>
3. <a href="/2026/03/18/高斯分布-3-仿射變換後的性質變化/" target="_blank">高斯分布 (3) - 仿射變換後的性質變化</a>
4. <a href="/2026/08/07/高斯分布-4-邊緣化/" target="_blank">高斯分布 (4) - 邊緣化</a>

## 前言

前一篇文章我們討論的是「把整個高斯分佈做仿射變換 $\mathbf{Y} = A\mathbf{X} + \mathbf{b}$」會發生什麼事，結論是分佈仍然是高斯，且 $\mathbf{Y} \sim \mathcal{N}(A\boldsymbol{\mu} + \mathbf{b}, A\Sigma A^T)$。那時我們用 [二維高斯分布的仿射變換](/2026/03/18/高斯分布-3-仿射變換後的性質變化/#例子：二維高斯分布的仿射變換) 當作例子，體會了一個二維高斯分佈經過旋轉、平移之後，變成另一個二維高斯分佈的過程，但事實上高斯分佈不只能做到同維度的變換，甚至也能進行降維的變換，這個操作有個專有名稱叫做 **邊緣化 (Marginalization)**

## 邊緣化（Marginalization）

#### 定義：先把維度切成兩塊

邊緣化做的事就是 **降維**：把高斯分布的某些維度丟掉，只留下剩餘維度的分布。為了清楚描述「哪些維度要留、哪些要丟」，我們先把隨機向量 $\mathbf{X}$ 拆成兩個部分：要**保留的** $\mathbf{x}_a$（$p$ 維）與要**丟掉的** $\mathbf{x}_b$（$q$ 維）

{% raw %}

$$
\mathbf{X} =
\begin{bmatrix}
\mathbf{x}_a \\
\mathbf{x}_b
\end{bmatrix},
\quad
\boldsymbol{\mu} =
\begin{bmatrix}
\boldsymbol{\mu}_a \\
\boldsymbol{\mu}_b
\end{bmatrix},
\quad
\Sigma =
\begin{bmatrix}
\Sigma_{aa} & \Sigma_{ab} \\
\Sigma_{ba} & \Sigma_{bb}
\end{bmatrix}
$$

{% endraw %}

其中：

- $\Sigma_{aa}$（$p \times p$）：$\mathbf{x}_a$ 自己內部的共變異數
- $\Sigma_{bb}$（$q \times q$）：$\mathbf{x}_b$ 自己內部的共變異數
- $\Sigma_{ab} = \Sigma_{ba}^T$（$p \times q$）：兩塊之間的**耦合項**，描述 $\mathbf{x}_a$ 與 $\mathbf{x}_b$ 的相關性

接下來的邊緣化，關鍵就在於我們怎麼處理要丟掉的 {% raw %}$\mathbf{x}_b${% endraw %} 以及這個耦合項 {% raw %}$\Sigma_{ab}${% endraw %}

<!-- more -->

#### 連結：邊緣化與投影矩陣的關係

「丟掉 $\mathbf{x}_b$、只留下 $\mathbf{x}_a$」可以從抽樣的角度想像：從聯合分佈 $p(\mathbf{x}_a, \mathbf{x}_b)$ 抽出大量樣本，每個樣本點都同時帶有 $\mathbf{x}_a$ 與 $\mathbf{x}_b$ 兩部分的座標；現在我們只記錄每個樣本的 $\mathbf{x}_a$ 部分、完全無視 $\mathbf{x}_b$ 是多少，那麼這些「只剩 $\mathbf{x}_a$ 座標」的點所呈現出來的分佈，就叫做 $\mathbf{x}_a$ 的 **邊緣分佈 (Marginal distribution)** $p(\mathbf{x}_a)$

而「對每個樣本點丟掉 $\mathbf{x}_b$ 座標」寫成矩陣的語言，正是上一篇的仿射變換 $\mathbf{Y} = A\mathbf{X}$：取 $A$ 為 $p \times d$ 的投影矩陣（$I_p$ 是 $p \times p$ 的單位矩陣），變換後得到的 $\mathbf{Y}$ 就是 $\mathbf{x}_a$ 本身：

{% raw %}

$$
\mathbf{Y} = A\mathbf{X} =
\begin{bmatrix}
I_p & 0
\end{bmatrix}
\begin{bmatrix}
\mathbf{x}_a \\
\mathbf{x}_b
\end{bmatrix}
= \mathbf{x}_a
$$

{% endraw %}

用一個具體的例子來看：假設 $\mathbf{X}$ 是 3 維，我們要保留前 2 維、丟掉第 3 維（即 $p = 2$、$q = 1$），此時 $\mathbf{x}_a = (x_1, x_2)^T$、$\mathbf{x}_b = x_3$，而 $A$ 的左半邊是 $2 \times 2$ 的單位矩陣 $I_2$、右半邊補一行 $0$，把矩陣乘開：

{% raw %}

$$
\mathbf{Y} = A\mathbf{X} =
\begin{bmatrix}
1 & 0 & 0 \\
0 & 1 & 0
\end{bmatrix}
\begin{bmatrix}
x_1 \\
x_2 \\
x_3
\end{bmatrix}
=
\begin{bmatrix}
x_1 \\
x_2
\end{bmatrix}
= \mathbf{x}_a
$$

{% endraw %}

可以看到 $x_3$ 被 $A$ 的那行 $0$ 消掉了，投影後的 $\mathbf{Y}$ 剛好就是 $\mathbf{x}_a$

> **小結**：「丟掉某些維度」的邊緣化，其實就是仿射變換 $\mathbf{Y} = A\mathbf{X} + \mathbf{b}$ 的特例 - 取 $A$ 為「單位矩陣 $I_p$ 補上零」的投影矩陣，且 $\mathbf{b} = 0$

#### 推導：套用仿射變換的定理

既然邊緣化就是投影 $\mathbf{Y} = A\mathbf{X}$，而上一篇文章已經證明 [高斯分佈經過仿射變換後仍然服從高斯分佈](/2026/03/18/高斯分布-3-仿射變換後的性質變化/#定義)、且新的高斯分佈 $\mathbf{Y} \sim \mathcal{N}(A\boldsymbol{\mu}, A\Sigma A^T)$（這裡 $\mathbf{b} = 0$），剩下的工作就只是把投影矩陣分別代進均值與共變異數矩陣的公式，以下我們沿用剛剛「3 維只取前 2 維」的投影矩陣 $A$：

##### 投影後的均值

把 $A$ 代進 $\boldsymbol{\mu}_Y = A\boldsymbol{\mu}$：

{% raw %}

$$
\boldsymbol{\mu}_Y = A\boldsymbol{\mu} =
\begin{bmatrix}
1 & 0 & 0 \\
0 & 1 & 0
\end{bmatrix}
\begin{bmatrix}
\mu_1 \\
\mu_2 \\
\mu_3
\end{bmatrix}
=
\begin{bmatrix}
\mu_1 \\
\mu_2
\end{bmatrix}
= \boldsymbol{\mu}_a
$$

{% endraw %}

跟前面 $\mathbf{Y} = A\mathbf{X} = \mathbf{x}_a$ 的計算完全相同：$\boldsymbol{\mu}_b$ 被丟掉，只留下 $\boldsymbol{\mu}_a$

##### 投影後的共變異數矩陣

接著把 $A$ 代進 $\Sigma_Y = A\Sigma A^T$：

{% raw %}

$$
A\Sigma A^T =
\begin{bmatrix}
1 & 0 & 0 \\
0 & 1 & 0
\end{bmatrix}
\begin{bmatrix}
\sigma_{00} & \sigma_{01} & \sigma_{02} \\
\sigma_{10} & \sigma_{11} & \sigma_{12} \\
\sigma_{20} & \sigma_{21} & \sigma_{22}
\end{bmatrix}
\begin{bmatrix}
1 & 0 \\
0 & 1 \\
0 & 0
\end{bmatrix}
=
\begin{bmatrix}
\sigma_{00} & \sigma_{01} \\
\sigma_{10} & \sigma_{11}
\end{bmatrix}
$$

{% endraw %}

結果剛好就是**左上角的 $2 \times 2$ 子矩陣**

#### 結論：共變異數就是「直接取對應的子矩陣」

由上面的推導可以得到：高斯分佈的邊緣分佈仍然是高斯分佈，而且它的均值與共變異數，就是從原本的均值向量與共變異數矩陣中，把對應的那一塊**直接抓出來**而已：

{% raw %}

$$
\mathbf{x}_a \sim \mathcal{N}(\boldsymbol{\mu}_a,\ \Sigma_{aa})
$$

{% endraw %}

注意這裡完全沒有用到耦合項 $\Sigma_{ab}$、也沒有用到 $\Sigma_{bb}$，**它們就這樣被直接捨棄了**。換句話說，想求邊緣化後的共變異數矩陣，根本連矩陣乘法 $A\Sigma A^T$ 都不用算，把要丟掉的維度對應的行與列砍掉，直接抄剩下的子矩陣就好

## 例子：降維的仿射變換

前面我們用 3 維取前 2 維的投影矩陣，說明了邊緣化其實就是 $A\Sigma A^T$ 的特例。這裡再用一個 **2 維 → 1 維** 的具體例子，搭配圖可以更直覺的了解

假設有一個二維高斯分布：

{% raw %}

$$
\boldsymbol{\mu}
=
\begin{bmatrix}
1 \\
2
\end{bmatrix},
\quad
\Sigma
=
\begin{bmatrix}
4 & 1 \\
1 & 3
\end{bmatrix}
$$

{% endraw %}

取一個 $1 \times 2$ 的矩陣 $A$（即 $m = 1 < d = 2$），並令 $\mathbf{b} = 0$：

{% raw %}

$$
A =
\begin{bmatrix}
1 & 0
\end{bmatrix}
$$

{% endraw %}

則：

$$
Y = A\mathbf{X} = X_1
$$

也就是「只取出第一個分量」。套用定理，變換後的均值為：

{% raw %}

$$
\mu_Y = A\boldsymbol{\mu}
=
\begin{bmatrix}
1 & 0
\end{bmatrix}
\begin{bmatrix}
1 \\
2
\end{bmatrix}
= 1
$$

{% endraw %}

變換後的變異數為：

{% raw %}

$$
\Sigma_Y = A\Sigma A^T
=
\begin{bmatrix}
1 & 0
\end{bmatrix}
\begin{bmatrix}
4 & 1 \\
1 & 3
\end{bmatrix}
\begin{bmatrix}
1 \\
0
\end{bmatrix}
= 4
$$

{% endraw %}

所以：

$$
Y \sim \mathcal{N}(1, 4)
$$

原本的二維高斯分布被壓縮成了一個一維高斯分布（標準差為 $\sqrt{4} = 2$）

<img src="./gaussian_2d_projection.png" style="max-width: 600px;">

[圖片程式碼](https://github.com/bcjohnblue/python-plot/blob/main/2026/08/gaussian-distribution/two-dimension-projection.py)

從上方兩張圖片可以一眼看出二維高斯降維投影到一維上依舊是高斯。上圖是原本的二維高斯點雲（實線與虛線分別為 $1\sigma$、$2\sigma$ 等密度橢圓），灰色虛線示意每個點被投影到 $x$ 軸上；下圖則是投影後的一維高斯 $\mathcal{N}(1, 4)$，紅色點即為投影後的樣本，兩張圖的 $x$ 軸刻度互相對齊，可以直接對照每個點投影前後的位置

從幾何上看，這相當於把整個二維點雲 **投影到 $x$ 軸上**，而投影後的分布依然是高斯分布。值得注意的是，結果只保留了 $\Sigma$ 左上角的 $4$，至於耦合項 $1$ 與第二個分量的變異數 $3$ 都被直接捨棄了——這正呼應前面的結論：**邊緣化就是直接取出對應的子矩陣**

## 總結

1. **邊緣化保持高斯性**：對高斯分佈做邊緣化（把不需要的維度丟掉），結果仍然是高斯分佈
2. **邊緣化 = 投影 = 取子矩陣**：把不需要的維度丟掉時，結果的共變異數就是原矩陣中對應維度的子矩陣，可以直接把其他行列捨棄。它本質上是上一篇 $A\Sigma A^T$ 仿射變換在 $A$ 為投影矩陣時的特例

## 參考資料

[Pattern Recognition and Machine Learning (Bishop) - 2.3.2 Marginal Gaussian distribution](https://www.microsoft.com/en-us/research/publication/pattern-recognition-machine-learning/)

[(PP 6.8) Marginal distributions of a Gaussian](https://www.youtube.com/watch?v=ycDSJkZ_h0I)
