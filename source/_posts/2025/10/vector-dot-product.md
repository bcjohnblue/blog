---
title: 向量的內積與外積
date: 2025-10-18 22:34:41
updated: 2025-12-07 22:34:41
categories: 線性代數
mathjax: true
---

## 向量內積

**向量內積(inner product)** 又稱 **點積(dot product)**，是線性代數中最重要的向量運算之一，內積的結果是一個**純量**

對於兩個向量 $\mathbf{u}$ 和 $\mathbf{v}$，內積有兩種等價的定義方式：

### 定義一：分量形式

$$\mathbf{u} \cdot \mathbf{v} = u_1v_1 + u_2v_2 + \cdots + u_nv_n$$

其中 $\mathbf{u}$ 和 $\mathbf{v}$ 是 $n$ 維向量：

{% raw %}

$$\mathbf{u} = \begin{bmatrix} u_1 \\ u_2 \\ \vdots \\ u_n \end{bmatrix} \quad \mathbf{v} = \begin{bmatrix} v_1 \\ v_2 \\ \vdots \\ v_n \end{bmatrix}$$

{% endraw %}

### 定義二：矩陣乘法形式

$$\mathbf{u} \cdot \mathbf{v} = \mathbf{u}^T \mathbf{v}$$

這可以表示為：

{% raw %}

$$\mathbf{u} \cdot \mathbf{v} = \begin{bmatrix} u_1 & u_2 & \cdots & u_n \end{bmatrix} \begin{bmatrix} v_1 \\ v_2 \\ \vdots \\ v_n \end{bmatrix}$$

{% endraw %}

<!-- more -->

### 內積的性質

設 $\mathbf{u}$ 和 $\mathbf{v}$ 為向量，$A$ 為矩陣，$c$ 為純量，則內積具有以下性質：

#### 1. 與範數的關係

{% raw %}

$$\mathbf{u} \cdot \mathbf{u} = \|\mathbf{u}\|^2$$

{% endraw %}

這個性質連接了內積與向量範數（長度）的平方

#### 2. 零向量性質

$$\mathbf{u} \cdot \mathbf{u} = 0 \text{ 若且唯若 } \mathbf{u} = \mathbf{0}$$

一個向量與自身的內積為零，只有當 $\mathbf{u}$ 是零向量時成立

#### 3. 交換律

$$\mathbf{u} \cdot \mathbf{v} = \mathbf{v} \cdot \mathbf{u}$$

內積滿足交換律，這與矩陣乘法不同

#### 4. 分配律（左分配）

$$\mathbf{u} \cdot (\mathbf{v} + \mathbf{w}) = \mathbf{u} \cdot \mathbf{v} + \mathbf{u} \cdot \mathbf{w}$$

#### 5. 分配律（右分配）

$$(\mathbf{v} + \mathbf{w}) \cdot \mathbf{u} = \mathbf{v} \cdot \mathbf{u} + \mathbf{w} \cdot \mathbf{u}$$

#### 6. 純量乘法

$$(c\mathbf{u}) \cdot \mathbf{v} = c(\mathbf{u} \cdot \mathbf{v}) = \mathbf{u} \cdot (c\mathbf{v})$$

純量可以從內積中提出

#### 7. 純量乘法下的範數

{% raw %}

$$\|c\mathbf{u}\| = |c| \|\mathbf{u}\|$$

{% endraw %}

#### 8. 與矩陣乘法的關係

$$A\mathbf{u} \cdot \mathbf{v} = (A\mathbf{u})^T \mathbf{v} = \mathbf{u}^T(A^T \mathbf{v}) = \mathbf{u} \cdot A^T \mathbf{v}$$

這個性質展示了矩陣乘法與內積以及矩陣轉置之間的關係

### 內積的幾何意義

#### 1. 向量長度

內積與向量長度的關係：

{% raw %}

$$\|\mathbf{u}\| = \sqrt{\mathbf{u} \cdot \mathbf{u}}$$

{% endraw %}

#### 2. 角度關係

對於非零向量 $\mathbf{u}$ 和 $\mathbf{v}$：

{% raw %}

$$\mathbf{u} \cdot \mathbf{v} = \|\mathbf{u}\| \|\mathbf{v}\| \cos\theta$$

{% endraw %}

其中 $\theta$ 是兩向量之間的夾角

#### 3. 正交性

兩個向量正交（垂直）：

{% raw %}

$$\mathbf{u} \cdot \mathbf{v} = 0$$

{% endraw %}

### 內積的具體例子

#### 例子 1：二維向量

{% raw %}

$$\mathbf{u} = \begin{bmatrix} 3 \\ 4 \end{bmatrix} \quad \mathbf{v} = \begin{bmatrix} 1 \\ 2 \end{bmatrix}$$

{% endraw %}

$$\mathbf{u} \cdot \mathbf{v} = 3 \cdot 1 + 4 \cdot 2 = 3 + 8 = 11$$

#### 例子 2：正交向量

{% raw %}

