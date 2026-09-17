---
title: 球諧函數 (1) - 定義與理解
date: 2026-09-17 23:00:00
updated: 2026-09-17 23:00:00
categories: Math
mathjax: true
---

###### 此為 **球諧函數** 系列文章 - 第 1 篇：

1. <a href="/2026/09/17/球諧函數-1-定義與理解/" target="_blank">球諧函數 (1) - 定義與理解</a>
2. <a href="/2026/08/09/球諧函數-2-在-3d-gaussian-splatting-中的應用/" target="_blank">球諧函數 (2) - 在 3D Gaussian Splatting 中的應用</a>

## 前言

[Jacobian 矩陣系列文章](/2026/08/08/jacobian-矩陣-2-在-ewa-volume-splatting-中的應用/) 中，我們理解了 3D 高斯的「形狀」是如何投影到 2D 螢幕上的，但一顆高斯除了形狀之外還有「顏色」，而且在真實世界中，同一個點的顏色會隨著觀看角度而改變（例如：湖面上的反光，站在不同位置看，亮點的位置與強度都不一樣；金屬或陶瓷表面的高光，只要稍微移動視角就會看到不一樣的感覺），**3D Gaussian Splatting** 中用來描述這種「隨方向改變的顏色」的數學工具，就是今天要介紹的 **球諧函數 (Spherical Harmonics，簡稱 SH)**，在介紹球諧函數前讓我們先從傅立葉級數開始說起，因為事實上「球諧函數可以說就是球面版的傅立葉級數」

<!-- more -->

## 傅立葉級數

先回憶一維的 **傅立葉級數 (Fourier Series)**，任何一個週期函數 $f(\theta)$，都可以拆解成一系列 $\sin$ 與 $\cos$ 的加權和，這裡的週期函數也可以想成「定義在**圓**上的函數」：把 $\theta$ 看成單位圓上的角度，繞一圈 $2\pi$ 剛好又回到原點。輸入是圓上的一個位置、輸出是該位置的數值，例如「沿著赤道每個經度的溫度」就是這樣的函數，其傅立葉級數表示為：

{% raw %}

$$
f(\theta) = \frac{a_{0}}{2} + \sum_{n=1}^{\infty} \big( a_{n} \cos n\theta + b_{n} \sin n\theta \big)
$$

{% endraw %}

