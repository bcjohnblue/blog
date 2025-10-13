---
title: 線性生成空間 - span
date: 2025-10-13 21:53:16
updated: 2025-10-13 21:53:16
categories: 線性代數
mathjax: true
---

## 什麼是 Span？

**線性生成空間(Span)** 是線性代數中一個核心概念，它描述了給定向量集合能夠「生成」的所有可能向量的集合

### 定義

設 $S = \{\mathbf{v}_1, \mathbf{v}_2, \ldots, \mathbf{v}_k\}$ 是向量空間 $V$ 中的一個向量集合，則 $S$ 的 **span** 定義為：

{% raw %}

$$\text{span}(S) = \text{span}\{\mathbf{v}_1, \mathbf{v}_2, \ldots, \mathbf{v}_k\} = \{c_1\mathbf{v}_1 + c_2\mathbf{v}_2 + \cdots + c_k\mathbf{v}_k : c_1, c_2, \ldots, c_k \in \mathbb{R}\}$$

{% endraw %}

換句話說，$\text{span}(S)$ 是所有 $S$ 中向量的線性組合所構成的集合

<!-- more -->

## 範例 1：二維空間中的 span

{% raw %}
考慮向量 $\mathbf{v}_1 = \begin{bmatrix} 1 \\ 0 \end{bmatrix}$ 和 $\mathbf{v}_2 = \begin{bmatrix} 0 \\ 1 \end{bmatrix}$

$$\text{span}\{\mathbf{v}_1, \mathbf{v}_2\} = \{c_1\begin{bmatrix} 1 \\ 0 \end{bmatrix} + c_2\begin{bmatrix} 0 \\ 1 \end{bmatrix} : c_1, c_2 \in \mathbb{R}\}$$

{% endraw %}

這個 span 就是整個二維空間 $\mathbb{R}^2$

## 範例 2：共線向量的 span

{% raw %}

考慮向量 $\mathbf{v}_1 = \begin{bmatrix} 1 \\ 2 \end{bmatrix}$ 和 $\mathbf{v}_2 = \begin{bmatrix} 2 \\ 4 \end{bmatrix}$

{% endraw %}

由於 $\mathbf{v}_2 = 2\mathbf{v}_1$，這兩個向量共線，所以：

{% raw %}

$$\text{span}\{\mathbf{v}_1, \mathbf{v}_2\} = \text{span}\{\mathbf{v}_1\} = \{c\begin{bmatrix} 1 \\ 2 \end{bmatrix} : c \in \mathbb{R}\}$$

{% endraw %}

這是一個通過原點的直線

## 範例 3：三維空間中的平面

{% raw %}
考慮向量 $\mathbf{v}_1 = \begin{bmatrix} 1 \\ 0 \\ 0 \end{bmatrix}$ 和 $\mathbf{v}_2 = \begin{bmatrix} 0 \\ 1 \\ 0 \end{bmatrix}$

$$\text{span}\{\mathbf{v}_1, \mathbf{v}_2\} = \{c_1\begin{bmatrix} 1 \\ 0 \\ 0 \end{bmatrix} + c_2\begin{bmatrix} 0 \\ 1 \\ 0 \end{bmatrix} : c_1, c_2 \in \mathbb{R}\} = \{\begin{bmatrix} c_1 \\ c_2 \\ 0 \end{bmatrix} : c_1, c_2 \in \mathbb{R}\}$$

{% endraw %}

這是一個通過原點的平面（$xy$ 平面）

## 如何判斷向量是否在 span 中？

{% raw %}

要判斷向量 $\mathbf{b}$ 是否在 $\text{span}\{\mathbf{v}_1, \mathbf{v}_2, \ldots, \mathbf{v}_k\}$ 中，需要檢查是否存在純量 $c_1, c_2, \ldots, c_k$ 使得：

$$\mathbf{b} = c_1\mathbf{v}_1 + c_2\mathbf{v}_2 + \cdots + c_k\mathbf{v}_k$$

這等價於求解線性方程組：

$$[\mathbf{v}_1 \quad \mathbf{v}_2 \quad \cdots \quad \mathbf{v}_k]\begin{bmatrix} c_1 \\ c_2 \\ \vdots \\ c_k \end{bmatrix} = \mathbf{b}$$

{% endraw %}

### 範例：判斷向量是否在 span 中

{% raw %}

設 $\mathbf{v}_1 = \begin{bmatrix} 1 \\ 2 \\ 3 \end{bmatrix}$，$\mathbf{v}_2 = \begin{bmatrix} 0 \\ 1 \\ 1 \end{bmatrix}$，$\mathbf{b} = \begin{bmatrix} 2 \\ 5 \\ 7 \end{bmatrix}$。

我們需要檢查是否存在 $c_1, c_2$ 使得：

$$c_1\begin{bmatrix} 1 \\ 2 \\ 3 \end{bmatrix} + c_2\begin{bmatrix} 0 \\ 1 \\ 1 \end{bmatrix} = \begin{bmatrix} 2 \\ 5 \\ 7 \end{bmatrix}$$

{% endraw %}

這給出方程組：

- $c_1 = 2$
- $2c_1 + c_2 = 5 \Rightarrow 4 + c_2 = 5 \Rightarrow c_2 = 1$
- $3c_1 + c_2 = 7 \Rightarrow 6 + 1 = 7 \Rightarrow 7 = 7$

{% raw %}

因此 $\mathbf{b} \in \text{span}\{\mathbf{v}_1, \mathbf{v}_2\}$，且 $\mathbf{b} = 2\mathbf{v}_1 + \mathbf{v}_2$

{% endraw %}

## 相關連結

[零空間(Null space)](/2025/10/13/零空間-null-space/)
[代數重數與幾何重數](/2025/10/13/代數重數與幾何重數/)

## 參考資料

[[線性代數] Span 的概念](https://www.youtube.com/watch?v=FEnFCtNILtI)
[wiki - 線性生成空間](https://zh.wikipedia.org/zh-tw/%E7%BA%BF%E6%80%A7%E7%94%9F%E6%88%90%E7%A9%BA%E9%97%B4)
