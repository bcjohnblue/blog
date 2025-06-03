---
title: MSE、PSNR、SSIM 圖片差異性比較
date: 2025-06-04 00:53:57
updated: 2025-06-04 00:53:57
categories: Math
tags:
mathjax: true
---

## 前言

在進行影像壓縮、AI 影像重建、或品質評估時，常會使用 MSE、PSNR、SSIM 三種指標來衡量影像品質，這篇文章學習如何用數學計算出這三種指標

## 1. MSE（Mean Squared Error）

**均方誤差**，是最基本的誤差評估指標

### 定義：

計算兩張圖的對應像素差的平方平均：

$$
\text{MSE} = \frac{1}{mn} \sum_{i=1}^{m} \sum_{j=1}^{n} (I_{i,j} - K_{i,j})^2
$$

- $I$：原始影像像素值
- $K$：比較影像像素值
- $m, n$：影像的寬高

<!-- more -->

### 特點：

- 數值越小越好
- 簡單易計算
- 缺點：不考慮人眼視覺感受，對肉眼無感的變化也會被當成誤差

---

## 2. PSNR（Peak Signal-to-Noise Ratio）

**峰值信噪比**，是 MSE 的對數表示，更常被工程界使用。

### 定義：

$$PSNR = 10 \cdot \log_{10} \left( \frac{MAX_I^2}{MSE} \right)$$

- $ \text{MAX}\_I $：影像像素最大值（8-bit 圖像通常是 255）

### 特點：

- 數值越大越好（失真越少）
- 有單位（dB）

### 說明：
- PSNR接近 50dB ，代表壓縮後的圖像僅有些許非常小的誤差。
- PSNR大於 30dB ，人眼很難察覺壓縮後和原始影像的差異。
- PSNR介於 20dB 到 30dB 之間，人眼就可以察覺出圖像的差異。
- PSNR介於 10dB 到 20dB 之間，人眼還是可以用肉眼看出這個圖像原始的結構，且直觀上會判斷兩張圖像不存在很大的差異。
- PSNR低於 10dB，人類很難用肉眼去判斷兩個圖像是否為相同，一個圖像是否為另一個圖像的壓縮結果。

<img src="./psnr-demo.png" />