P.S. 式子開頭的常數項 $a_{0}/2$ 就是函數在一個週期內的**平均值**，至於為什麼要除以 $2$，見文末的 [補充資料 1](./#補充資料)

這個拆解有以下幾個特點：

- $\sin n\theta$、$\cos n\theta$ 是一組固定的「基底函數」，$n$ 越小波形越平緩、$n$ 越大波形越細碎
- 任何函數都只是這些基底的**加權和**，權重就是係數 $a_{n}$、$b_{n}$
- 只取前面幾項（低頻），就能得到原函數的「粗略輪廓」；越多項，細節越豐富

下圖用方波當例子：只取 1 項時只是一條平緩的 $\sin$ 波，隨著項數增加，疊加的結果越來越貼近方波

<img src="./fourier_square_wave.png" style="max-width: 700px;">

[圖片程式碼](https://github.com/bcjohnblue/python-plot/blob/main/2026/08/spherical-harmonics/fourier_square_wave.py)

現在把問題升級：如果函數不是定義在圓上，而是定義在**球面**上呢？

球面上的每一個點，都對應一個「從球心指向該點的方向」，所以描述球面上的位置，最方便的方式就是用**單位向量** $\mathbf{d} = (x, y, z)$（滿足 $x^2 + y^2 + z^2 = 1$）。舉幾個例子：

- 正上方（北極）：$\mathbf{d} = (0, 0, 1)$
- 正前方（赤道上某點）：$\mathbf{d} = (1, 0, 0)$
- 斜上 $45°$：$\mathbf{d} = (0.707, 0, 0.707)$

於是我們可以用一個**球面函數**去定義球面上的每一個點，這個函數的輸入是一個方向 $\mathbf{d}$，而輸出則是沿著方向 $\mathbf{d}$ 在球面上得出的數值。例如：「地球表面各地的溫度」就是一個球面函數，給它一個方向（某個經緯度），它可以回傳指向這個方向時，地球表面的溫度值是多少

所以我們希望有一組定義在球面上的基底函數，讓任何球面函數都能像傅立葉級數那樣，拆解成這組基底的加權和。這組在球面上扮演 $\sin$、$\cos$ 角色的基底，就叫做**球諧函數**

## 球諧函數的基底

傅立葉級數的基底只有 $\sin$、$\cos$ 兩種，而球諧函數的基底則不只兩種，而是超多組固定的球面函數，每一階都有好幾個成員、每個成員都有自己的公式，需要用兩個索引 $\ell$、$m$ 來編號，記為 $Y_{\ell m}(\mathbf{d})$

為什麼傅立葉只需要一個頻率 $n$，球諧函數卻需要 $\ell$ 和 $m$ 兩個？因為圓是一維的曲線，同一個頻率的波形只有「相位」的差別，$\sin$ 與 $\cos$ 兩個就足以涵蓋；但球面是**二維的曲面**，同一種細碎程度的花紋，有更多種「擺放方式」，所以第 $\ell$ 階需要 $2\ell + 1$ 個成員才能完整表示，$m$（**序數**，order，範圍從 $-\ell$ 到 $+\ell$）就是同一階內這些不同擺放方式的編號

兩者的對應關係整理如下：

<style>
.sh-table table { margin: 1.2em 0; font-size: 1em; }
.sh-table th, .sh-table td { padding: 8px 14px; line-height: 1.7; }
/* 讓 markdown 的 :---: 置中設定不被主題的 th { text-align: left } 蓋掉 */
th[align="center"], td[align="center"] { text-align: center; }
</style>

<div class="sh-table">

|                            | 傅立葉級數                     | 球諧函數                          |
| -------------------------- | ------------------------------ | --------------------------------- |
| 函數定義在哪裡             | 圓上（一個週期 $0 \sim 2\pi$） | 球面上                            |
| 基底是誰                   | $\sin n\theta$、$\cos n\theta$ | $Y_{\ell m}(\mathbf{d})$          |
| 「細碎程度」的編號         | 頻率 $n$                       | 階數 $\ell$                       |
| 同一個細碎程度下有幾個基底 | $2$ 個（$\sin$ 和 $\cos$）     | $2\ell + 1$ 個（用序數 $m$ 編號） |

</div>

<br />

### 直接看圖：每一階長什麼樣子

乍看之下 $\ell$ 和 $m$ 到底是什麼一定讓人很迷惑，所以我們先來直接看圖，會比較好理解不同的 $\ell$ 和 $m$ 所畫出來的圖像是長什麼樣子的

<img src="./sh_basis_pyramid.png" style="max-width: 100%;">

[圖片程式碼](https://github.com/bcjohnblue/python-plot/blob/main/2026/08/spherical-harmonics/sh_basis_pyramid.py)

上圖中的顏色代表正負，**紅色是正值、藍色是負值**，以第二排中間的基底（$Y_{1,0}$）為例：朝正上方時函數值最大（紅瓣）、朝正下方時函數值最小（藍瓣）、朝赤道方向時函數值是 $0$。上圖中只先畫出了球諧函數的前三階基底：

- **第 $0$ 階（第一排）**：只有一個成員，而且是**常數**，每個方向的值都一樣，所以畫出來是一顆完美的圓球
- **第 $1$ 階（第二排）**：三個成員是同一種「兩瓣花紋」，只是分別沿著 $y$、$z$、$x$ 軸擺放，這就是「同一種細碎程度、不同擺放方式」的意思
- **第 $2$ 階（第三排）**：五個成員的花紋都變細了一級（四瓣型），差別同樣只在擺放方式：正放、斜放在不同平面上、或繞著 $z$ 軸對稱
- **第 $3$ 階（第四排）**：花瓣又更多、更細，對應傅立葉級數中越來越高的頻率

由上面的規律可以看出，如果取到第 $L$ 階為止，基底函數的總數為：

{% raw %}

$$
1 + 3 + 5 + \dots + (2L+1) = (L+1)^2
$$

{% endraw %}

<!-- 這個 $(L+1)^2$ 之後會不斷出現，值得先記住：$L = 0$ 時有 $1$ 個、$L = 1$ 時有 $4$ 個、$L = 2$ 時有 $9$ 個、$L = 3$ 時有 $16$ 個 -->

### 實際計算：每一個基底的數值

上面我們大概從圖中了解，這些不同的基底 $Y_{\ell m}$ 到底是長什麼樣子的，以下我們更進一步來看各個基底實際上的數值，以下列出前兩階（$\ell = 0, 1$）實際計算出來的數值：

- 第 $0$ 階（$1$ 個）：

{% raw %}

$$
Y_{00} = \frac{1}{2}\sqrt{\frac{1}{\pi}} \approx 0.2821
$$

{% endraw %}

- 第 $1$ 階（$3$ 個）：

{% raw %}

$$
Y_{1,-1} = \sqrt{\frac{3}{4\pi}}\, y \approx 0.4886\, y
\qquad
Y_{1,0} = \sqrt{\frac{3}{4\pi}}\, z \approx 0.4886\, z
\qquad
Y_{1,1} = \sqrt{\frac{3}{4\pi}}\, x \approx 0.4886\, x
$$

{% endraw %}

這些基底的數值為什麼是這樣呢？實際上都來自同一條**通式**被計算出來的，只要代入 $\ell$、$m$ 就能算出對應基底的公式與常數，不過這個計算過程非常複雜，詳情可以見文末 [補充資料 2. 球諧函數的通式](./#2-球諧函數的通式)

我們來看其中的一個基底 $Y_{1,0} \approx 0.4886 z$，實際試算它的三個方向：

- 北極 $\mathbf{d} = (0, 0, 1)$：代入 $z = 1$，得到 $0.4886$（最大值）
- 赤道 $\mathbf{d} = (1, 0, 0)$：代入 $z = 0$，得到 $0$
- 南極 $\mathbf{d} = (0, 0, -1)$：代入 $z = -1$，得到 $-0.4886$（最小值）

也就是說 $Y_{1,0}$ 描述的是「上正下負」的漸層花紋，回頭看上面的基底圖，它正是那個沿著 $z$ 軸擺放的上下兩瓣

### 對照圖與數值：算出來的 $0.4886$ 畫在圖上哪裡？

上一節我們算出：$Y_{1,0}$ 在北極方向 $(0, 0, 1)$ 的輸出值是 $0.4886$，現在回頭看基底圖上那個沿 $z$ 軸擺放的紅瓣，$0.4886$ 對應到是圖上的哪裡？紅瓣的頂點是畫在輸入方向的 $z = 1$，還是畫在輸出值的 $0.4886$？

實際上這些基底圖的畫法規則是 **畫點的位置 = 輸入方向 × 輸出值**，也就是說，曲面上每一點的畫法都是同樣的三個步驟：

1. **選一個輸入方向** $\mathbf{d}$：它是單位向量，長度恆為 $1$，只負責指定它的方向
2. **算出輸出值** $Y_{\ell m}(\mathbf{d})$：這是一個純數字，例如：北極方向 $z = 1$ 時，$Y_{1,0} = 0.4886 \times 1 = 0.4886$
3. **沿著方向 $\mathbf{d}$ 走「輸出值」這麼遠**，把點畫在那裡：輸出值決定離原點多遠，正負決定顏色（紅正、藍負)。所以北極方向 $z = 1$ 時，算出的值 $0.4886$ 就是整個紅瓣最上方的座標

但知道圖中座標的算法後，我覺得有一點一直想不通，基底 $Y_{1,0} = 0.4886 z$ 這個算式明明只跟 $z$ 有關，$x$ 完全沒參與計算，為什麼紅瓣會往 $x$ 方向張開？例如說紅瓣朝右上偏 $45°$ 的方向，它的 $z$ 分量是 $0.707$，代入公式後算出來的值是 $0.345$，而在這個過程中 $x$ 完全沒有出現，為什麼最後 $x$ 方向上還是會有值呢？

<img src="./sh_y10_basis.png" style="max-width: 360px;">

[圖片程式碼](https://github.com/bcjohnblue/python-plot/blob/main/2026/08/spherical-harmonics/sh_y10_basis.py)

不知道大家會不會跟我有一樣的疑問？下面我們來探討看看，一樣以 $Y_{1,0} \approx 0.4886 z$ 為例，把上一節試算過的三個方向，再加上一個偏 $45°$ 的方向，依上面的三個步驟整理成表：

<div class="sh-table">

| 輸入方向 $\mathbf{d}$           | $z$ 分量 | 輸出值 $0.4886 z$ | 畫在哪裡                                                           |
| :------------------------------ | :------: | :---------------: | :----------------------------------------------------------------- |
| 北極 $(0, 0, 1)$                |   $1$    |     $0.4886$      | 沿 $z$ 軸往上走 $0.4886$，落在 $(0, 0, 0.4886)$，即紅瓣頂點        |
| 偏 $45°$ $(0.707, 0, 0.707)$    | $0.707$  |      $0.345$      | 沿 $45°$ 方向走 $0.345$，落在 $(0.244, 0, 0.244)$，即紅瓣右側      |
| 左上 $45°$ $(-0.707, 0, 0.707)$ | $0.707$  |      $0.345$      | 沿左上 $45°$ 方向走 $0.345$，落在 $(-0.244, 0, 0.244)$，即紅瓣左側 |
| 赤道 $(1, 0, 0)$                |   $0$    |        $0$        | 走 $0$ 距離，落在原點 $(0, 0, 0)$                                  |
| 南極 $(0, 0, -1)$               |   $-1$   |     $-0.4886$     | 沿 $z$ 軸往下走 $0.4886$，落在 $(0, 0, -0.4886)$，即藍瓣底點       |

</div>

<br />

注意紅瓣的頂點不在 $z = 1$，而是在 $z = 0.4886$ 的位置。下面用一張二維、真實比例尺的剖面圖把這件事畫出來。虛線大圓代表所有的方向，長度恆為 $1$；紅藍色的小圓才是 $Y_{1,0}$ 的輸出值，表格中的五個方向都標在圖上：

<img src="./sh_y10_true_scale.png" style="max-width: 640px;">

[圖片程式碼](https://github.com/bcjohnblue/python-plot/blob/main/2026/08/spherical-harmonics/sh_y10_true_scale.py)

現在回到一開始的疑問：$x$ 沒有參與計算，為什麼紅瓣會往 $x$ 方向展開？原因是這樣的：

- **算值時**：只用到方向的 $z$ 分量：$0.4886 \times 0.707 \approx 0.345$，這一步確實跟 $x$ 無關
- **畫點時**：是沿著方向向量 $(0.707, 0, 0.707)$ 走 $0.345$ 的距離，因此落點的座標是 $0.345 \times (0.707, 0, 0.707) = (0.244, 0, 0.244)$，其中 $x = 0.244$ 是方向自己帶進來的，不是函數算出來的

把 $z = 0.707$ 代進 $Y_{1,0}$，在圖上的幾何意義是：把偏 $45°$ 這個方向繞著 $z$ 軸轉一圈，得到 $z$ 分量同樣是 $0.707$ 的所有方向，也就是單位球上的一個**緯度圈**。這一整圈方向算出來的距離都是同一個 $0.345$，而這個距離的確跟 $x$ 沒有任何關係

所以「代入同一個 $z$ 值」代表的意義是**算出來的距離一樣**。我們一開始混淆的地方在於：落點的 $x$ 座標有值，只是因為這一圈方向各自往不同的方向走了 $0.345$，那是曲面上落點的座標，跟「同一個 $z$ 值算出同樣的距離」是兩件不相干的事

希望以上的說明可以幫助跟我有同樣疑問的人，能夠了解這些球諧函數，實際上它是怎麼畫出來的

## 球面版的傅立葉級數 - 球諧函數

上一節我們瞭解了每個基底 $Y_{\ell m}$ 的編號、長相、數值怎麼算。但回想文章開頭，傅立葉級數的重點從來不是單一一條 $\sin$ 或 $\cos$ 波長什麼樣子，而是**任何函數都能拆成這些波的加權和**，就像方波的例子，一條條波形乘上各自的權重再疊起來，就能逼近目標函數

現在輪到球面來做同樣的事情：把「一條條 $\sin$、$\cos$ 波」換成上一節那「一顆顆花瓣」，任何球面函數 $f(\mathbf{d})$ 就都能展開成它們的加權和：

{% raw %}

$$
f(\mathbf{d}) = \sum_{\ell=0}^{\infty} \sum_{m=-\ell}^{\ell} c_{\ell m}\, Y_{\ell m}(\mathbf{d})
$$

{% endraw %}

其中 $c_{\ell m}$ 就是每個基底的權重係數。現在問題來了：給定一個函數 $f$，這些係數要怎麼求？

### 正交 - 求出係數的關鍵

求係數的關鍵是**正交**：只要基底之間彼此正交，每一個係數都可以用「內積」直接算出來，不需要解任何方程式。這一節會分成四步來說明：

1. 先介紹內積是什麼，以及它如何從向量推廣到函數
2. 用最簡單的二維向量，看出「正交」跟「求係數」之間的關係
3. 給一個對任何基底都成立的一般證明
4. 最後回到球諧函數，寫下它自己的正交關係，進而才能求出每一項係數

#### 內積：把係數求出來的工具

我們先說明一下**係數**跟**內積**是什麼，然後往下才可以繼續探討內積是怎麼把係數求出來的

**係數：每個基底要乘上幾倍**

回看展開式 $f = \sum \sum c_{\ell m} Y_{\ell m}$：係數 $c_{\ell m}$ 的意思就是「這個基底 $Y_{\ell m}$ 要乘上多少倍，全部加起來才會等於 $f$」，而目前我們知道：函數 $f$ 是給定的、每個基底 $Y_{\ell m}$ 也都是已知的固定公式，唯一未知的是所有係數 $c_{\ell m}$，要想辦法把它們一個一個找出來

**內積：量測「重疊程度」的工具**

我們從最熟悉的 **向量內積(Inner Product)** 出發講起，計算方式是「對應位置相乘、再全部相加」：

{% raw %}

$$
\mathbf{u} = (1, 0)
\qquad
\mathbf{f} = (3, 4)
\qquad \Longrightarrow \qquad
\mathbf{u} \cdot \mathbf{f} = 1 \times 3 + 0 \times 4 = 3
$$

{% endraw %}

而內積量出來的這個數字，幾何意義是**兩個向量的「重疊程度」**：

- 完全同方向時最大
- 反方向時為負
- 互相**垂直**時剛好是 $0$ - 這個特例待會就是主角

那麼對於函數來說要怎麼做內積？我們可以把函數視為一個「無限多維的向量」- 定義域上的每一個點是一個位置，函數在那個點的值就是那個位置的分量。比方說圓上的函數 $f(\theta)$，可以想成把 $\theta$ 從 $-\pi$ 到 $\pi$ 的每一個值都當成一格，每格填入 $f(\theta)$ 的數值，排出一條無限長的向量

如此一來，向量內積的做法就能原封不動地搬過來：「對應位置相乘」變成「對應點的函數值相乘」，而「全部相加」因為位置有無限多個，就換成**積分**。積分範圍是函數的整個定義域，圓的話是 $[-\pi, \pi]$、而球面上的函數區間是整個球面：

{% raw %}

$$
\underbrace{\langle f, g \rangle = \int_{-\pi}^{\pi} f(\theta)\, g(\theta)\, d\theta}_{\text{圓上的函數}}
\qquad \text{或} \qquad
\underbrace{\langle f, g \rangle = \int_{S^2} f(\mathbf{d})\, g(\mathbf{d})\, d\omega}_{\text{球面上的函數}}
$$

{% endraw %}

#### 基底正交的重要性

單純內積是無法把係數求出來的，這裡還缺一個關鍵「每一個基底之間必須互相正交」。只要這個前提成立，拿基底跟 $f$ 做內積，就能一個一個求出每項係數實際的值，很神奇吧。下面用最簡單的二維向量來理解為什麼：

**情況 A：兩個基底互相垂直**

取互相垂直的基底 $\mathbf{e}_1 = (1, 0)$、$\mathbf{e}_2 = (0, 1)$，要展開的向量是 $\mathbf{f} = (3, 4)$。拿兩個基底分別去跟 $\mathbf{f}$ 做內積：

{% raw %}

$$
\mathbf{e}_{1} \cdot \mathbf{f} = 1 \times 3 + 0 \times 4 = 3
\qquad
\mathbf{e}_{2} \cdot \mathbf{f} = 0 \times 3 + 1 \times 4 = 4
$$

{% endraw %}

而真正的係數，也就是兩個基底各乘幾倍才能加出 $\mathbf{f}$，一眼就看得出來：

{% raw %}

$$
\mathbf{f} = 3\, \mathbf{e}_{1} + 4\, \mathbf{e}_{2}
$$

{% endraw %}

結論：內積量到 $3$ 和 $4$，真正的係數也正好是 $3$ 和 $4$，這代表個別基底與 $f$ 做內積後，量到的數字就是我們想要求的係數

**情況 B：兩個基底不垂直**

基底改取 $\mathbf{u} = (1, 0)$、$\mathbf{v} = (1, 1)$，同樣展開 $\mathbf{f} = (3, 4)$，一樣拿兩個基底分別去量 $\mathbf{f}$：

{% raw %}

$$
\mathbf{u} \cdot \mathbf{f} = 1 \times 3 + 0 \times 4 = 3
\qquad
\mathbf{v} \cdot \mathbf{f} = 1 \times 3 + 1 \times 4 = 7
$$

{% endraw %}

但這次真正的係數沒辦法用看的，得解聯立方程才知道：

{% raw %}

$$
c_{1} (1, 0) + c_{2} (1, 1) = (c_{1} + c_{2},\; c_{2}) = (3, 4)
\qquad \Longrightarrow \qquad
c_{2} = 4, \quad c_{1} = -1
$$

{% endraw %}

也就是 $\mathbf{f} = -1 \cdot \mathbf{u} + 4 \cdot \mathbf{v}$：內積量到 $3$ 和 $7$，真正的係數卻是 $-1$ 和 $4$，量到的數字不能直接當係數用

兩種情況並排就看得很清楚：

|                  | 內積量到的 | 真正的係數 | 一致嗎 |
| :--------------- | :--------: | :--------: | :----: |
| 情況 A（垂直）   |  $3$、$4$  |  $3$、$4$  |  一致  |
| 情況 B（不垂直） |  $3$、$7$  | $-1$、$4$  | 不一致 |

這就是基底正交的重要性：**每個基底與 $f$ 做內積時，量到的數字完全不受其他基底影響，所以一次內積就能直接算出一個係數**

#### 一般證明：只要基底正交，內積就是係數

上面我們只用了最簡單的二維向量說明，但事實上不管是向量還是函數只要**基底之間彼此正交**，內積求出來的就是係數。以下我們來看一般的證明方式，設 $f$ 能展開成 $f = \sum_{n} c_{n} B_{n}$，且基底兩兩正交（$n \neq m$ 時 $\langle B_{n}, B_{m} \rangle = 0$）。想求某個特定的 $c_{m}$，就拿 $B_{m}$ 去跟等式兩邊做內積：

{% raw %}

$$
\langle f, B_{m} \rangle
= \left\langle \sum_{n} c_{n} B_{n},\; B_{m} \right\rangle
= \sum_{n} c_{n} \langle B_{n}, B_{m} \rangle
$$

{% endraw %}

右邊每一項都掛著一個 $\langle B_{n}, B_{m} \rangle$，重點來了正交性讓 $n \neq m$ 的項**全部歸零**，唯一剩餘的是 $n = m$ 那一項：

{% raw %}

$$
\langle f, B_{m} \rangle = c_{m} \langle B_{m}, B_{m} \rangle
\qquad \Longrightarrow \qquad
c_{m} = \frac{\langle f, B_{m} \rangle}{\langle B_{m}, B_{m} \rangle}
$$

{% endraw %}

有沒有看到，這就是正交的厲害之處：**一次內積就可以算出一個係數**。分母 $\langle B_{m}, B_{m} \rangle$ 是基底自己跟自己的內積，這裡如果我們把分母調成 $1$，這就叫做**歸一 (Normalized)**，係數就是簡單的一次內積 $c_{m} = \langle f, B_{m} \rangle$，連除法都不需要

### 球諧函數的正交歸一性

好了，上面介紹完正交的重要性，現在回到主軸 **球諧函數**，實際上球諧函數的「基底」，靠的正是同一個性質，而且更特別，它們彼此**正交歸一 (Orthonormal)**

**正交**：代表的是任兩個不同的基底在整個球面上積分的乘積為 $0$
**歸一**：則是自己跟自己的積分則為 $1$

以下數學式表達這個性質：

{% raw %}

$$
\int_{S^2} Y_{\ell m}(\mathbf{d})\, Y_{\ell' m'}(\mathbf{d})\, d\omega =
\begin{cases}
1, & \ell = \ell' \text{ 且 } m = m' \\
0, & \text{其他情況}
\end{cases}
$$

{% endraw %}

（$\int_{S^2} d\omega$ 表示對整個球面的所有方向積分，整個球面積分的立體角總和為 $4\pi$）

回到一開始的目的，我們已經知道球諧函數的各個基底，但每個基底的 **權重係數($c_{\ell m}$)** 並不知道要怎麼求出來，此時套用上一小節的正交技巧，就可以求出每一個係數了。首先把展開式兩邊跟某個基底 $Y_{\ell m}$ 做內積（也就是做球面積分）：

{% raw %}

$$
\int_{S^2} f\, Y_{\ell m}\, d\omega
= \int_{S^2} \left( \sum_{\ell'} \sum_{m'} c_{\ell' m'} Y_{\ell' m'} \right) Y_{\ell m}\, d\omega
= \sum_{\ell'} \sum_{m'} c_{\ell' m'} \int_{S^2} Y_{\ell' m'}\, Y_{\ell m}\, d\omega
$$

{% endraw %}

於是每一項都是 **兩個基底相乘再積分**，上面那條正交歸一性就在這裡發揮作用：

- 正交：只要 $(\ell', m') \neq (\ell, m)$，那一項積分就是 $0$，整項直接消失
- 歸一：唯一活下來的 $(\ell', m') = (\ell, m)$ 那一項，倍率剛好是 $1$，完全不用計算基底自己跟自己的內積

{% raw %}

$$
c_{\ell m} = \int_{S^2} f(\mathbf{d})\, Y_{\ell m}(\mathbf{d})\, d\omega
$$

{% endraw %}

於是每一項的係數都能套用上式的積分這樣獨立求出，互不干擾

### 範例 1：計算 $f = 1 + z$ 的各項係數

我們用一個實際的例子把整個流程走一遍，來更好的理解各項係數是怎麼算出來的，假設有一個球面函數：

{% raw %}

$$
f(\mathbf{d}) = 1 + z
$$

{% endraw %}

它的意思是：越靠近北極（$z = 1$）值越大（最大 $2$）、越靠近南極（$z = -1$）值越小（最小 $0$），是一個「上亮下暗」的漸層，接著我們來求它的球諧係數

#### 求第 $0$ 階係數

把 $f$ 與 $Y_{00}$ 做球面積分。由於 $Y_{00} \approx 0.2821$ 是常數，可以直接提出來：

{% raw %}

$$
c_{00} = \int_{S^2} (1 + z) \cdot Y_{00}\, d\omega
= 0.2821 \left( \int_{S^2} 1\, d\omega + \int_{S^2} z\, d\omega \right)
$$

{% endraw %}

其中 $\int_{S^2} 1 d\omega = 4\pi$（球面立體角總和），而 $\int_{S^2} z d\omega = 0$（$z$ 在北半球為正、南半球為負，互相對消），所以：

{% raw %}

$$
c_{00} = 0.2821 \times 4\pi \approx 3.5449
$$

{% endraw %}

#### 求第 $1$ 階係數

**$Y_{1,0}$ 的係數 $c_{1,0}$**

把 $f$ 與 $Y_{1,0} \approx 0.4886 z$ 做球面積分：

{% raw %}

$$
c_{1,0} = \int_{S^2} (1 + z) \cdot 0.4886\, z\, d\omega
= 0.4886 \left( \int_{S^2} z\, d\omega + \int_{S^2} z^2\, d\omega \right)
$$

{% endraw %}

第一項一樣是 $0$；第二項 $\int_{S^2} z^2 d\omega = 4\pi / 3$ 是從文末 [補充資料 3](./#3-三個球面積分的推導) 算出來的，所以：

{% raw %}

$$
c_{1,0} = 0.4886 \times \frac{4\pi}{3} \approx 2.0466
$$

{% endraw %}

<!-- P.S. 到這裡用到的三個積分 $\int_{S^2} 1 d\omega = 4\pi$、$\int_{S^2} z d\omega = 0$、$\int_{S^2} z^2 d\omega = 4\pi / 3$ 實際計算的流程在文末 [補充資料 3. 三個球面積分的推導](./#3-三個球面積分的推導) -->

**$Y_{1,1}$ 的係數 $c_{1,1}$**

$Y_{1,1} \approx 0.4886 x$ 也照同樣的流程算一次：

{% raw %}

$$
c_{1,1} = \int_{S^2} (1 + z) \cdot 0.4886\, x\, d\omega
= 0.4886 \left( \int_{S^2} x\, d\omega + \int_{S^2} x z\, d\omega \right)
$$

{% endraw %}

這兩個積分裡都帶著 $x$，改用球座標來算（$x = \sin\theta \cos\phi$、$z = \cos\theta$、$d\omega = \sin\theta d\theta d\phi$，細節見 [補充資料 3. 三個球面積分的推導](./#3-三個球面積分的推導)）：

{% raw %}

$$
\begin{aligned}
\int_{S^2} x\, d\omega
&= \int_{0}^{2\pi} \cos\phi\, d\phi \int_{0}^{\pi} \sin^2\theta\, d\theta
= 0 \times \frac{\pi}{2} = 0 \\[6pt]
\int_{S^2} x z\, d\omega
&= \int_{0}^{2\pi} \cos\phi\, d\phi \int_{0}^{\pi} \sin^2\theta \cos\theta\, d\theta
= 0 \times 0 = 0
\end{aligned}
$$

{% endraw %}

關鍵在 $\phi$ 那一段：$\cos\phi$ 繞一整圈積分 $\int_{0}^{2\pi} \cos\phi d\phi = \sin 2\pi - \sin 0 = 0$，不管 $\theta$ 那邊積出什麼，整個積分都會被這個 $0$ 乘掉，所以 $c_{1,1} = 0$

**$Y_{1,-1}$ 的係數 $c_{1,-1}$**

最後是 $Y_{1,-1} \approx 0.4886 y$，一樣先展開：

{% raw %}

$$
c_{1,-1} = \int_{S^2} (1 + z) \cdot 0.4886\, y\, d\omega
= 0.4886 \left( \int_{S^2} y\, d\omega + \int_{S^2} y z\, d\omega \right)
$$

{% endraw %}

換成球座標（$y = \sin\theta \sin\phi$、$z = \cos\theta$、$d\omega = \sin\theta d\theta d\phi$，細節見 [補充資料 3. 三個球面積分的推導](./#3-三個球面積分的推導)）：

{% raw %}

$$
\begin{aligned}
\int_{S^2} y\, d\omega
&= \int_{0}^{2\pi} \sin\phi\, d\phi \int_{0}^{\pi} \sin^2\theta\, d\theta
= 0 \times \frac{\pi}{2} = 0 \\[6pt]
\int_{S^2} y z\, d\omega
&= \int_{0}^{2\pi} \sin\phi\, d\phi \int_{0}^{\pi} \sin^2\theta \cos\theta\, d\theta
= 0 \times 0 = 0
\end{aligned}
$$

{% endraw %}

$\theta$ 那一段跟 $c_{1,1}$ 完全相同，差別只在 $\phi$ 的部分從 $\cos\phi$ 換成 $\sin\phi$，但繞一整圈積分同樣是 $\int_{0}^{2\pi} \sin\phi d\phi = -\cos 2\pi + \cos 0 = 0$，所以：

{% raw %}

$$
c_{1,-1} = 0.4886 \times (0 + 0) = 0
$$

{% endraw %}

#### 第 $2$ 階以上的係數

第 $2$ 階以上有無限多個基底，不可能一個一個積下去，但其實不用算也可以知道答案全是 $0$，原因有以下兩點：

第一點是**帶有 $x$、$y$ 的基底**。$f = 1 + z$ 裡完全沒有 $x$、$y$ 的成分，所以任何含 $x$ 或 $y$ 的基底都量不到東西，這也正是剛才 $c_{1,1}$、$c_{1,-1}$ 會積出 $0$ 的原因，第 $2$ 階以上像 $Y_{2,1}$、$Y_{2,2}$ 這些基底也是同樣的道理。可以回頭對照前面 [直接看圖：每一階長什麼樣子](./#直接看圖：每一階長什麼樣子) 的基底圖：每一排只有正中間那顆（$m = 0$）是繞著 $z$ 軸對稱的，其他所有花紋的瓣都是往 $x$、$y$ 方向長出去的，而 $f$ 只有 $z$ 軸的變化，其他方向上自然量不到東西

第二點是**只帶 $z$ 的基底**，例如 $Y_{2,0} \propto 3z^2 - 1$（實際推導見文末 [補充資料 2. 球諧函數的通式](./#2-球諧函數的通式) 的第 $2$ 階部分）。它跟 $f$ 一樣只跟 $z$ 有關，第一點的理由對它不管用，但可以這樣想：$f = 1 + z$ 對 $z$ 來說只是一條直線，而 $Y_{2,0}$ 是 $z$ 的二次式、更高階的 $Y_{\ell,0}$ 是 $z$ 的更高次式，一條直線裡沒有任何「彎曲」的成分可以讓它們量到，係數自然是 $0$

我們實際拿 $Y_{2,0}$ 算一次驗證。它的明確式是：

{% raw %}

$$
Y_{2,0} = \frac{1}{4}\sqrt{\frac{5}{\pi}}\, (3z^2 - 1) \approx 0.3154\, (3z^2 - 1)
$$

{% endraw %}

跟 $f$ 做球面積分並展開：

{% raw %}

$$
\begin{aligned}
c_{2,0}
&= \int_{S^2} (1 + z) \cdot 0.3154\, (3z^2 - 1)\, d\omega \\[6pt]
&= 0.3154 \left( 3 \int_{S^2} z^2\, d\omega - \int_{S^2} 1\, d\omega + 3 \int_{S^2} z^3\, d\omega - \int_{S^2} z\, d\omega \right)
\end{aligned}
$$

{% endraw %}

其中的三個值，我們前面都有求過了：$\int_{S^2} z^2 d\omega = 4\pi / 3$、$\int_{S^2} 1 d\omega = 4\pi$、$\int_{S^2} z d\omega = 0$。新出現的 $\int_{S^2} z^3 d\omega$ 跟 $z$ 的情況一樣，北半球為正、南半球為負，兩邊剛好對消為 $0$。全部代回去：

{% raw %}

$$
c_{2,0} = 0.3154 \left( 3 \times \frac{4\pi}{3} - 4\pi + 0 - 0 \right) = 0.3154 \times 0 = 0
$$

{% endraw %}

括號裡 $3 \times \frac{4\pi}{3}$ 與 $4\pi$ 剛好抵消，這個 $-1$ 不是隨便放的：如果 $Y_{2,0}$ 只有 $3z^2$，它跟常數 $Y_{00}$ 的內積會是 $3 \times \frac{4\pi}{3} = 4\pi$，並不為 $0$；正是減掉這個 $1$ 之後才剛好歸零，這也是 [勒讓德多項式](./#2-球諧函數的通式) 被設計成兩兩正交的結果

#### 重建驗證

把算出來的係數代回展開式，由於 $c_{1,1}$、$c_{1,-1}$ 與第 $2$ 階以上的係數全部是 $0$，展開式只剩下兩項，$f$ 就近似成：

{% raw %}

$$
f(\mathbf{d}) \approx c_{00}\, Y_{00}(\mathbf{d}) + c_{1,0}\, Y_{1,0}(\mathbf{d})
$$

{% endraw %}

接著把上面求出來的係數與基底的數值代進去：

{% raw %}

$$
f(\mathbf{d}) \approx 3.5449 \times 0.2821 + 2.0466 \times 0.4886\, z
= 1 + z
$$

{% endraw %}

完美還原了原函數！由於 $f = 1 + z$ 本身就只含常數與一次項，因此前兩階基底就足以精準的表示原式

### 範例 2：用前幾階近似一顆橢球

範例 1 的 $f = 1 + z$ 剛好是低階多項式，兩項就能精確還原，但實際上大多數的球面函數都不是多項式，要精確表達需要無限多階的係數，這時「只取前幾階」得到的就是一個**近似**。這一節挑一顆橢球來看看近似的效果有多好

橢球的三個半軸分別沿著 $x$、$y$、$z$ 軸，長度為 $a = 1.2$、$b = 1$、$c = 0.8$，我們把球面函數 $f(\mathbf{d})$ 定義成「從球心沿方向 $\mathbf{d}$ 走到橢球表面的距離」：

{% raw %}

$$
f(\mathbf{d}) = \frac{1}{\sqrt{\dfrac{x^2}{a^2} + \dfrac{y^2}{b^2} + \dfrac{z^2}{c^2}}}
\qquad
a = 1.2,\ b = 1,\ c = 0.8
$$

{% endraw %}

代幾個方向確認一下：朝 $x$ 軸 $\mathbf{d} = (1, 0, 0)$ 時分母是 $1/a$，所以 $f = a = 1.2$；同理朝 $y$ 軸得到 $1$、朝 $z$ 軸得到 $0.8$。它是一顆 $x$ 方向最長、$z$ 方向最扁的橢球，而且因為有根號，$f$ 不是 $x, y, z$ 的多項式

先把這顆橢球畫出來看看。圖中的曲面就是以 $r = f(\mathbf{d})$ 的距離畫出來的形狀，紅色三條線是三個半軸，端點的紅點正好落在橢球表面上，長度分別是 $a$、$b$、$c$；顏色對應 $f$ 的大小，朝 $x$ 軸的方向最亮（距離最遠）、朝 $z$ 軸最暗（距離最近）：

<img src="./sh_ellipsoid_target.png" style="max-width: 600px;">

[圖片程式碼](https://github.com/bcjohnblue/python-plot/blob/main/2026/08/spherical-harmonics/sh_ellipsoid_target.py)

#### 哪些係數不用算就知道是 $0$

在動手積分之前，先用對稱性把一大半的係數排除掉：

- **所有奇數階（$\ell = 1, 3, \dots$）的係數都是 $0$**。以 $c_{1,0} = \int_{S^2} f \cdot 0.4886 z d\omega$ 為例：橢球上下對稱，北半球任一點 $(x, y, z)$ 和它正下方的鏡像點 $(x, y, -z)$ 到表面的距離一樣，$f$ 值相同，但 $z$ 在這兩點正好差一個負號。所以兩點的貢獻是 $f z$ 與 $-f z$，剛好成對抵消，因此積分不用算就可以推算出是 $0$。$Y_{1,1} \propto x$、$Y_{1,-1} \propto y$ 換成左右、前後鏡像也是同樣的道理，而第 $3$ 階以上的奇數階基底也都是同樣的狀況
- **第 $2$ 階裡帶混合項的三個基底 $Y_{2,-2} \propto xy$、$Y_{2,-1} \propto yz$、$Y_{2,1} \propto xz$ 的係數也是 $0$**（這三個基底的詳細推導見文末 [補充資料 2. 球諧函數的通式](./#2-球諧函數的通式) 的第 $2$ 階部分）。因為橢球的三個軸剛好對齊座標軸，把 $x$ 換成 $-x$（或 $y$、$z$）$f$ 都不變，但 $xy$ 這類乘積會變號，同樣正負對消

於是第 $2$ 階以內只剩下三個係數要算：$c_{00}$、$c_{2,0}$ 與 $c_{2,2}$

#### 數值積分求係數

$f$ 帶著根號，跟基底相乘後的積分沒有簡單的封閉解，這裡改用數值積分：把球面切成很多小格子，每一格算出 $f \times Y_{\ell m} \times$ 面積元素，全部加起來就是 $c_{\ell m} = \int_{S^2} f Y_{\ell m} d\omega$ 的近似值，而算到第 $4$ 階的係數如下：

|   係數    |          對應基底           |   數值    |
| :-------: | :-------------------------: | :-------: |
| $c_{00}$  |          $Y_{00}$           | $3.4588$  |
| $c_{2,0}$ | $Y_{2,0} \propto 3z^2 - 1$  | $-0.3250$ |
| $c_{2,2}$ | $Y_{2,2} \propto x^2 - y^2$ | $0.1566$  |
| $c_{4,0}$ |          $Y_{4,0}$          | $0.0404$  |
| $c_{4,2}$ |          $Y_{4,2}$          | $-0.0245$ |
| $c_{4,4}$ |          $Y_{4,4}$          | $0.0092$  |

[係數計算程式碼](https://github.com/bcjohnblue/python-plot/blob/main/2026/08/spherical-harmonics/sh_ellipsoid_truncation.py)

可以看到係數隨階數快速變小，第 $4$ 階的係數只有第 $2$ 階的十分之一左右，實際上通常只會挑前幾階來近似，下面我們就先只取到第 $2$ 階，看看近似的效果如何

#### 近似到第 $2$ 階

把三個係數代回展開式，並把基底的常數乘進去：

{% raw %}

$$
\begin{aligned}
f(\mathbf{d}) &\approx c_{00}\, Y_{00} + c_{2,0}\, Y_{2,0} + c_{2,2}\, Y_{2,2} \\[6pt]
&= 3.4588 \times 0.2821 - 0.3250 \times 0.3154\, (3z^2 - 1) + 0.1566 \times 0.5463\, (x^2 - y^2) \\[6pt]
&\approx 0.9757 - 0.1025\, (3z^2 - 1) + 0.0855\, (x^2 - y^2)
\end{aligned}
$$

{% endraw %}

第一項 $0.9757$ 是 $f$ 在球面上的平均值，也就是 $L = 0$ 時的重建結果 - 一顆半徑 $0.9757$ 的正圓球。後面兩項則負責把它「壓扁、拉長」成橢球，代入三個軸的方向跟真實值比一比：

|  方向  | 真實值 |               $L = 2$ 近似               |  誤差   |
| :----: | :----: | :--------------------------------------: | :-----: |
| $x$ 軸 | $1.2$  | $0.9757 + 0.1025 + 0.0855 \approx 1.164$ | $0.036$ |
| $y$ 軸 | $1.0$  | $0.9757 + 0.1025 - 0.0855 \approx 0.993$ | $0.007$ |
| $z$ 軸 | $0.8$  |     $0.9757 - 0.2050 \approx 0.771$      | $0.029$ |

可以看到只用三個非零係數，最大誤差就只有 $0.036$，下圖畫出近似到 $L = 0, 2, 4$ 三種的結果：

<img src="./sh_ellipsoid_truncation.png" style="max-width: 100%;">

[圖片程式碼](https://github.com/bcjohnblue/python-plot/blob/main/2026/08/spherical-harmonics/sh_ellipsoid_truncation.py)

$L = 0$ 只是一顆球，看不出任何方向性，但 $L = 2$ 時已經是一顆肉眼幾乎分不出差別的橢球，再加上第 $4$ 階，最大誤差更可以降到 $0.006$，這就是球諧函數厲害的地方，僅僅只用三個數字($L = 2$)就可以描述出這一顆橢球的形狀

## 結論

回顧一下這篇文章的重點：

1. **球諧函數是「球面版的傅立葉基底」**：傅立葉級數把圓上的函數拆成 $\sin$ $\cos$ 的加權和，球諧函數把球面上的函數拆成 $Y_{\ell m}$ 的加權和
2. **兩個索引決定一切**：階數 $\ell$ 是頻率（越高花瓣越細碎）、序數 $m$ 是同階內的編號；取到第 $L$ 階共需 $(L+1)^2$ 個係數
3. **正交歸一性讓係數可以獨立求出**：基底彼此「互相垂直」，每個係數就是函數與該基底的球面內積
4. **只取前幾階就能近似**：係數隨階數快速變小，像範例 2 的橢球只用第 $2$ 階以內的三個非零係數，誤差就壓在 $3$% 以內，用少少的數字就能近似描述一整個球面函數

其中第 4 點的壓縮效率正是它實用價值的核心。[下一篇文章](/2026/08/09/球諧函數-2-在-3d-gaussian-splatting-中的應用/) 我們就來看 **3D Gaussian Splatting** 如何運用這個工具，讓每顆高斯帶著 $48$ 個球諧係數，用極低的成本表現出「顏色隨視角改變」的真實感

## 補充資料

#### 1. 傅立葉級數的常數項為什麼等於 $a_{0}/2$？

我們知道傅立葉級數是由 $\cos \theta$ 跟 $\sin \theta$ 無限組合的一個函數，但為什麼這個函數最前面的常數項是 $a_{0}/2$ 呢？下面我們來證明這一點

為了方便把右式的 $\cos \theta$, $\sin \theta$ 簡化掉，我們嘗試將傅立葉展開式的兩邊同時在一個完整週期 $[-\pi, \pi]$ 上積分：

{% raw %}

$$
\int_{-\pi}^{\pi} f(\theta)\, d\theta
= \int_{-\pi}^{\pi} \frac{a_{0}}{2}\, d\theta
+ \sum_{n=1}^{\infty} \left( a_{n} \int_{-\pi}^{\pi} \cos n\theta \, d\theta + b_{n} \int_{-\pi}^{\pi} \sin n\theta \, d\theta \right)
$$

{% endraw %}

這時我們發現：$\cos n\theta$ 與 $\sin n\theta$ 在 $[-\pi, \pi]$ 內其面積剛好可以完全對消：

{% raw %}

$$
\int_{-\pi}^{\pi} \cos n\theta \, d\theta = 0
\qquad
\int_{-\pi}^{\pi} \sin n\theta \, d\theta = 0
\qquad (n = 1, 2, 3, \dots)
$$

{% endraw %}

下圖可以明顯看到不管 $n$ 是多少，曲線在 **軸上方（紅色）** 與 **軸下方（藍色）** 圍出的面積都一樣大，加起來剛好是 $0$：

<img src="./fourier_sincos_cancellation.png" style="max-width: 700px;">

[圖片程式碼](https://github.com/bcjohnblue/python-plot/blob/main/2026/08/spherical-harmonics/fourier_sincos_cancellation.py)

於是右邊無窮多項的 $\cos n\theta$ 與 $\sin n\theta$ 全部歸零，唯一活下來的只有常數項：

{% raw %}

$$
\int_{-\pi}^{\pi} f(\theta)\, d\theta
= \int_{-\pi}^{\pi} \frac{a_{0}}{2}\, d\theta
= \frac{a_{0}}{2} \times 2\pi
= a_{0}\, \pi
$$

{% endraw %}

最後把積分總量除以週期長度 $2\pi$，就得到函數的平均值：

{% raw %}

$$
\text{平均值} = \frac{1}{2\pi} \int_{-\pi}^{\pi} f(\theta)\, d\theta
= \frac{a_{0}\, \pi}{2\pi}
= \frac{a_{0}}{2}
$$

{% endraw %}

證明完成，常數項 $a_{0}/2$ 恰好就是 $f$ 在一個週期上的平均值

#### 2. 球諧函數的通式

正文「把公式攤開來看」一節列出的明確式其實都來自同一條通式。用球座標 $(\theta, \phi)$ 表示，實數球諧函數的通式為：

{% raw %}

$$
Y_{\ell m}(\theta, \phi) =
\begin{cases}
\sqrt{2}\, K_{\ell m} \cos(m\phi)\, P_{\ell}^{m}(\cos\theta), & m > 0 \\[0.8em]
K_{\ell 0}\, P_{\ell}^{0}(\cos\theta), & m = 0 \\[0.8em]
\sqrt{2}\, K_{\ell |m|} \sin(|m|\phi)\, P_{\ell}^{|m|}(\cos\theta), & m < 0
\end{cases}
$$

{% endraw %}

先將式子裡的每個符號清晰的解釋出來：

- $\ell$：**階數**（$0, 1, 2, \ldots$）- 文章中提到的頻率層級，$\ell$ 越大花紋越細
- $m$：同一階內的**編號**，範圍是 $-\ell$ 到 $\ell$，所以第 $\ell$ 階共有 $2\ell + 1$ 個基底
- $|m|$：$m$ 的**絕對值**（例如 $m = -2$ 時 $|m| = 2$）
- $\theta$、$\phi$：**球座標的兩個角度** - $\theta$ 是與 $z$ 軸的夾角（緯度方向）、$\phi$ 是繞 $z$ 軸的方位角（經度方向）
- $P_{\ell}^{m}(\cos\theta)$：**伴隨勒讓德多項式 (Associated Legendre Polynomials)**，控制花紋**沿緯度方向**（上下）的起伏
- $\cos(m\phi)$ 與 $\sin(m\phi)$：控制花紋**繞經度方向**（水平轉圈）的起伏，$|m|$ 越大繞一圈的起伏次數越多
- $K_{\ell m}$：**歸一化常數**，把每個基底縮放到「自己與自己的球面積分」恰好等於 $1$（也就是文章中提到的歸一性）

其中歸一化常數 $K_{\ell m}$ 的完整式為：

{% raw %}

$$
K_{\ell m} = \sqrt{\frac{2\ell+1}{4\pi} \cdot \frac{(\ell - |m|)!}{(\ell + |m|)!}}
$$

{% endraw %}

接著我們看看 $P_{\ell}^{m}$ 本身是怎麼算出來的？連同最後代回通式的驗算，整個過程分三步。因為定義中需要對變數微分，以下先用一般變數 $u$ 表示 $\cos\theta$

**第一步：算出勒讓德多項式 $P_{\ell}$**

勒讓德多項式的算法就是下面這一條式子，它有個名字叫 [羅德里格公式 (Rodrigues' formula)](https://math.libretexts.org/Bookshelves/Differential_Equations/A_First_Course_in_Differential_Equations_for_Scientists_and_Engineers_%28Herman%29/04%3A_Series_Solutions/4.05:_Legendre_Polynomials)：

{% raw %}

$$
P_{\ell}(u) = \frac{1}{2^{\ell}\, \ell!} \frac{d^{\ell}}{du^{\ell}} (u^{2} - 1)^{\ell}
$$

{% endraw %}

這條式子做的事情是：把 $(u^{2} - 1)^{\ell}$ **微分 $\ell$ 次**、再除以常數 $2^{\ell} \ell!$，得到的多項式就是第 $\ell$ 條勒讓德多項式

下表把 $\ell = 0 \sim 3$ 的計算過程整個攤開，由左至右依序是「先展開、微分 $\ell$ 次、乘上常數、得到結果」

<div class="sh-table">

| $\ell$ | $(u^{2} - 1)^{\ell}$          | $\frac{d^{\ell}}{du^{\ell}} (u^{2} - 1)^{\ell}$ | $\frac{1}{2^{\ell} \ell!}$ | $P_{\ell}(u)$              |
| ------ | ----------------------------- | ----------------------------------------------- | -------------------------- | -------------------------- |
| $0$    | $1$                           | $1$                                             | $1$                        | $1$                        |
| $1$    | $u^{2} - 1$                   | $2u$                                            | $\frac{1}{2}$              | $u$                        |
| $2$    | $u^{4} - 2u^{2} + 1$          | $12u^{2} - 4$                                   | $\frac{1}{8}$              | $\frac{1}{2}(3u^{2} - 1)$  |
| $3$    | $u^{6} - 3u^{4} + 3u^{2} - 1$ | $120u^{3} - 72u$                                | $\frac{1}{48}$             | $\frac{1}{2}(5u^{3} - 3u)$ |

</div>

改編自 [Mathematics LibreTexts 的 Table 4.5.1](https://math.libretexts.org/Bookshelves/Differential_Equations/A_First_Course_in_Differential_Equations_for_Scientists_and_Engineers_%28Herman%29/04%3A_Series_Solutions/4.05:_Legendre_Polynomials)

P.S. 羅德里格公式只告訴我們「怎麼算」，但這些多項式的長相其實有更直觀的來源：它們就是把 $1$、$u$、$u^2$、$u^3$ 這串單項式，在 $-1$ 到 $1$ 的區間上一個一個**正交化**的結果。以 $P_2$ 為例，$u^2$ 跟常數 $1$ 的內積是 $\int_{-1}^{1} u^2 du = 2/3$，並不為 $0$，所以要扣掉它在常數方向上的分量 $(2/3) / 2 = 1/3$，得到 $u^2 - 1/3$；再乘上 $3/2$ 整理係數，就是表中的 $\frac{1}{2}(3u^2 - 1)$。至於跟 $u$ 垂直則不用額外處理，因為 $u^2$ 是偶函數、$u$ 是奇函數，相乘積分自然為 $0$

**第二步：添加上標 $m$**

對第一步的結果再微分 $m$ 次、乘上因子 $(1 - u^{2})^{m/2}$，就得到伴隨勒讓德多項式：

{% raw %}

$$
P_{\ell}^{m}(u) = (1 - u^{2})^{m/2}\, \frac{d^{m}}{du^{m}} P_{\ell}(u)
$$

{% endraw %}

拿 $P_{2}^{1}$（$\ell = 2$、$m = 1$）實際算一次：

1. 第一步：羅德里格公式給出 $P_{2}(u) = \frac{1}{2}(3u^{2} - 1)$
2. 第二步：對它微分 $1$ 次得 $3u$，乘上 $(1 - u^{2})^{1/2}$，得到 $P_{2}^{1}(u) = 3u\sqrt{1 - u^{2}}$
3. 代入 $u = \cos\theta$（此時 $\sqrt{1 - u^{2}} = \sin\theta$），便得到 $3\cos\theta \sin\theta$

接著把前三階（$\ell = 0, 1, 2$）會用到的伴隨勒讓德多項式全部算出來，同樣用表格把計算過程攤開 - 由左至右依序是「從上表查出 $P_{\ell}(u)$、微分 $m$ 次、乘上因子、代入 $u = \cos\theta$ 得到結果」：

<div class="sh-table">

| $\ell$ | $m$ | $P_{\ell}(u)$             | $\frac{d^{m}}{du^{m}} P_{\ell}(u)$ | $(1 - u^{2})^{m/2}$ | $P_{\ell}^{m}(\cos\theta)$         |
| ------ | --- | ------------------------- | ---------------------------------- | ------------------- | ---------------------------------- |
| $0$    | $0$ | $1$                       | $1$                                | $1$                 | $1$                                |
| $1$    | $0$ | $u$                       | $u$                                | $1$                 | $\cos\theta$                       |
| $1$    | $1$ | $u$                       | $1$                                | $\sqrt{1 - u^{2}}$  | $\sin\theta$                       |
| $2$    | $0$ | $\frac{1}{2}(3u^{2} - 1)$ | $\frac{1}{2}(3u^{2} - 1)$          | $1$                 | $\frac{1}{2}(3\cos^{2}\theta - 1)$ |
| $2$    | $1$ | $\frac{1}{2}(3u^{2} - 1)$ | $3u$                               | $\sqrt{1 - u^{2}}$  | $3\cos\theta \sin\theta$           |
| $2$    | $2$ | $\frac{1}{2}(3u^{2} - 1)$ | $3$                                | $1 - u^{2}$         | $3\sin^{2}\theta$                  |

</div>

<br />

**第三步：代回通式、換回直角座標 $(x, y, z)$**

最後把上面算好的 $P_{\ell}^{m}$ 和 $K_{\ell m}$ 代回通式，再配上 [球座標換回直角座標的對應關係](https://en.wikipedia.org/wiki/Spherical_coordinate_system#Cartesian_coordinates)：

{% raw %}

$$
x = \sin\theta \cos\phi
\qquad
y = \sin\theta \sin\phi
\qquad
z = \cos\theta
$$

{% endraw %}

**第 $0$ 階（$\ell = 0$）**：

- $Y_{00}$：只有這一條，走 $m = 0$ 分支，查表得 $P_{0}^{0}(\cos\theta) = 1$：

{% raw %}

$$
Y_{00} = K_{00}\, P_{0}^{0}(\cos\theta)
= \sqrt{\frac{2 \cdot 0 + 1}{4\pi}} \cdot 1
= \frac{1}{2}\sqrt{\frac{1}{\pi}}
$$

{% endraw %}

**第 $1$ 階（$\ell = 1$）**：

- $Y_{1,0}$：走 $m = 0$ 分支，查表得 $P_{1}^{0}(\cos\theta) = \cos\theta$：

{% raw %}

$$
Y_{1,0} = K_{10}\, P_{1}^{0}(\cos\theta)
= \sqrt{\frac{3}{4\pi}}\, \cos\theta
= \sqrt{\frac{3}{4\pi}}\, z
$$

{% endraw %}

- $Y_{1,1}$：走 $m > 0$ 分支，先算歸一化常數 $K_{11} = \sqrt{\frac{3}{4\pi} \cdot \frac{0!}{2!}} = \sqrt{\frac{3}{8\pi}}$，再組合起來：

{% raw %}

$$
Y_{1,1} = \sqrt{2} \cdot \sqrt{\frac{3}{8\pi}} \cdot \cos\phi \cdot \sin\theta
= \sqrt{\frac{3}{4\pi}}\, \sin\theta \cos\phi
= \sqrt{\frac{3}{4\pi}}\, x
$$

{% endraw %}

- $Y_{1,-1}$：走 $m < 0$ 分支，跟上面唯一的差別是 $\cos\phi$ 換成 $\sin\phi$，於是 $\sin\theta \sin\phi$ 換回直角座標就是 $y$，得到

{% raw %}

$$
Y_{1,-1} = \sqrt{\frac{3}{4\pi}}\, y
$$

{% endraw %}

**第 $2$ 階（$\ell = 2$）**：五條全部算一次

- $Y_{2,0}$：走 $m = 0$ 分支，查表得 $P_{2}^{0}(\cos\theta) = \frac{1}{2}(3\cos^{2}\theta - 1)$：

{% raw %}

$$
Y_{2,0} = K_{20}\, P_{2}^{0}(\cos\theta)
= \sqrt{\frac{5}{4\pi}} \cdot \frac{1}{2}(3\cos^{2}\theta - 1)
= \frac{1}{4}\sqrt{\frac{5}{\pi}}\, (3z^{2} - 1)
$$

{% endraw %}

- $Y_{2,1}$：走 $m > 0$ 分支，先算歸一化常數 $K_{21} = \sqrt{\frac{5}{4\pi} \cdot \frac{1!}{3!}} = \sqrt{\frac{5}{24\pi}}$，查表得 $P_{2}^{1}(\cos\theta) = 3\cos\theta \sin\theta$，代入通式：

{% raw %}

$$
Y_{2,1} = \sqrt{2} \cdot \sqrt{\frac{5}{24\pi}} \cdot \cos\phi \cdot 3\cos\theta \sin\theta
= \frac{1}{2}\sqrt{\frac{15}{\pi}}\, \sin\theta \cos\phi \cdot \cos\theta
= \frac{1}{2}\sqrt{\frac{15}{\pi}}\, xz
$$

{% endraw %}

- $Y_{2,-1}$：走 $m < 0$ 分支，$\cos\phi$ 換成 $\sin\phi$，於是 $\sin\theta \sin\phi \cdot \cos\theta$ 換回直角座標就是 $yz$：

{% raw %}

$$
Y_{2,-1} = \frac{1}{2}\sqrt{\frac{15}{\pi}}\, yz
$$

{% endraw %}

- $Y_{2,2}$：走 $m > 0$ 分支，先算歸一化常數 $K_{22} = \sqrt{\frac{5}{4\pi} \cdot \frac{0!}{4!}} = \sqrt{\frac{5}{96\pi}}$，代入通式：

{% raw %}

$$
Y_{2,2} = \sqrt{2} \cdot \sqrt{\frac{5}{96\pi}} \cdot \cos(2\phi) \cdot 3\sin^{2}\theta
= \frac{1}{4}\sqrt{\frac{15}{\pi}}\, \sin^{2}\theta \cos(2\phi)
$$

{% endraw %}

接著用倍角公式 $\cos(2\phi) = \cos^{2}\phi - \sin^{2}\phi$ 換回直角座標：

{% raw %}

$$
\sin^{2}\theta \cos(2\phi)
= (\sin\theta \cos\phi)^{2} - (\sin\theta \sin\phi)^{2}
= x^{2} - y^{2}
$$

{% endraw %}

最後可得：

{% raw %}

$$
Y_{2,2} = \frac{1}{4}\sqrt{\frac{15}{\pi}}\, (x^{2} - y^{2})
$$

{% endraw %}

- $Y_{2,-2}$：走 $m < 0$ 分支，$\cos(2\phi)$ 換成 $\sin(2\phi)$，其餘與 $Y_{2,2}$ 相同：

{% raw %}

$$
Y_{2,-2} = \sqrt{2} \cdot \sqrt{\frac{5}{96\pi}} \cdot \sin(2\phi) \cdot 3\sin^{2}\theta
= \frac{1}{4}\sqrt{\frac{15}{\pi}}\, \sin^{2}\theta \sin(2\phi)
$$

{% endraw %}

這次用的倍角公式是 $\sin(2\phi) = 2\sin\phi \cos\phi$：

{% raw %}

$$
\sin^{2}\theta \sin(2\phi)
= 2 (\sin\theta \cos\phi)(\sin\theta \sin\phi)
= 2xy
$$

{% endraw %}

最後可得：

{% raw %}

$$
Y_{2,-2} = \frac{1}{2}\sqrt{\frac{15}{\pi}}\, xy
$$

{% endraw %}

#### 3. 三個球面積分的推導

正文在「範例」一節用到了三個球面積分：$\int_{S^2} 1 d\omega = 4\pi$、$\int_{S^2} z d\omega = 0$、$\int_{S^2} z^2 d\omega = 4\pi / 3$，當時只直接引用了結果。這裡把「在球面上積分」這件事攤開來 - 也就是把 $\int_{S^2} \cdots d\omega$ 換成真的可以計算的形式，用球座標把這三個值一步一步積出來

**球座標與面積元素**

球面上的方向可以用兩個角度描述：$\theta$ 是與 $z$ 軸的夾角（$0$ 到 $\pi$，緯度方向）、$\phi$ 是繞 $z$ 軸的方位角（$0$ 到 $2\pi$，經度方向），對應的直角座標為：

{% raw %}

$$
x = \sin\theta \cos\phi
\qquad
y = \sin\theta \sin\phi
\qquad
z = \cos\theta
$$

{% endraw %}

而球面上一小塊面積 $d\omega$ 的兩個邊長分別是：沿緯度方向走 $d\theta$ 角度，弧長就是 $d\theta$（半徑為 $1$）；沿經度方向走 $d\phi$ 角度，但這時繞的是**緯圈**而不是大圓，緯圈的半徑是 $\sin\theta$，所以弧長是 $\sin\theta d\phi$。兩者相乘：

{% raw %}

$$
d\omega = \sin\theta\, d\theta\, d\phi
$$

{% endraw %}

我們藉由下圖來理解這件事：

左圖：紅色區塊代表圍出來的那一小塊面積，而那一小塊面積的兩個邊用紫色跟綠色表示，紫色那條沿著緯度方向走 $d\theta$，經線是半徑 $1$ 的大圓，弧長就等於 $1 \times d\theta = d\theta$；綠色那條沿著經度方向走 $d\phi$，關鍵就在這裡，此時走的緯圈不是大圓，而是半徑只有 $\sin\theta$ 的小圓，我們可以藉由右圖看到這個小圓

右圖：把視角轉到 $z$ 軸正上方往下看，這時緯圈就攤成一個半徑 $\sin\theta$ 的平面圓，因此綠色那條邊的長度就是 $\sin\theta d\phi$。圖中同時把赤道（半徑 $1$）上同樣 $d\phi$ 掃出的弧長畫出來對照，兩者的角度一樣，但半徑差了 $\sin\theta$ 倍

<img src="./sh_area_element.png" style="max-width: 100%;">

[圖片程式碼](https://github.com/bcjohnblue/python-plot/blob/main/2026/08/spherical-harmonics/sh_area_element.py)

**先把 $\phi$ 積掉，再做變數代換**

要算的三個積分（$1$、$z$、$z^2$）有個共同點：被積分的函數只跟 $z = \cos\theta$ 有關、完全不含 $\phi$（也就是同一個緯度圈上的值都一樣）。這讓化簡可以一路做到底 - 下面把被積分的函數統一寫成 $g(z)$（代 $1$、$z$、$z^2$ 都適用），一步一步推

**步驟 1：把球面積分展開成兩個角度的二重積分**

把剛剛得到的 $d\omega = \sin\theta d\theta d\phi$ 代進去，同時把 $z$ 寫成 $\cos\theta$：

{% raw %}

$$
\int_{S^2} g(z)\, d\omega
= \int_{0}^{2\pi} \!\! \int_{0}^{\pi} g(\cos\theta)\, \sin\theta\, d\theta\, d\phi
$$

{% endraw %}

**步驟 2：把 $\phi$ 積掉，換成 $2\pi$**

被積分的東西完全不含 $\phi$，所以內層對 $\theta$ 積完之後是一個與 $\phi$ 無關的常數，外層的 $\phi$ 積分就只是乘上一整圈的長度 $2\pi$：

{% raw %}

$$
= \left( \int_{0}^{2\pi} d\phi \right) \int_{0}^{\pi} g(\cos\theta)\, \sin\theta\, d\theta
= 2\pi \int_{0}^{\pi} g(\cos\theta)\, \sin\theta\, d\theta
$$

{% endraw %}

到這裡已經從「球面上的積分」降成「一個角度的一維積分」了

**步驟 3：變數代換 $u = \cos\theta$，把 $\sin\theta d\theta$ 整包換掉**

{% raw %}

$$
u = \cos\theta
\qquad \Longrightarrow \qquad
du = -\sin\theta\, d\theta
\qquad \Longrightarrow \qquad
\sin\theta\, d\theta = -du
$$

{% endraw %}

代回去之後，$g(\cos\theta)$ 變成 $g(u)$、$\sin\theta d\theta$ 變成 $-du$：

{% raw %}

$$
= 2\pi \int_{\theta = 0}^{\theta = \pi} g(u)\, (-du)
$$

{% endraw %}

**步驟 4：把上下限也換成 $u$**

積分變數換了，上下限當然要跟著換：$\theta = 0$ 對應 $u = \cos 0 = 1$，$\theta = \pi$ 對應 $u = \cos \pi = -1$：

{% raw %}

$$
= 2\pi \int_{1}^{-1} g(u)\, (-du)
= 2\pi \int_{-1}^{1} g(u)\, du
$$

{% endraw %}

最後一步是把上下限對調（$1 \rightarrow -1$ 換成 $-1 \rightarrow 1$），對調要乘上一個 $-1$，剛好把 $-du$ 的負號吃掉

**整理成一條公式**

{% raw %}

$$
\int_{S^2} g(z)\, d\omega = 2\pi \int_{-1}^{1} g(u)\, du
$$

{% endraw %}

這條式子把球面積分變成了一條普通的一維定積分，而本文中用到的三個結果都只是它的特例：

{% raw %}

$$
\int_{S^2} 1\, d\omega = 2\pi \int_{-1}^{1} du = 2\pi \times 2 = 4\pi
$$

$$
\int_{S^2} z\, d\omega = 2\pi \int_{-1}^{1} u\, du = 2\pi \left[ \frac{u^2}{2} \right]_{-1}^{1} = 0
$$

$$
\int_{S^2} z^2\, d\omega = 2\pi \int_{-1}^{1} u^2\, du = 2\pi \left[ \frac{u^3}{3} \right]_{-1}^{1} = 2\pi \times \frac{2}{3} = \frac{4\pi}{3}
$$

{% endraw %}

## 參考資料

[Spherical harmonics - Wikipedia](https://en.wikipedia.org/wiki/Spherical_harmonics)
[Table of spherical harmonics - Wikipedia](https://en.wikipedia.org/wiki/Table_of_spherical_harmonics)
[Rodrigues' formula - Wikipedia](https://en.wikipedia.org/wiki/Rodrigues%27_formula)
[Legendre Polynomials (Rodrigues' formula) - Mathematics LibreTexts](https://math.libretexts.org/Bookshelves/Differential_Equations/A_First_Course_in_Differential_Equations_for_Scientists_and_Engineers_%28Herman%29/04%3A_Series_Solutions/4.05:_Legendre_Polynomials)
