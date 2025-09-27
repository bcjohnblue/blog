---
title: 轉置矩陣
date: 2025-09-27 22:54:58
updated: 2025-09-27 22:54:58
categories: 線性代數
mathjax: true
---

## 定義

對於矩陣 $A$，其轉置矩陣表示為 $A^T$，其中：

$$A_{ij}^T = A_{ji}$$

換句話說，原矩陣的第 $i$ 行第 $j$ 列元素，在轉置矩陣中變成第 $j$ 行第 $i$ 列元素

## 例子

{% raw %}

$$\begin{bmatrix} 1 & 2 \\ 3 & 4 \end{bmatrix}^T = \begin{bmatrix} 1 & 3 \\ 2 & 4 \end{bmatrix}$$

$$\begin{bmatrix} 1 & 2 \\ 3 & 4 \\ 5 & 6 \end{bmatrix}^T = \begin{bmatrix} 1 & 3 & 5 \\ 2 & 4 & 6 \end{bmatrix}$$

{% endraw %}

<!-- more -->

## 特性

### 1. 轉置的轉置

$$(A^T)^T = A$$

轉置矩陣的轉置等於原矩陣本身

### 2. 矩陣加法的轉置

$$(A + B)^T = A^T + B^T$$

兩個矩陣和的轉置等於各自轉置的和

### 3. 純量乘法的轉置

$$(cA)^T = cA^T$$

純量乘以矩陣的轉置等於純量乘以矩陣的轉置

### 4. 矩陣乘法的轉置

$$(AB)^T = B^T A^T$$

兩個矩陣乘積的轉置等於其轉置矩陣的逆序乘積

**證明：**

設 $A$ 是 $m\times n$ 矩陣，$B$ 是 $n\times p$ 矩陣。記 $(AB)_{ij}$ 為 $AB$ 的第 $i$ 行第 $j$ 列元素，則

{% raw %}

$$
(AB)_{ij} = \sum_{k=1}^n a_{ik} b_{kj}
$$

{% endraw %}

轉置後，第 $i$ 行第 $j$ 列變成第 $j$ 行第 $i$ 列，因此

{% raw %}

$$
\big((AB)^T\big)_{ij} = (AB)_{ji}
= \sum_{k=1}^n a_{jk} b_{ki}
$$

{% endraw %}

另一方面，考慮 $B^T A^T$，它是 $p\times m$ 矩陣，其第 $i$ 行第 $j$ 列為

{% raw %}

$$
(B^T A^T)_{ij} = \sum_{k=1}^n (B^T)_{ik} (A^T)_{kj}
= \sum_{k=1}^n b_{ki} a_{jk}
$$

{% endraw %}

由於純量乘法可交換，$\sum_{k} a_{jk} b_{ki} = \sum_{k} b_{ki} a_{jk}$，因此對任意 $i,j$：

{% raw %}

$$
\big((AB)^T\big)_{ij} = (B^T A^T)_{ij}
$$

{% endraw %}

因為對所有元素都相等，故矩陣相等：

$$
(AB)^T = B^T A^T
$$

**範例：**

令
{% raw %}

$$
A=\begin{bmatrix}1&2&3\\4&5&6\end{bmatrix} \quad
B=\begin{bmatrix}7&8\\9&10\\11&12\end{bmatrix}
$$

{% endraw %}

計算得

{% raw %}

$$
AB=\begin{bmatrix}58&64\\139&154\end{bmatrix}
\quad\Rightarrow\quad
(AB)^T=\begin{bmatrix}58&139\\64&154\end{bmatrix}
$$

{% endraw %}

而

{% raw %}

$$
B^T=\begin{bmatrix}7&9&11\\8&10&12\end{bmatrix} \quad
A^T=\begin{bmatrix}1&4\\2&5\\3&6\end{bmatrix}
$$

$$
B^T A^T = \begin{bmatrix}58&139\\64&154\end{bmatrix}
$$

{% endraw %}

兩邊相等，驗證成立

從另一個方向來思考，最後求出來的元素：

- $a_{11}$ - 58

一開始是由 (1, 2, 3) 與 (7, 9, 11) 這兩組互乘得到 58，轉置過後是由 (7, 9, 11) 與 (1, 2, 3) 互乘，最後得到的結果一樣

- $a_{12}$ - 139

一開始是由 (4, 5, 6) 與 (7, 9, 11) 這兩組互乘得到 139，轉置過後是由 (7, 9, 11) 與 (4, 5, 6) 互乘，最後得到的結果一樣

- $a_{21}$ - 64

一開始是由 (1, 2, 3) 與 (8, 10, 12) 這兩組互乘得到 64，轉置過後是由 (8, 10, 12) 與 (1, 2, 3) 互乘，最後得到的結果一樣

- $a_{22}$ - 154

一開始是由 (4, 5, 6) 與 (8, 10, 12) 這兩組互乘得到 154，轉置過後是由 (8, 10, 12) 與 (4, 5, 6) 互乘，最後得到的結果一樣

## 參考資料

[wiki - 轉置矩陣](https://zh.wikipedia.org/zh-tw/%E8%BD%AC%E7%BD%AE%E7%9F%A9%E9%98%B5)
[LA21矩陣的轉置](https://www.youtube.com/watch?v=5RDG5KWTZ5A)