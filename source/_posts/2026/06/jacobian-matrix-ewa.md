---
title: Jacobian 矩陣 (2) - 在 EWA Volume Splatting 中的應用
date: 2026-08-08 23:28:34
updated: 2026-08-08 23:28:34
categories: Math
mathjax: true
---

###### 此為 **Jacobian 矩陣** 系列文章 - 第 2 篇：

1. <a href="/2026/08/08/jacobian-矩陣-1-定義/" target="_blank">Jacobian 矩陣 (1) - 定義</a>
2. <a href="/2026/08/08/jacobian-矩陣-2-在-ewa-volume-splatting-中的應用/" target="_blank">Jacobian 矩陣 (2) - 在 EWA Volume Splatting 中的應用</a>

## 前言

前一篇文章中，我們詳細介紹了 **Jacobian 矩陣** 的數學定義與幾何意義。我們了解到，Jacobian 矩陣本質上就是多變數向量函數的「一階導數」，它代表了一個複雜的非線性變換在空間中某一點附近的**局部線性近似**

今天我們來學習 Jacobian 矩陣在 3D 電腦圖學領域中發揮的關鍵作用，探討一篇極具影響力的經典論文 — **EWA Volume Splatting** (Zwicker et al., 2001) 是如何巧妙地利用 Jacobian 矩陣與 Gaussian 的數學特性，讓 **Volume Rendering (體積渲染)** 這件原本只能靠緩慢的 **Ray Marching (光線步進)** 完成的任務大幅加速，甚至奠定了近年爆紅的 **3D Gaussian Splatting** 即時渲染技術的數學基礎

## 背景：三維渲染的挑戰 Ray Marching vs. Splatting

要理解 EWA Volume Splatting 的偉大之處，我們得先看看傳統是怎麼渲染三維體積資料（例如：醫療 CT 掃描、雲霧、或是點雲）的

#### Ray Marching (光線步進)：緩慢的傳統演算法

傳統體積渲染最常用的方法是 **Ray Marching (光線步進)**。它的作法是從虛擬相機的視角出發，對螢幕上的「每一個像素」射出一條光線穿過 3D 場景。光線在穿過體積資料的過程中，必須沿途不斷地「步進」取樣，計算每一小步的顏色與透明度，最後將整條光線上所有取樣點的結果積分起來，才能決定該像素的最終顏色。這也造就了它的效能瓶頸：只要場景稍微複雜、解析度稍微高一點，就得射出數百萬條光線，每條光線又要取樣成百上千次，計算量極為龐大；再加上取樣時需要不斷在 3D 空間中插值查詢資料，記憶體存取非常缺乏效率，在早期的硬體上根本不可能做到即時渲染

#### Splatting (拋濺法)：擁抱 GPU 的演算法

為了解決這個效能問題，電腦圖學家們提出了 **Splatting (拋濺法)**。這是一種思路完全反過來的演算法：
與其從螢幕射出光線去尋找 3D 資料，不如我們直接拿著 3D 資料，把它們像「砸雪球」一樣，一顆一顆直接「砸 (Splat)」到 2D 螢幕上！

在 Splatting 中：

1. 空間中的資料被表示為一顆一顆重疊的 3D 球體 (通常是 3D 高斯橢球)
2. 渲染的過程，就是將這每一顆 3D 橢球，經過相機投影，變成 2D 螢幕上的一個 2D 橢圓
3. 最後只要把這些 2D 橢圓整個疊加起來，就完成了一張畫面

這套流程之所以有潛力，是因為它完全打中了現代硬體的強項 — 把 3D 座標投影到 2D、再於螢幕上繪製 2D 圖形，正是 **GPU Rasterizer (光柵化硬體)** 最擅長的事情。數以萬計的 2D 繪製指令可以同時被平行處理，這讓 Splatting 天生就具備大幅加速的本錢

不過，要把這個想法真正付諸實行，還得先跨過一道非常棘手的數學難題

<!-- more -->

## 難題：非線性透視投影

