---
title: 高斯分布 (1) - 從共變異數矩陣出發
date: 2026-03-18 21:33:35
updated: 2026-03-18 21:33:35
categories: Math
mathjax: true
---

###### 此為 **高斯分布** 系列文章 - 第 1 篇：

1. <a href="/2026/03/18/高斯分布-1-從共變異數矩陣出發/" target="_blank">高斯分布 (1) - 從共變異數矩陣出發</a>
2. <a href="/2026/03/18/高斯分布-2-一維到多維的分布形式/" target="_blank">高斯分布 (2) - 一維到多維的分布形式</a>
3. <a href="/2026/03/18/高斯分布-3-仿射變換後的性質變化/" target="_blank">高斯分布 (3) - 仿射變換後的性質變化</a>

## 序

這一系列文章的目的是研究高斯分布相關的數學性質，在 [高斯分布 (2) - 一維到多維的分布形式](/2026/03/18/高斯分布-2-一維到多維的分布形式/) 文章中會學習到高斯分布的多維形式，基本上就是由**平均值**及**共變異數矩陣**所決定的，因此在正式介紹高斯分布數學式之前，我們需要先了解什麼是**共變異數矩陣**

## 前言

我們在看資料時，常常不只想知道「一個東西變化大不大」，還會好奇「兩個東西是不是一起變」。例如：天氣變熱時，飲料銷量會不會跟著上升? 讀書時間增加，成績會不會也變好? 這些「一起變」的現象，就是 **共變異數 (Covariance)** 想描述的內容

最基本的概念是 **變異數 (Variance)**，它告訴我們某個數據本身的波動有多大；但只看單一變數就像只看一個角色，很難了解整個故事。當我們把兩個變數放在一起，就可以用**共變異數**判斷它們是同方向動（一起變大或變小）、還是相互拉扯（一个變大、一个變小），或是根本沒什麼關聯

而當資料裡的變數不只兩個，而是三個、十個、甚至上百個時，我們就需要把所有變數之間的「一起變動關係」整理成一張表，那就是 **共變異數矩陣 (Covariance Matrix)**。這張矩陣像是一個地圖，描繪出每個變數之間的連動方式，幫助我們看出資料的真正結構

<!-- more -->

## 變異數 (Variance)

變異數的公式可用來衡量資料的分散程度，其定義是每個觀測值與平均值之差的平方的平均。對於一組數據 $X = \{x_1, x_2, ..., x_n\}$，其變異數定義為：

### 定義

$$\text{Var}(X) = \frac{1}{n} \sum_{i=1}^{n} (x_i - \mu)^2$$

其中：

- $x_i$ 為每個數據點
- $\mu$ 是數據的平均值
- $n$ 是數據點的個數

### 期望值表示