圖片取自 [wiki 峰值信噪比](https://zh.wikipedia.org/zh-tw/%E5%B3%B0%E5%80%BC%E4%BF%A1%E5%99%AA%E6%AF%94)

---

## 3. SSIM（Structural Similarity Index）

**結構相似度指標**，模擬人眼對影像品質的感受。

### 定義：

SSIM 從**亮度、對比、結構**三方面比較圖像：

$$\text{SSIM}(x,y) = \frac{(2\mu_x\mu_y + C_1)(2\sigma_{xy} + C_2)}{(\mu_x^2 + \mu_y^2 + C_1)(\sigma_x^2 + \sigma_y^2 + C_2)}$$

- $ \mu_x、\mu_y $：區域亮度平均值
- $ \sigma_x、\sigma_y $：變異數
- $ \sigma\_{xy} $：共變異數
- $C_1 = (K_1 L)^2$, $C_2 = (K_2 L)^2$；常見取 $K_1 = 0.01, K_2 = 0.03, L = 255$
- 通常以區塊滑動方式計算局部 SSIM，取平均為最終分數

### 特點：

- 數值介於 `-1 ~ 1`，越接近 1 代表圖片越相似
- 更貼近人類視覺的感知品質
- 可分辨模糊、對比變化、局部失真

### 說明：

下圖展示了 SSIM 相較於 MSE 可以更好的代表圖片相似度，以下六張圖 MSE 的數值幾乎一樣，但很明顯右下角的圖片模糊許多並不應該視為與左上角的原圖是相似的，此時使用 SSIM 的計算結果 = 0.662 可以比 MSE 更好的代表圖片相似度

<img src="https://pica.zhimg.com/v2-2f99a5d878a59ba6d1a0cc7528a97516_1440w.jpg" />

圖片取自 [SSIM (Structure Similarity Index Measure) 结构衡量指标+代码](https://zhuanlan.zhihu.com/p/399215180)

--- 

再以維基百科上的猩猩圖片為範例，Image E 與其他圖片毫無相關，但 MSE 值卻跟 Image B 幾乎一樣，而 SSIM 在 Image E 的值為 0.047 可以更好的表示 Image E 與原始圖 Image A 是不相似的

> 對人類的視覺系統來說，Image A、B、C、D 是一群較為相似的圖片，Image E 則是與其他圖片毫無相關。

> 這裡是使用Image A跟其他圖片進行相似度的比較，一共使用三種方法：均方誤差（MSE）、正規化方均根差（NRMSE）、結構相似度指標（SSIM）。

<img src="https://upload.wikimedia.org/wikipedia/commons/thumb/4/49/SSIM_experiment_picture.png/960px-SSIM_experiment_picture.png" />
<img src="https://upload.wikimedia.org/wikipedia/commons/d/de/SSIM_experiment_data.png" />

圖片取自 [wiki 結構相似性](https://zh.wikipedia.org/zh-tw/%E7%B5%90%E6%A7%8B%E7%9B%B8%E4%BC%BC%E6%80%A7)

---

## 範例

以兩張簡單的 4x4 圖像以三種方式計算差異性如下：

```scss
img1 (原圖):
100  100  100  100
100  100  100  100
100  100  100  100
100  100  100  100

img2 (扭曲圖):
100  100   90  100
100  100  100  100
100   95  100  100
100  100  100  105
```

### 1. 計算 MSE（Mean Squared Error）

$$
\text{MSE} = \frac{1}{mn} \sum_{i=1}^{m} \sum_{j=1}^{n} (I_{i,j} - K_{i,j})^2
$$

- 圖像大小：4×4 = 16 個像素
- 有誤差的像素共 4 個：

(0,2)：100 → 90 → 差值 = 10 → $10^2 = 100$

(2,1)：100 → 95 → 差值 = 5 → $5^2 = 25$

(3,3)：100 → 105 → 差值 = -5 → $(-5)^2 = 25$

$ \displaystyle 總誤差平方 = 100+25+25 = 150 $

$ \displaystyle MSE = \frac{150}{16} = \mathbf{9.375} $

### 2. 計算 PSNR（Peak Signal-to-Noise Ratio）

$$PSNR = 10 \cdot \log_{10} \left( \frac{MAX_I^2}{MSE} \right)$$

- 8-bit 圖像最大值 $ \text{MAX}\_I = 255 $
- $ \text{MSE} = 9.375 $

$$
PSNR = 10 \cdot \log_{10} \left( \frac{255^2}{9.375} \right) = 10 \cdot \log_{10}(6945.6) \approx 10 \cdot 3.842 = \mathbf{38.42 \text{ dB}}
$$

### 3. 計算 SSIM

$$\text{SSIM}(x,y) = \frac{(2\mu_x\mu_y + C_1)(2\sigma_{xy} + C_2)}{(\mu_x^2 + \mu_y^2 + C_1)(\sigma_x^2 + \sigma_y^2 + C_2)}$$

##### 步驟 1：計算平均值 μ

`img1` 像素值都是 100，平均 $ \mu_x = 100 $

`img2` 所有像素總和為：

$$
sum=100×13+90+95+105=1300+90+95+105=1590
$$

$$
\mu_y = \frac{1590}{16} = 99.375
$$

##### 步驟 2：計算變異數 σ²

`img1` 每個像素值都是 100，變異數為：

$$\sigma_x^2 = \frac{1}{16} \sum (x_i - \mu_x)^2 = \frac{1}{16} \sum (x_i - 100)^2 = 0$$

`img2` 的平均值 $ \mu_y = 99.375 $，變異數計算簡略如下：

| 值  | 與平均差值 | 平方      |
| --- | ---------- | --------- |
| 100 | 0.625      | 0.390625  |
| 100 | 0.625      | 0.390625  |
| 90  | -9.375     | 87.890625 |
| 100 | 0.625      | 0.390625  |
| 100 | 0.625      | 0.390625  |
| ... | ...        | ...       |
| 105 | 5.625      | 31.640625 |

總平方和為：約 132.25

$$\sigma_y^2 = \frac{132.25}{16} \approx 8.2656 $$

##### 步驟 3：計算共變異數 $ \sigma\_{xy} $

$$\sigma_{xy} = \frac{1}{N} \sum (x_i - \mu_x)(y_i - \mu_y)$$

`img1` 所有像素都等於 100，且 $\mu_x = 100$，所以：

$$(x_i - \mu_x) = 0 \Rightarrow \sigma_{xy} = 0$$

在這個範例中，由於 `img1` 圖像是常數圖，最終求得的共變異數為 0

##### 步驟 4：套入 SSIM 公式

求出參數如下：

$\mu_x = 100$

$\mu_y = 99.375$

$\sigma_x^2 = 0$

$\sigma_y^2 = 8.2656$

$\sigma_{xy} = 0$

$L = 255$

$C_1 = (0.01 \cdot 255)^2 ≈ 6.5025$

$C_2 = (0.03 \cdot 255)^2 ≈ 58.5225$

{% raw %}

$$
\begin{align*}
\text{SSIM}(x,y) &= \frac{(2\mu_x\mu_y + C_1)(2\sigma_{xy} + C_2)}{(\mu_x^2 + \mu_y^2 + C_1)(\sigma_x^2 + \sigma_y^2 + C_2)} \\
&= \frac{(2 \times 100 \times 99.375 + 6.5025)(2 \times 0 + 58.5225)}{(100^2 + 99.375^2 + 6.5025)(0^2 + 8.2656^2 + 58.5225)} \approx \mathbf{0.8759}
\end{align*}
$$

{% endraw %}

### 範例統整

| 指標       | 結果         | 單位      | 意義與說明                                    |
| -------- | ---------- | ------- | ---------------------------------------- |
| **MSE**  | `9.375`    | 無單位     | 平均每個像素的誤差平方值。數值越小代表越接近。                  |
| **PSNR** | `38.42 dB` | 分貝 (dB) | 對數尺度表示圖像品質，值越大代表失真越小。常見品質界線 > 30 dB 即不錯。 |
| **SSIM** | `0.8759`   | 0～1     | 結構相似度，綜合亮度、對比、結構等特徵，1 表示完全一致。            |


## 參考資料

[图像质量评估指标：MSE、PSNR 和 SSIM](https://zhuanlan.zhihu.com/p/582577216)
[SSIM (Structure Similarity Index Measure) 结构衡量指标+代码](https://zhuanlan.zhihu.com/p/399215180)
[一文看懂图像质量评估](https://zhuanlan.zhihu.com/p/705677295)
[峰值信噪比](https://zh.wikipedia.org/zh-tw/%E5%B3%B0%E5%80%BC%E4%BF%A1%E5%99%AA%E6%AF%94)
[結構相似性](https://zh.wikipedia.org/zh-tw/%E7%B5%90%E6%A7%8B%E7%9B%B8%E4%BC%BC%E6%80%A7)

{% raw %}

<style>
table {
  th, td {
   padding: 8px 12px;
  } 
}
</style>

{% endraw %}