$$\mathbf{u} = \begin{bmatrix} 1 \\ 2 \end{bmatrix} \quad \mathbf{v} = \begin{bmatrix} -2 \\ 1 \end{bmatrix}$$

{% endraw %}

$$\mathbf{u} \cdot \mathbf{v} = 1 \cdot (-2) + 2 \cdot 1 = -2 + 2 = 0$$

## 向量外積

**向量外積(outer product)** 又稱 **張量積**，是另一個重要的向量運算，與內積不同，外積的結果是一個矩陣而非純量

對於兩個向量 $\mathbf{u}$ 和 $\mathbf{v}$，外積有兩種等價的定義方式：

### 定義一：分量形式

對於 $m$ 維向量 $\mathbf{u}$ 和 $n$ 維向量 $\mathbf{v}$：

{% raw %}

$$\mathbf{u} = \begin{bmatrix} u_1 \\ u_2 \\ \vdots \\ u_m \end{bmatrix} \quad \mathbf{v} = \begin{bmatrix} v_1 \\ v_2 \\ \vdots \\ v_n \end{bmatrix}$$

{% endraw %}

外積定義為一個 $m \times n$ 矩陣，其中第 $i$ 行第 $j$ 列的元素為 $u_i v_j$：

{% raw %}

$$\mathbf{u} \otimes \mathbf{v} = \begin{bmatrix} u_1 v_1 & u_1 v_2 & \cdots & u_1 v_n \\ u_2 v_1 & u_2 v_2 & \cdots & u_2 v_n \\ \vdots & \vdots & \ddots & \vdots \\ u_m v_1 & u_m v_2 & \cdots & u_m v_n \end{bmatrix}$$

{% endraw %}

### 定義二：矩陣乘法形式

$$\mathbf{u} \otimes \mathbf{v} = \mathbf{u} \mathbf{v}^T$$

這可以表示為：

{% raw %}

$$\mathbf{u} \otimes \mathbf{v} = \begin{bmatrix} u_1 \\ u_2 \\ \vdots \\ u_m \end{bmatrix} \begin{bmatrix} v_1 & v_2 & \cdots & v_n \end{bmatrix}$$

{% endraw %}

### 外積的性質

設 $\mathbf{u}$、$\mathbf{v}$、$\mathbf{w}$ 為向量，$c$ 為純量，則外積具有以下性質：

#### 1. 分配律

$$\mathbf{u} \otimes (\mathbf{v} + \mathbf{w}) = \mathbf{u} \otimes \mathbf{v} + \mathbf{u} \otimes \mathbf{w}$$

#### 2. 純量乘法

$$(c\mathbf{u}) \otimes \mathbf{v} = c(\mathbf{u} \otimes \mathbf{v}) = \mathbf{u} \otimes (c\mathbf{v})$$

純量可以從外積中提出

#### 3. 轉置關係

$$(\mathbf{u} \otimes \mathbf{v})^T = \mathbf{v} \otimes \mathbf{u}$$

### 外積的 Rank 性質

外積運算具有一個重要的性質：**外積矩陣的 rank 都為 1**（當 $\mathbf{u}$ 和 $\mathbf{v}$ 都是非零向量時）

這是因為外積矩陣 $\mathbf{u} \mathbf{v}^T$ 的每一列都是向量 $\mathbf{u}$ 的純量倍數：

{% raw %}

$$\mathbf{u} \mathbf{v}^T = \begin{bmatrix} v_1 \mathbf{u} & v_2 \mathbf{u} & \cdots & v_n \mathbf{u} \end{bmatrix}$$

{% endraw %}

因此所有列向量都線性相關，矩陣的列空間只有一維，rank 為 1

### 例子

設：

{% raw %}

$$\mathbf{u} = \begin{bmatrix} 1 \\ 2 \\ 3 \end{bmatrix} \quad \mathbf{v} = \begin{bmatrix} 4 \\ 5 \end{bmatrix}$$

{% endraw %}

則：

{% raw %}

$$\mathbf{u} \otimes \mathbf{v} = \mathbf{u} \mathbf{v}^T = \begin{bmatrix} 1 \\ 2 \\ 3 \end{bmatrix} \begin{bmatrix} 4 & 5 \end{bmatrix} = \begin{bmatrix} 4 & 5 \\ 8 & 10 \\ 12 & 15 \end{bmatrix}$$

{% endraw %}

可以驗證這個矩陣的 rank 為 1，因為第二列是第一列的 2 倍，第三列是第一列的 3 倍

## 相關連結

[轉置矩陣](/2025/09/27/轉置矩陣/)
[矩陣中的軸元與秩](/2025/09/24/矩陣中的軸元與秩/)
[向量內積](/2023/12/11/向量內積/)

## 參考資料

[Linear Algebra Lecture 29: Orthogonality](https://youtu.be/hxI7stenqaw?si=H62Iv81SjRqXhlAH&t=320)
[wiki - 外積 (張量積)](<https://zh.wikipedia.org/wiki/%E5%A4%96%E7%A7%AF_(%E5%BC%A0%E9%87%8F%E7%A7%AF)>)