對於隨機變數 $X$，變異數也可以用 [期望值](https://zh.wikipedia.org/zh-tw/%E6%9C%9F%E6%9C%9B%E5%80%BC) 表示：

{% raw %}

$$\text{Var}(X) = E(X^2) - (E(X))^2$$

{% endraw %}

**證明**

變異數的原始定義是衡量隨機變數 $X$ 的值與其期望值 $\mu = E(X)$ 之差的平方的平均：

{% raw %}

$$\text{Var}(X) = E((X - \mu)^2) = E((X - E(X))^2)$$

{% endraw %}

先展開平方項

{% raw %}

$$E((X - \mu)^2) = E(X^2 - 2X\mu + \mu^2)$$

{% endraw %}

根據期望值的線性性質，將期望值運算子應用於每一項

{% raw %}

$$E(X^2 - 2X\mu + \mu^2) = E(X^2) - E(2X\mu) + E(\mu^2)$$

{% endraw %}

由於 $\mu$ (即 $E(X)$) 是一個常數，可以將其移出期望值運算子

{% raw %}

$$E(X^2) - 2\mu E(X) + \mu^2$$

{% endraw %}

將 $\mu$ 替換回 $E(X)$

{% raw %}

$$E(X^2) - 2E(X)E(X) + (E(X))^2$$

{% endraw %}

簡化後得到最終公式

{% raw %}

$$\text{Var}(X) = E(X^2) - (E(X))^2$$

{% endraw %}

### 幾何意義

- 變異數衡量數據點相對於平均值的「分散程度」
- 較大的變異數表示數據點較為分散
- 較小的變異數表示數據點較為集中

### 範例

假設有三個人的身高分別為 160、170、180 公分

**步驟 1：計算平均值**
$$\mu = \frac{160 + 170 + 180}{3} = \frac{510}{3} = 170$$

**步驟 2：計算變異數**

{% raw %}

$$
\begin{align*}
\text{Var}(X) &= \frac{(160-170)^2 + (170-170)^2 + (180-170)^2}{3} \\
&= \frac{(-10)^2 + 0^2 + 10^2}{3} = \frac{100 + 0 + 100}{3} \\
&= \frac{200}{3} \approx 66.67
\end{align*}
$$

{% endraw %}

因此，變異數約為 66.67，代表這三個身高數據的分散程度

## 共變異數 (Covariance)

[共變異數](https://zh.wikipedia.org/zh-tw/%E5%8D%8F%E6%96%B9%E5%B7%AE) 在大陸又被稱爲 [協方差](https://zh.wikipedia.org/zh-cn/%E5%8D%8F%E6%96%B9%E5%B7%AE)，用以衡量兩個變數同時變動的方向與程度。它的定義是兩變數偏離各自平均數的乘積的平均

### 定義

$$\text{Cov}(X,Y) = \frac{1}{n - 1} \sum_{i=1}^{n} (x_i - \bar{x})(y_i - \bar{y})$$

其中：

- $x_i, y_i$ 分別為兩變數的觀測值
- $\bar{x}, \bar{y}$ 分別是兩變數的平均值
- $n$ 是資料對數

#### 期望值表示

對於隨機變數 $X$ 和 $Y$，共變異數也可以用期望值表示：

{% raw %}

$$\text{Cov}(X,Y) = E(XY) - E(X)E(Y)$$

{% endraw %}

**證明**

共變異數的原始定義是衡量兩個隨機變數 $X$ 和 $Y$ 與各自期望值 $\mu_x = E(X)$ 和 $\mu_y = E(Y)$ 偏差乘積的平均：

{% raw %}

$$\text{Cov}(X,Y) = E((X - \mu_x)(Y - \mu_y)) = E((X - E(X))(Y - E(Y)))$$

{% endraw %}

先展開乘積

{% raw %}

$$E((X - \mu_x)(Y - \mu_y)) = E(XY - X\mu_y - Y\mu_x + \mu_x\mu_y)$$

{% endraw %}

根據期望值的線性性質，將期望值運算子應用於每一項

{% raw %}

$$E(XY - X\mu_y - Y\mu_x + \mu_x\mu_y) = E(XY) - E(X\mu_y) - E(Y\mu_x) + E(\mu_x\mu_y)$$

{% endraw %}

由於 $\mu_x$ 和 $\mu_y$ (即 $E(X)$ 和 $E(Y)$) 是常數，可以將其移出期望值運算子

{% raw %}

$$E(XY) - \mu_y E(X) - \mu_x E(Y) + \mu_x\mu_y$$

{% endraw %}

將 $\mu_x$ 和 $\mu_y$ 替換回 $E(X)$ 和 $E(Y)$

{% raw %}

$$E(XY) - E(Y)E(X) - E(X)E(Y) + E(X)E(Y)$$

{% endraw %}

簡化後得到最終公式

{% raw %}

$$\text{Cov}(X,Y) = E(XY) - E(X)E(Y)$$

{% endraw %}

### 幾何意義

- **正值**：兩變數同向變動（正相關）
- **負值**：兩變數反向變動（負相關）
- **接近零**：兩變數無明顯線性關係

### 範例

假設有一組學生的邏輯能力分數（X）和他們的課程成績（Y）如下：

| 學生 | X (邏輯能力) | Y (課程成績) |
| ---- | ------------ | ------------ |
| 1    | 70           | 72           |
| 2    | 75           | 78           |
| 3    | 80           | 75           |
| 4    | 85           | 83           |
| 5    | 90           | 89           |

**步驟 1：計算平均值**
$$\bar{x} = \frac{70 + 75 + 80 + 85 + 90}{5} = \frac{400}{5} = 80$$
$$\bar{y} = \frac{72 + 78 + 75 + 83 + 89}{5} = \frac{397}{5} = 79.4$$

**步驟 2：計算共變異數**

{% raw %}

$$
\begin{align*}
\text{Cov}(X,Y) &= \frac{(70-80)(72-79.4) + (75-80)(78-79.4) + (80-80)(75-79.4) + (85-80)(83-79.4) + (90-80)(89-79.4)}{4} \\
&= \frac{(-10)(-7.4) + (-5)(-1.4) + 0 \times (-4.4) + 5 \times 3.6 + 10 \times 9.6}{4} \\
&= \frac{74 + 7 + 0 + 18 + 96}{4} \\
&= \frac{195}{4} = 48.75
\end{align*}
$$

{% endraw %}

結果共變異數為正（48.75），表示兩變數同向變動關係較強，即邏輯能力與成績呈正相關

## 共變異數矩陣 (Covariance Matrix)

共變異數可以描述兩個變數間的相關性，但當變數超過兩個時，單純的共變異數已不足以描述其分布特性，此時我們可以使用一個矩陣同時描述各維度變數之間的關係，而這就是 **共變異數矩陣 (Covariance Matrix)**

### 定義

假設有三個隨機變數：$X_1, X_2, X_3$，它們的共變異數矩陣 $\Sigma$ 是：

{% raw %}

$$
\Sigma =
\begin{bmatrix}
\text{Var}(X_1) & \text{Cov}(X_1, X_2) & \text{Cov}(X_1, X_3) \\
\text{Cov}(X_2, X_1) & \text{Var}(X_2) & \text{Cov}(X_2, X_3) \\
\text{Cov}(X_3, X_1) & \text{Cov}(X_3, X_2) & \text{Var}(X_3)
\end{bmatrix}
$$

{% endraw %}

其中：

- **對角線**：各變數的變異數
- **非對角線**：各變數之間的共變異數

### 內積定義

另一個常用來表示共變異數矩陣的方式是 [矩陣內積](/2025/10/18/向量的內積與外積/#定義二：矩陣乘法形式)，這種表示形式的優點是極為簡潔：

{% raw %}

$$\Sigma = \frac{1}{n-1}(\tilde{X}^T \tilde{X})$$

{% endraw %}

其中 $\tilde{X}$ 是**中心化後的資料矩陣**（每一列減去其平均值），在資料矩陣 $\tilde{X}$ 中：

- **列 (row)**：代表每一筆資料樣本
- **行 (column)**：代表每一個特徵變數

當我們計算 $\tilde{X}^T \tilde{X}$ 時：

- **對角線元素** $\tilde{x}_i^T \tilde{x}_i$：特徵向量與自己的內積 → 給出**變異數 (variance)**
- **非對角線元素** $\tilde{x}_i^T \tilde{x}_j$：不同特徵向量的內積 → 給出**共變異數 (covariance)**

#### 證明

設中心化後的資料矩陣為：

{% raw %}

$$
\tilde{X} = \begin{bmatrix}
\tilde{x}_{11} & \tilde{x}_{12} & \cdots & \tilde{x}_{1p} \\
\tilde{x}_{21} & \tilde{x}_{22} & \cdots & \tilde{x}_{2p} \\
\vdots & \vdots & \ddots & \vdots \\
\tilde{x}_{n1} & \tilde{x}_{n2} & \cdots & \tilde{x}_{np}
\end{bmatrix}_{n \times p}
$$

{% endraw %}

計算 $\tilde{X}^T \tilde{X}$：

{% raw %}

$$
\tilde{X}^T \tilde{X} = \begin{bmatrix}
\sum_{k=1}^n \tilde{x}_{k1}^2 & \sum_{k=1}^n \tilde{x}_{k1}\tilde{x}_{k2} & \cdots \\
\sum_{k=1}^n \tilde{x}_{k2}\tilde{x}_{k1} & \sum_{k=1}^n \tilde{x}_{k2}^2 & \cdots \\
\vdots & \vdots & \ddots
\end{bmatrix}
$$

{% endraw %}

注意到：

- 對角線元素 $\sum_{k=1}^n \tilde{x}_{ki}^2$ 除以 $(n-1)$ 就是 $\text{Var}(X_i)$
- 非對角線元素 {% raw %} $\sum_{k=1}^n \tilde{x}_{ki}\tilde{x}_{kj}$ 除以 $(n-1)$ 就是 $\text{Cov}(X_i, X_j)$ {% endraw %}

因此：

{% raw %}

$$\Sigma = \frac{1}{n-1}(\tilde{X}^T \tilde{X})$$

{% endraw %}

這個形式自然地給出了共變異數矩陣的所有元素，是計算共變異數矩陣最簡潔的方式

#### 期望值表示

對於隨機向量 $\mathbf{X} = (X_1, X_2, ..., X_p)^T$，共變異數矩陣也可以用期望值表示：

{% raw %}

$$\Sigma = E[(\mathbf{X} - E[\mathbf{X}])(\mathbf{X} - E[\mathbf{X}])^T]$$

{% endraw %}

其中 $E[\mathbf{X}]$ 是隨機向量的期望值向量。這個形式將共變異數矩陣視為「隨機向量與其期望值偏差的外積」的期望值

**證明**

共變異數矩陣 $\Sigma$ 的第 $(i,j)$ 個元素是 $\text{Cov}(X_i, X_j)$。根據共變異數的期望值表示：

{% raw %}

$$\text{Cov}(X_i, X_j) = E((X_i - E(X_i))(X_j - E(X_j)))$$

{% endraw %}

考慮矩陣 $(\mathbf{X} - E[\mathbf{X}])(\mathbf{X} - E[\mathbf{X}])^T$ 的第 $(i,j)$ 個元素：

{% raw %}

$$[(\mathbf{X} - E[\mathbf{X}])(\mathbf{X} - E[\mathbf{X}])^T]_{ij} = (X_i - E(X_i))(X_j - E(X_j))$$

{% endraw %}

對該矩陣取期望值，得到第 $(i,j)$ 個元素：

{% raw %}

$$E[(\mathbf{X} - E[\mathbf{X}])(\mathbf{X} - E[\mathbf{X}])^T]_{ij} = E((X_i - E(X_i))(X_j - E(X_j))) = \text{Cov}(X_i, X_j)$$

{% endraw %}

因此，矩陣 $E[(\mathbf{X} - E[\mathbf{X}])(\mathbf{X} - E[\mathbf{X}])^T]$ 的第 $(i,j)$ 個元素等於 $\Sigma$ 的第 $(i,j)$ 個元素，故：

{% raw %}

$$\Sigma = E[(\mathbf{X} - E[\mathbf{X}])(\mathbf{X} - E[\mathbf{X}])^T]$$

{% endraw %}

證畢

### 範例

假設我們有以下四筆樣本資料：

| 樣本 | $X_1$ | $X_2$ | $X_3$ |
| ---- | ----- | ----- | ----- |
| 1    | 2     | 3     | 1     |
| 2    | 4     | 7     | 5     |
| 3    | 6     | 5     | 2     |
| 4    | 8     | 9     | 6     |

#### 使用定義計算

**步驟 1：計算樣本平均**
$$\bar{X}_1 = \frac{2 + 4 + 6 + 8}{4} = 5$$
$$\bar{X}_2 = \frac{3 + 7 + 5 + 9}{4} = 6$$
$$\bar{X}_3 = \frac{1 + 5 + 2 + 6}{4} = 3.5$$

**步驟 2：計算偏差**
以樣本 1 為例：
$$(X_1 - \bar{X}_1, X_2 - \bar{X}_2, X_3 - \bar{X}_3) = (2-5, 3-6, 1-3.5) = (-3, -3, -2.5)$$

**步驟 3：計算變異數**
使用公式：

{% raw %}

$$
\text{Var}(X_i) = \frac{1}{n-1} \sum_{k=1}^{n} (X_{i,k} - \bar{X}_i)^2
$$

{% endraw %}

以 $\text{Var}(X_1)$ 為例：

{% raw %}

$$
\begin{align*}
\text{Var}(X_1) &= \frac{1}{3}[(2-5)^2 + (4-5)^2 + (6-5)^2 + (8-5)^2] \\
&= \frac{1}{3}[(-3)^2 + (-1)^2 + (1)^2 + (3)^2] \\
&= \frac{1}{3}[9 + 1 + 1 + 9] \\
&= \frac{1}{3} \times 20 = \frac{20}{3} \approx 6.67
\end{align*}
$$

{% endraw %}

**步驟 4：計算共變異數**
使用公式：

{% raw %}

$$
\text{Cov}(X_i, X_j) = \frac{1}{n-1} \sum_{k=1}^{n} (X_{i,k} - \bar{X}_i)(X_{j,k} - \bar{X}_j)
$$

{% endraw %}

以 $\text{Cov}(X_1, X_2)$ 為例：

{% raw %}

$$
\begin{align*}
\text{Cov}(X_1, X_2) &= \frac{1}{3}[(2-5)(3-6) + (4-5)(7-6) + (6-5)(5-6) + (8-5)(9-6)] \\
&= \frac{1}{3}[(-3)(-3) + (-1)(1) + (1)(-1) + (3)(3)] \\
&= \frac{1}{3}[9 + (-1) + (-1) + 9] \\
&= \frac{1}{3} \times 16 = \frac{16}{3} \approx 5.33
\end{align*}
$$

{% endraw %}

**步驟 5：完成矩陣計算**
經過類似計算，最終的共變異數矩陣為：

{% raw %}

$$
\Sigma \approx \begin{bmatrix}
6.67 & 5.33 & 5.83 \\
5.33 & 6.67 & 5.50 \\
5.83 & 5.50 & 5.67
\end{bmatrix}
$$

{% endraw %}

幾何意義：

- **$\text{Var}(X_i)$**：變數自身的分散程度 (對角線)
- **$\text{Cov}(X_i, X_j)$**：變數間的線性相關 (非對角線)

#### 使用內積定義計算

我們也可以使用內積定義 $\Sigma = \frac{1}{n-1}(\tilde{X}^T \tilde{X})$ 來計算共變異數矩陣

**步驟 1：構建中心化資料矩陣 $\tilde{X}$**

從步驟 2 的偏差計算，我們得到中心化後的資料矩陣：

{% raw %}

$$
\tilde{X} = \begin{bmatrix}
-3 & -3 & -2.5 \\
-1 & 1 & 1.5 \\
1 & -1 & -1.5 \\
3 & 3 & 2.5
\end{bmatrix}_{4 \times 3}
$$

{% endraw %}

**步驟 2：計算 $\tilde{X}^T \tilde{X}$**

{% raw %}

$$
\begin{align*}
\tilde{X}^T \tilde{X} &= \begin{bmatrix}
-3 & -1 & 1 & 3 \\
-3 & 1 & -1 & 3 \\
-2.5 & 1.5 & -1.5 & 2.5
\end{bmatrix} \begin{bmatrix}
-3 & -3 & -2.5 \\
-1 & 1 & 1.5 \\
1 & -1 & -1.5 \\
3 & 3 & 2.5
\end{bmatrix} \\
&= \begin{bmatrix}
20 & 16 & 17.5 \\
16 & 20 & 16.5 \\
17.5 & 16.5 & 17
\end{bmatrix}
\end{align*}
$$

{% endraw %}

**步驟 3：除以 $(n-1)$ 得到共變異數矩陣**

由於 $n = 4$，所以 $n-1 = 3$：

{% raw %}

$$
\begin{align*}
\Sigma &= \frac{1}{3} \begin{bmatrix}
20 & 16 & 17.5 \\
16 & 20 & 16.5 \\
17.5 & 16.5 & 17
\end{bmatrix} \\
&= \begin{bmatrix}
\frac{20}{3} & \frac{16}{3} & \frac{17.5}{3} \\
\frac{16}{3} & \frac{20}{3} & \frac{16.5}{3} \\
\frac{17.5}{3} & \frac{16.5}{3} & \frac{17}{3}
\end{bmatrix} \\
&\approx \begin{bmatrix}
6.67 & 5.33 & 5.83 \\
5.33 & 6.67 & 5.50 \\
5.83 & 5.50 & 5.67
\end{bmatrix}
\end{align*}
$$

{% endraw %}

結果與前面使用傳統公式計算的結果完全一致！

### 性質

#### 對稱性

共變異數矩陣必須是對稱的：$\Sigma^T = \Sigma$，這源於共變異數的定義本身就具有對稱性：
$$\text{Cov}(X_i, X_j) = \text{Cov}(X_j, X_i)$$

#### 半正定性 (SPD)

對任意向量 $v$，都有 $v^T \Sigma v \geq 0$。因此所有特徵值皆為非負，代表變異度不會為負

**證明**  
使用內積定義 $\Sigma = \frac{1}{n-1}(\tilde{X}^T \tilde{X})$，對任意向量 $v$：

{% raw %}

$$
\begin{aligned}
v^T \Sigma v
&= v^T \cdot \frac{1}{n-1}(\tilde{X}^T \tilde{X}) \cdot v \\
&= \frac{1}{n-1} v^T \tilde{X}^T \tilde{X} v \\
&= \frac{1}{n-1} (\tilde{X} v)^T (\tilde{X} v) \\
&= \frac{1}{n-1} \|\tilde{X} v\|^2 \geq 0
\end{aligned}
$$

{% endraw %}

因為 {% raw %} $\|\tilde{X} v\|^2$ 是向量 $\tilde{X} v$ {% endraw %} 的範數平方，必然非負，故 $v^T \Sigma v \geq 0$，因此 $\Sigma$ 為 <a href="/2025/11/03/正定矩陣與半正定矩陣-1-定義與性質/#半正定矩陣" target="_blank" style="line-height: 28px;">半正定矩陣</a>

#### 特徵分解

由於半正定的 $\Sigma$ 同時也是對稱矩陣，可進行 [正交對角化](/2025/10/15/對稱矩陣/#4-對角化性質)：
$$\Sigma = Q \Lambda Q^T$$
其中 $Q$ 為正交矩陣，$\Lambda$ 為包含非負特徵值的對角矩陣。這可用來讀出主軸方向（特徵向量）與各方向的變異度（特徵值）

## 相關連結

[向量的內積與外積](/2025/10/18/向量的內積與外積/)
[對稱矩陣](/2025/10/15/對稱矩陣/)
[正交矩陣](/2025/10/06/正交矩陣/)
[正定矩陣與半正定矩陣 (1) - 定義與性質](/2025/11/03/正定矩陣與半正定矩陣-1-定義與性質/)

## 參考資料

[機器學習 Lesson 13 — 特徵工程中的奇異值分解與共變異數的關係](https://flag-editors.medium.com/%E6%A9%9F%E5%99%A8%E5%AD%B8%E7%BF%92lesson-13-%E7%89%B9%E5%BE%B5%E5%B7%A5%E7%A8%8B%E4%B8%AD%E7%9A%84%E5%A5%87%E7%95%B0%E5%80%BC%E5%88%86%E8%A7%A3%E8%88%87%E5%85%B1%E8%AE%8A%E7%95%B0%E6%95%B8%E7%9A%84%E9%97%9C%E4%BF%82-21cf2b7a063a)
[Covariance and the regression line | Regression | Probability and Statistics | Khan Academy](https://www.youtube.com/watch?v=ualmyZiPs9w)
[統計課從沒搞清楚的事：算變異量為什麼要除以 n-1？什麼是「自由度」？](https://pansci.asia/archives/115065)
[共變異數矩陣的性質](https://ccjou.wordpress.com/2014/06/03/%E5%85%B1%E8%AE%8A%E7%95%B0%E6%95%B8%E7%9F%A9%E9%99%A3%E7%9A%84%E6%80%A7%E8%B3%AA/)

<style>
table {
  border-collapse: collapse;
}
th, td {
  padding: 6px 12px;
}
</style>