在 EWA Volume Splatting 中，空間中的資料點（Voxel 或 Point Cloud）被構建成一個連續的函數，而這個函數是由許多個 **3D 高斯函數 (3D Gaussian)** 疊加而成的，這篇論文中要做到就是將這些 3D 高斯橢球，透過相機渲染到 2D 螢幕上，也就是 [相機透視投影 (Perspective Projection)](/2025/02/28/three-js-中物體的遠近關係-3-深度值的計算方式/#3D-投影到-2D)

具體來說，在這篇論文中把相機空間的一個 3D 點 $\mathbf{u} = (u_0, u_1, u_2)^T$ 投影到 2D 螢幕上的函數 $m$ 長這樣：

{% raw %}

$$
m(\mathbf{u}) =
\begin{pmatrix}
u_0 / u_2 \\
u_1 / u_2 \\
\| \mathbf{u} \|
\end{pmatrix}
$$

{% endraw %}

P.S. 為什麼投影後的座標長這樣，稍後下面的 [深入論文：Section 4.4 The Projective Transformation](./#深入論文：Section-4-4-The-Projective-Transformation) 會完整說明

前兩個分量 $u_0 / u_2$、$u_1 / u_2$ 就是「除以深度 $u_2$」的透視投影，把點打到螢幕上，第三個分量 {% raw %} $\| \mathbf{u} \|$ {% endraw %} 則保留它到相機的距離（之後用於 [深度排序](/2025/02/13/three-js-中物體的遠近關係-1-什麼是深度測試？/)）

這裡最關鍵的，就是那個「除以 $u_2$」的動作。這個「[除以深度](/2025/02/28/three-js-中物體的遠近關係-3-深度值的計算方式/#實際上深度值的轉換函式)」的除法，正是畫面產生近大遠小透視感的來源；但也正是它，讓深度 $u_2$ 跑到了分母，使得 $m$ 沒辦法寫成 $A\mathbf{u} + \mathbf{b}$ 這種「常數矩陣乘上向量、再加平移」的線性形式 - 換句話說，這個相機投影是一個不折不扣的**非線性轉換**。而這樣的非線性，對「把 3D 高斯投影到 2D」的數學計算來說，是一個致命的難題，下一節我們就來看看它到底致命在哪裡

## 關鍵：高斯函數的優美性質只在線性變換下成立

那麼，這個非線性到底致命在哪裡呢？原因在於：把高斯投影、降維會用到的那些數學工具，全都建立在「投影是**線性**的」這個前提上。我們先複習一下前面幾篇文章學到的、高斯函數那些簡潔優美的數學性質就會知道為什麼：

#### 性質 1：高斯經過「線性（仿射）變換」後，仍然是高斯

在 [高斯分布 (3) - 仿射變換後的性質變化](/2026/03/18/高斯分布-3-仿射變換後的性質變化/) 中，我們證明過一個定理：如果一個隨機向量 $\mathbf{X} \sim \mathcal{N}(\boldsymbol{\mu}, \Sigma)$，對它進行仿射變換 $\mathbf{Y} = A\mathbf{X} + \mathbf{b}$（其中 $A$ 是常數矩陣、$\mathbf{b}$ 是平移向量），那麼 $\mathbf{Y}$ 仍然服從高斯分布：

$$
\mathbf{Y} \sim \mathcal{N}(A\boldsymbol{\mu} + \mathbf{b},\ A\Sigma A^T)
$$

而且新的均值與共變異數矩陣有著極為簡潔的封閉解：

- 均值：$\boldsymbol{\mu}_Y = A\boldsymbol{\mu} + \mathbf{b}$
- 共變異數矩陣：$\Sigma_Y = A\Sigma A^T$

#### 性質 2：當線性變換是「投影降維」時，連算都幾乎不用算

在 [高斯分布 (4) - 邊緣化](/2026/08/07/高斯分布-4-邊緣化/) 中，我們更看到了上面定理最乾脆的特例：當 $A$ 取成一個投影矩陣（也就是降維）時，投影後的高斯幾乎不用算 - 新的共變異數矩陣就是直接抄原矩陣對應的子矩陣

換句話說，**只要投影是線性的，把一顆高斯投影到低維、求出它在螢幕上的形狀，是一件又精確又快的事**，這正是 Splatting 夢寐以求的工具

問題在於，前面 [難題：非線性透視投影](./#難題：非線性透視投影) 一節已經看到，相機投影 $m$ 含有「除以深度 $u_2$」的透視除法，是個不折不扣的**非線性轉換**，並不是性質 1、2 說的那種線性／仿射投影。而性質 1 的定理只在「線性」這個前提下才成立，一旦把高斯丟進非線性的 $m$，出來的結果就不再是高斯了，可能變成某種形狀怪異、難以用簡單數學式描述的斑塊。既然連高斯的數學形式都保不住，自然也就套不上 $\Sigma_Y = A\Sigma A^T$ 這條封閉公式，更別提要把它畫到螢幕、丟給 GPU 加速了

## 解法：Jacobian 矩陣登場

此時正是 Jacobian 矩陣派上用處的地方！回憶 [前一篇文章](/2026/08/08/jacobian-矩陣-1-定義/) 的核心結論 - Jacobian 矩陣就是一個非線性向量函數在某一點的「局部線性近似」

這裡要近似的，就是前面提到的那個投影函數 $m(\mathbf{u})$，由於一顆 3D 高斯橢球通常只佔據空間中很小的一塊區域，我們可以在它的中心點 $\mathbf{u}_k$，利用 **泰勒展開式 (Taylor Expansion)** 對 $m$ 做一階展開：

$$
m(\mathbf{u}) \approx m(\mathbf{u}_k) + \mathbf{J}_k (\mathbf{u} - \mathbf{u}_k)
$$

其中各符號的意義：

- $\mathbf{u}$：相機空間中的任意輸入點
- $\mathbf{u}_k$：第 $k$ 顆高斯的中心點，也是泰勒展開的基準點
- $m(\mathbf{u})$：非線性的投影函數
- $m(\mathbf{u}_k)$：中心點投影後的精確位置
- $\mathbf{J}_k$：$m$ 在 $\mathbf{u}_k$ 處的 Jacobian 矩陣，扮演局部線性近似中的線性轉換
- $(\mathbf{u} - \mathbf{u}_k)$：輸入點相對中心點的位移

白話說就是：**任一點的投影 $\approx$ 中心點的投影 $m(\mathbf{u}_k)$，加上位移經 $\mathbf{J}_k$ 修正後的量**，離中心愈近就愈準

不過，這個式子乍看之下還不太像我們熟悉的仿射變換。沒關係，把括號乘開、重新整理一下：

{% raw %}

$$
m(\mathbf{u}) \approx \mathbf{J}_k \mathbf{u} + \big( m(\mathbf{u}_k) - \mathbf{J}_k \mathbf{u}_k \big)
$$

{% endraw %}

再對照仿射變換的標準形式 $A \mathbf{u} + \mathbf{b}$，對應關係就一目了然了。關鍵在於**中心點 $\mathbf{u}_k$ 是固定的**，所以 $m(\mathbf{u}_k)$ 與 $\mathbf{J}_k \mathbf{u}_k$ 都是不隨輸入 $\mathbf{u}$ 改變的常數：

- 唯一隨 $\mathbf{u}$ 變動的部分 $\mathbf{J}_k \mathbf{u}$，對應到線性轉換的矩陣 $A = \mathbf{J}_k$
- 剩下不含 $\mathbf{u}$ 的常數項收集在一起，就是固定的平移量 $\mathbf{b} = m(\mathbf{u}_k) - \mathbf{J}_k \mathbf{u}_k$

於是在中心點 $\mathbf{u}_k$ 的附近，**原本非線性的 $m$，就被替換成了一個由 $\mathbf{J}_k$ 代表的線性（仿射）變換**

而這整套「用泰勒一階展開，把 $m$ 換成仿射映射」的做法，正是論文中的 **公式 (17)**：論文以 $x_k = m(\mathbf{u}_k)$ 表示投影後的中心，把上面的近似改寫成一個等號定義式，正式定義出這個專門用來近似 $m$ 的仿射映射：

{% raw %}

$$
m_{u_k}(\mathbf{u}) = x_k + \mathbf{J}_{u_k} (\mathbf{u} - \mathbf{u}_k)
$$

{% endraw %}

一旦投影重新回到「線性」的世界，前面那些高斯函數的優美性質就全都派得上用場了：性質 1 的封閉公式 $\Sigma_Y = A\Sigma A^T$ 可以直接套用（把 $A$ 換成 $\mathbf{J}_k$）；性質 2 的降維更是連算都幾乎不用算，直接抄原矩陣對應的子矩陣即可。這就是 Jacobian 矩陣帶來的神奇之處 - 原本卡死的數學運算，搖身一變成了直覺簡單的計算

#### 小問題：為什麼套用 Jacobian 矩陣的「局部近似」可以接受？

這裡我們插入討論一個小問題，用線性近似一個非線性函數，難道誤差不會很大嗎？

關鍵在於高斯的本質 - 它是一個**集中在中心點附近的機率分佈**，超過幾個標準差之外的機率幾乎為零。換句話說，一顆 3D 高斯「實際上有意義的範圍」非常小，而在這麼小的範圍內，$m$ 的非線性可以被忽略，泰勒一階展開就足以表達它的行為

#### 小結

到這裡，我們已經把 EWA Volume Splatting 這篇論文最核心的概念講完了 - 透過 Jacobian 矩陣，把原本非線性的「3D 高斯投影到 2D」近似成一個局部的線性變換，接著再借助高斯函數在線性變換下那些優美的性質，幾乎不費吹灰之力就能算出投影到 2D 螢幕後的結果

理解了概念之後，接下來就讓我們真正走進論文的 **Section 4.4 The Projective Transformation**，看看它是如何精確定義這個投影函數 $m(\mathbf{u})$，又是如何把 Jacobian 一步步推導出來，最終算出 2D 螢幕上的座標

## 深入論文：Section 4.4 The Projective Transformation

論文中提到，要將體積資料渲染到螢幕上，需要將資料從 **Camera Space (相機空間)** 映射到 **Ray Space (光線空間)**

### 1. 座標轉換 - 三維相機空間投影到二維光線空間

我們現在要想辦法將三維中的所有座標點，投影到二維的平面上，目的就是為了把三維空間中的這些東西渲染到 2D 螢幕上

所以第一步要做的就是座標轉換，也就是如何將三維座標轉換到二維上。 我們將三維座標投影到某個特定的二維平面上，這個平面稱之為「投影平面」。為了方便，我們把它投影到位於 $u_2 = 1$ 的位置上

- **相機空間 (Camera Space)** 的座標點標記為 $\mathbf{u} = (u_0, u_1, u_2)^T$
- **光線空間 (Ray Space)** 的座標點標記為 $\mathbf{x} = (x_0, x_1, x_2)^T$

#### 正向投影：相機空間 → 光線空間

下圖展示了這個 3D 投影的全貌。紅色的點就是原本三維相機空間中的點，它的座標是 $(u_0, u_1, u_2)$，當投影到 $u_2=1$ 的平面時，它得到的二維光線空間座標是 $(x_0, x_1, x_2)$

<img style="max-width: 600px;" src="ray_space_3d.png" />

接著我們將根據三維相機空間中的值，用國高中所學的相似三角形概念，推導出二維平面上的座標值，以下我們假設三維相機空間中的值為 $u_0 = 0.5, u_1 = 0.9, u_2 = 1.2$

首先來看 $u_0-u_2$ 平面，運用相似三角形的概念可以得到下式：

$$
\frac{x_0}{1} = \frac{u_0}{u_2} \Rightarrow x_0 = \frac{u_0}{u_2} = \frac{0.5}{1.2} = 0.417
$$

<img style="max-width: 600px;" src="ray_space_u0_u2.png" />

接著看 $u_1-u_2$ 平面，同理可得

$$
\frac{x_1}{1} = \frac{u_1}{u_2} \Rightarrow x_1 = \frac{u_1}{u_2} = \frac{0.9}{1.2} = 0.75
$$

<img style="max-width: 600px;" src="ray_space_u1_u2.png" />

透過上面的相似三角形，我們已經得到了 2D 座標 $x_0 = u_0 / u_2$ 與 $x_1 = u_1 / u_2$。但只有 $(x_0, x_1)$ 還不夠 ── 投影到 2D 平面後，原本的「深度」資訊就遺失了，後續無法做深度排序、也無法計算 Gaussian 沿著光線方向的衰減

因此我們補上第三個分量 $x_2$，將它定義為「該點到相機原點的歐氏距離」：

{% raw %}

$$
x_2 = \| (u_0, u_1, u_2)^T \| = \sqrt{u_0^2 + u_1^2 + u_2^2}
$$

{% endraw %}

把這三個分量合在一起，就得到論文中 **公式 (15)** 所定義的映射函數 $\mathbf{x} = m(\mathbf{u})$，它將相機空間的點 $\mathbf{u}$ 投影到光線空間中：

{% raw %}

$$
\begin{pmatrix}
x_0 \\
x_1 \\
x_2
\end{pmatrix}
= m(\mathbf{u}) =
\begin{pmatrix}
u_0 / u_2 \\
u_1 / u_2 \\
\| (u_0, u_1, u_2)^T \|
\end{pmatrix}
$$

{% endraw %}

其中各分量的意義為：

- $x_0, x_1$：透視除法後的 2D 螢幕座標（也就是光線座標）
- $x_2$：該點到相機的歐氏距離，保留下來用於深度排序與深度衰減計算

#### 反向投影：光線空間 → 相機空間

反過來，若已知光線空間中的點 $\mathbf{x}$，我們也可以還原回相機空間中對應的 $\mathbf{u}$，這個對應關係就是反函數 $m^{-1}(\mathbf{x})$。接下來我們一步步推導它的形式

從前面的相似三角形推導我們知道，相機空間的點 $\mathbf{u} = (u_0, u_1, u_2)^T$ 被投影到 $u_2 = 1$ 的平面後，落點為 $(x_0, x_1, 1)^T$

由於投影本身就是把 $\mathbf{u}$ 沿著「原點 → $\mathbf{u}$」的光線一路延伸到 $u_2 = 1$ 平面上才停下來，因此 $\mathbf{u}$、$(x_0, x_1, 1)^T$、原點這三個點必定共線，都在同一條從原點出發的光線上

也就是說，我們只要知道兩件事，就能完全鎖定 $\mathbf{u}$ 的位置：

1. **方向**：光線指向哪個方向 → 由 $(x_0, x_1, 1)^T$ 決定
2. **距離**：$\mathbf{u}$ 距離原點多遠 → 就是 $x_2$

接下來只要把這兩個資訊組合起來就好

##### 步驟 1：從 $(x_0, x_1, 1)^T$ 取得單位方向向量

$(x_0, x_1, 1)^T$ 雖然方向正確，但長度不一定是 1。我們先計算它的長度（也就是歐氏範數）：

{% raw %}

$$
l = \| (x_0, x_1, 1)^T \| = \sqrt{x_0^2 + x_1^2 + 1}
$$

{% endraw %}

把它除以自身的長度，就得到長度為 1 的**單位方向向量** $\hat{\mathbf{d}}$：

{% raw %}

$$
\hat{\mathbf{d}} = \frac{1}{l}
\begin{pmatrix} x_0 \\ x_1 \\ 1 \end{pmatrix}
=
\begin{pmatrix} x_0 / l \\ x_1 / l \\ 1 / l \end{pmatrix}
$$

{% endraw %}

##### 步驟 2：沿著光線方向走 $x_2$ 的距離

既然 $\mathbf{u}$ 在這條光線上、且距離原點為 $x_2$，那麼從原點沿著單位方向 $\hat{\mathbf{d}}$ 走 $x_2$ 的距離，就會剛好抵達 $\mathbf{u}$：

{% raw %}

$$
\mathbf{u} = x_2 \cdot \hat{\mathbf{d}} = \frac{x_2}{l}
\begin{pmatrix} x_0 \\ x_1 \\ 1 \end{pmatrix}
$$

{% endraw %}

這就是論文中 **公式 (16)** 反函數 $m^{-1}(\mathbf{x})$ 的形式：

{% raw %}

$$
\begin{pmatrix}
u_0 \\
u_1 \\
u_2
\end{pmatrix}
= m^{-1}(\mathbf{x}) =
\begin{pmatrix}
x_0 / l \cdot x_2 \\
x_1 / l \cdot x_2 \\
1 / l \cdot x_2
\end{pmatrix}
$$

{% endraw %}

##### 步驟 3：驗證反函數確實能還原

我們可以代回去檢查，看看 $m^{-1}(\mathbf{x})$ 是否真的滿足當初定義的三個分量：

- 前兩個分量必須滿足透視除法 $u_0 / u_2 = x_0$、$u_1 / u_2 = x_1$：

  $$
  \frac{u_0}{u_2} = \frac{x_0 \cdot x_2 / l}{x_2 / l} = x_0 \quad\checkmark \qquad
  \frac{u_1}{u_2} = \frac{x_1 \cdot x_2 / l}{x_2 / l} = x_1 \quad\checkmark
  $$

- 第三個分量必須滿足 {% raw %} $\| \mathbf{u} \| = x_2$ {% endraw %}：

{% raw %}

$$
\| \mathbf{u} \| = \left\| \frac{x_2}{l} (x_0, x_1, 1)^T \right\| = \frac{x_2}{l} \cdot \| (x_0, x_1, 1)^T \| = \frac{x_2}{l} \cdot l = x_2 \quad\checkmark
$$

{% endraw %}

### 2. 計算 Jacobian 矩陣 $J$

為了找到局部的線性近似，我們需要計算 $m(\mathbf{u})$ 對 $\mathbf{u}$ 的 Jacobian 矩陣 $\mathbf{J}$。根據 Jacobian 的定義，我們要計算所有一階偏導數：

{% raw %}

$$
\mathbf{J} = \frac{\partial m}{\partial \mathbf{u}} =
\begin{bmatrix}
\dfrac{\partial x_0}{\partial u_0} & \dfrac{\partial x_0}{\partial u_1} & \dfrac{\partial x_0}{\partial u_2} \\[0.5em]
\dfrac{\partial x_1}{\partial u_0} & \dfrac{\partial x_1}{\partial u_1} & \dfrac{\partial x_1}{\partial u_2} \\[0.5em]
\dfrac{\partial x_2}{\partial u_0} & \dfrac{\partial x_2}{\partial u_1} & \dfrac{\partial x_2}{\partial u_2}
\end{bmatrix}
$$

{% endraw %}

我們逐項計算偏導數：

對於 $x_0 = u_0 / u_2$：

{% raw %}

$$
\frac{\partial x_0}{\partial u_0} = \frac{1}{u_2} \qquad
\frac{\partial x_0}{\partial u_1} = 0 \qquad
\frac{\partial x_0}{\partial u_2} = -\frac{u_0}{u_2^2}
$$

{% endraw %}

對於 $x_1 = u_1 / u_2$：

{% raw %}

$$
\frac{\partial x_1}{\partial u_0} = 0 \qquad
\frac{\partial x_1}{\partial u_1} = \frac{1}{u_2} \qquad
\frac{\partial x_1}{\partial u_2} = -\frac{u_1}{u_2^2}
$$

{% endraw %}

對於 $x_2 = \Vert \mathbf{u} \Vert = \sqrt{u_0^2 + u_1^2 + u_2^2}$：

{% raw %}

$$
\frac{\partial x_2}{\partial u_0} = \frac{u_0}{\Vert \mathbf{u} \Vert} \qquad
\frac{\partial x_2}{\partial u_1} = \frac{u_1}{\Vert \mathbf{u} \Vert} \qquad
\frac{\partial x_2}{\partial u_2} = \frac{u_2}{\Vert \mathbf{u} \Vert}
$$

{% endraw %}

將這些結果代入，我們得到了評估在相機空間點 $\mathbf{u}$ 的 Jacobian 矩陣：

{% raw %}

$$
\mathbf{J} =
\begin{bmatrix}
\dfrac{1}{u_2} & 0 & -\dfrac{u_0}{u_2^2} \\[0.5em]
0 & \dfrac{1}{u_2} & -\dfrac{u_1}{u_2^2} \\[0.5em]
\dfrac{u_0}{\| \mathbf{u} \|} & \dfrac{u_1}{\| \mathbf{u} \|} & \dfrac{u_2}{\| \mathbf{u} \|}
\end{bmatrix}
$$

{% endraw %}

### 3. 套用 Gaussian 線性轉換公式：推導投影後的 2D Gaussian

要描述投影到 2D 後的高斯長什麼樣子，其實只需要知道兩件事，**中心點（Mean）** 決定它的位置、**共變異數矩陣（Covariance）** 決定它的形狀。

假設在 **相機空間(3D)** 中，第 $k$ 顆高斯的中心點是 $\mathbf{u}_k$、共變異數矩陣是 $\mathbf{V}_k$。接下來我們來看他投影到 2D 上的中心點與其形狀

#### 投影後的中心點（Mean）

中心點是一個確定的位置，沒有不確定性的問題，所以我們不需要靠線性近似，直接用 $m$ 算就行：

$$
\mathbf{x}_k = m(\mathbf{u}_k)
$$

其中各符號的意義為：

- $\mathbf{u}_k$：第 $k$ 個 3D 高斯在**相機空間**中的中心點，$\mathbf{u}_k = (u_0, u_1, u_2)^T$
- $m(\cdot)$：前面推導出的投影函數（相機空間 → 光線空間）
- $\mathbf{x}_k$：投影後該高斯在**光線空間**中的中心點，$\mathbf{x}_k = (x_0, x_1, x_2)^T$

中心點的位置是「精確的」，並沒有用到 Jacobian

#### 投影後的共變異數矩陣（Covariance）

共變異數就不一樣了，它描述的是「中心點周圍」的分佈形狀，而「周圍」正是線性近似真正派上用場的地方。還記得我們在前面 [解法：Jacobian 矩陣登場](./#解法：Jacobian-矩陣登場) 一節推導出的結論嗎 - 在中心點附近，非線性投影 $m$ 可以近似成一個仿射變換，而仿射變換中扮演線性轉換矩陣 $A$ 的，正是 Jacobian 矩陣，也就是 $A = \mathbf{J}_k$

於是，直接搬出 [高斯函數在線性變換下的封閉公式](./#關鍵：高斯函數的優美性質只在線性變換下成立) $\Sigma_Y = A \Sigma A^T$，把公式中的 $A$ 換成在中心點上求出的 Jacobian 矩陣 $\mathbf{J}_k = \mathbf{J}(\mathbf{u}_k)$，就得到投影後的共變異數矩陣：

$$
\mathbf{V}_k' = \mathbf{J}_k \mathbf{V}_k \mathbf{J}_k^T
$$

其中各符號的意義為：

- $\mathbf{V}_k$：投影前，該高斯在**相機空間**中的 $3 \times 3$ 共變異數矩陣，描述這顆橢球的大小、形狀、方向
- $\mathbf{J}_k$：投影函數 $m$ 在中心點 $\mathbf{u}_k$ 處的 Jacobian 矩陣（$3 \times 3$），也就是 $m$ 在該點附近的局部線性近似
- $\mathbf{J}_k^T$：$\mathbf{J}_k$ 的轉置矩陣
- $\mathbf{V}_k'$：投影後，該高斯在**光線空間**中的 $3 \times 3$ 共變異數矩陣

#### 從 3D 降到 2D：只保留螢幕上的兩個維度

即使我們把 3D 投影到 2D 上了，但別忘了 $\mathbf{V}_k'$ 還是個 $3 \times 3$ 矩陣，包含了光線空間三個分量 $(x_0, x_1, x_2)$ 的共變異數。但 Splatting 真正要畫到螢幕上的，只有 $(x_0, x_1)$ 這兩個維度，$x_2$（也就是徑向距離）只負責深度排序與深度衰減，不會影響 2D 上的高斯點「畫在哪裡」

而捨棄一個維度，正是前面 [投影降維](./#性質-2：當線性變換是「投影降維」時，連算都幾乎不用算) 講過的事：降維後的共變異數矩陣就是原矩陣中相對應的子矩陣。因此我們連算都不用算，直接捨棄第三行與第三列，取左上角的 $2 \times 2$ 子矩陣就是 2D 螢幕上那顆橢圓的共變異數矩陣：

{% raw %}

$$
\Sigma_{2D} = (\mathbf{V}_k')_{[0:2,\, 0:2]} = (\mathbf{J}_k \mathbf{V}_k \mathbf{J}_k^T)_{[0:2,\, 0:2]}
$$

{% endraw %}

其中各符號的意義為：

- $\Sigma_{2D}$：2D 螢幕上那顆橢圓的 $2 \times 2$ 共變異數矩陣，最終決定這個 splat 畫出來的形狀
- $(\cdot)_{[0:2,\, 0:2]}$：取矩陣左上角的 $2 \times 2$ 子矩陣，也就是只保留 $(x_0, x_1)$ 對應的第 1、2 行與第 1、2 列，捨棄 $x_2$ 對應的第三行與第三列

#### 對照論文：公式 (20) 中的共變異數

最後我們來看一下上面我們在求 [投影後的共變異數矩陣](./#投影後的共變異數矩陣（Covariance）) 時，對應的其實就是論文中的 **公式 (20)**：

{% raw %}

$$
\begin{aligned}
\mathbf{V}_k &= \mathbf{J} \mathbf{V}_k' \mathbf{J}^T \\
&= \mathbf{J} \mathbf{W} \mathbf{V}_k'' \mathbf{W}^T \mathbf{J}^T
\end{aligned}
$$

{% endraw %}

要注意論文中的符號跟我們上面提到的並不相同，公式 (20) 中各符號的意義為：

- $\mathbf{V}_k''$：物件空間的共變異數矩陣
- $\mathbf{V}_k'$：相機空間的共變異數矩陣
- $\mathbf{V}_k$：投影後（光線空間）的共變異數矩陣
- $\mathbf{W}$：View Matrix，負責把物件空間的共變異數矩陣轉換到相機空間

### 4. 實際數值推導

上面我們了解完整個原理了，接著讓我們代入實際數值感受一下。假設相機空間中有一個點 $\mathbf{u} = (0.5, 0.9, 1.2)^T$：

#### 步驟 1：投影到光線空間

首先計算它在光線空間的座標 $\mathbf{x}$：

- $x_0 = u_0 / u_2 = 0.5 / 1.2 \approx 0.417$
- $x_1 = u_1 / u_2 = 0.9 / 1.2 = 0.750$
- $x_2 = \Vert \mathbf{u} \Vert = \sqrt{0.5^2 + 0.9^2 + 1.2^2} = \sqrt{0.25 + 0.81 + 1.44} = \sqrt{2.5} \approx 1.581$

這完美對應 [前面圖表的結果](./#1-座標轉換-三維相機空間投影到二維光線空間)：該點在 $u_2=1$ 的投影平面上，其 2D 螢幕座標為 $(0.417, 0.750)$，而它沿著射線方向的總長度（徑向距離）為 $1.581$

#### 步驟 2：計算 Jacobian 矩陣

接著計算在 $\mathbf{u} = (0.5, 0.9, 1.2)^T$ 處的 Jacobian 矩陣 $\mathbf{J}$。已知 $u_2 = 1.2$、$u_2^2 = 1.44$、$\Vert \mathbf{u} \Vert \approx 1.581$，代入 [上面推導出的公式](./#2-計算-Jacobian-矩陣-J)：

{% raw %}

$$
\mathbf{J} =
\begin{bmatrix}
\dfrac{1}{1.2} & 0 & -\dfrac{0.5}{1.44} \\[0.5em]
0 & \dfrac{1}{1.2} & -\dfrac{0.9}{1.44} \\[0.5em]
\dfrac{0.5}{1.581} & \dfrac{0.9}{1.581} & \dfrac{1.2}{1.581}
\end{bmatrix}
\approx
\begin{bmatrix}
0.833 & 0 & -0.347 \\
0 & 0.833 & -0.625 \\
0.316 & 0.569 & 0.759
\end{bmatrix}
$$

{% endraw %}

這個 $3 \times 3$ 矩陣就是 $m$ 在 $(0.5, 0.9, 1.2)$ 附近的「局部線性近似」。一旦把該點的 3D 共變異數矩陣 $\mathbf{V}$ 代入 $\mathbf{J} \mathbf{V} \mathbf{J}^T$，再取左上角 $2 \times 2$ 子矩陣，就是這顆 Gaussian 砸到螢幕上之後那個橢圓的形狀

## 結論

整篇文章其實只繞著一個核心問題打轉：**「3D Gaussian 經過相機投影之後，會變成什麼？」**

這篇論文給出的答案，就是引入 **Jacobian 矩陣** — 把原本非線性的透視投影，在每顆 Gaussian 的中心附近做**局部線性近似**，讓 3D 高斯投影到 2D 這件事，化作一次簡單的矩陣乘法 $\mathbf{J} \mathbf{V} \mathbf{J}^T$

藉由這樣的做法，整個渲染的數學運算變得非常快速：

1. 過去只能用 **Ray Marching（光線步進）**：對螢幕上每個像素射出光線、沿途逐步取樣與積分，非常緩慢
2. 現在改用 **Splatting**：藉由 Jacobian 矩陣的線性近似，每顆 3D 高斯都能快速轉換成 2D 螢幕上的橢圓，再交給 GPU 平行繪製

## 補充資料

#### 1. EWA Volume Splatting 與 3D Gaussian Splatting 的關係

文章開頭提過，EWA Volume Splatting 奠定了近年爆紅的 **3D Gaussian Splatting**（Kerbl et al., 2023）的數學基礎，這裡補充說明兩者的關係

最直接的證據，就是前面 [對照論文：公式 (20) 中的共變異數](./#對照論文：公式-20-中的共變異數) 一節的公式。把公式 (20) 的第二行，拿去對照 3D Gaussian Splatting 論文中「投影共變異數矩陣」的式子：

{% raw %}

$$
\Sigma' = \mathbf{J} \mathbf{W} \Sigma \mathbf{W}^T \mathbf{J}^T
$$

{% endraw %}

會發現兩者一模一樣，只是符號寫法不同：$\Sigma$ 對應物件空間的共變異數矩陣、$\mathbf{W}$ 同樣是 View Matrix，而 $\mathbf{J}$ 正是本文的主角- 投影函數在高斯中心點上的 Jacobian 矩陣。相隔二十幾年，「把一顆 3D 高斯投影到 2D 螢幕」這一步的數學，3D Gaussian Splatting 原封不動地繼承了 EWA Volume Splatting 的推導

當然，兩篇論文要解的問題並不相同：

- **EWA Volume Splatting (2001)** 處理的是「渲染」問題：輸入是既有的體積資料（CT 掃描、點雲等），高斯是人為選定、用來表示這些資料的基底，論文的重點在於：怎麼把這些 3D 高斯又快又漂亮地畫到螢幕上，不會出現鋸齒或閃爍的瑕疵
- **3D Gaussian Splatting (2023)** 處理的是「重建 + 即時渲染」問題：輸入是一組實際拍攝的照片，每顆高斯的位置、形狀、顏色、透明度都不是人為指定的，而是讓電腦反覆比對「畫出來的結果」和「真實照片」之間的差異，自動一點一點調整、學出來的，再加上充分發揮 GPU 平行繪圖的能力，讓畫面能以每秒 100 張以上的速度即時渲染出來

換句話說，3D Gaussian Splatting 的突破在於「怎麼從照片學出這些高斯」以及「怎麼把它們畫得飛快」，而它背後使用的投影數學 - 用 Jacobian 矩陣把非線性的透視投影局部線性化、再用 $\mathbf{J} \mathbf{V} \mathbf{J}^T$ 轉換共變異數矩陣，正是 EWA Volume Splatting 在 2001 年就打下的基礎

## 參考資料

[EWA Volume Splatting](https://www.cs.umd.edu/~zwicker/publications/EWAVolumeSplatting-VIS01.pdf)
[3D Gaussian Splatting for Real-Time Radiance Field Rendering](https://arxiv.org/pdf/2308.04079)
