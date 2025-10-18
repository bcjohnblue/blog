---
title: 向量內積
date: 2025-10-18 22:34:41
updated: 2025-10-18 22:34:41
categories: 線性代數
mathjax: true
---

## 定義

對於兩個向量 $\mathbf{u}$ 和 $\mathbf{v}$，內積有兩種等價的定義方式：

### 定義一：分量乘積之和

$$\mathbf{u} \cdot \mathbf{v} = u_1v_1 + u_2v_2 + \cdots + u_nv_n$$

其中 $\mathbf{u} = [u_1, u_2, \ldots, u_n]$ 和 $\mathbf{v} = [v_1, v_2, \ldots, v_n]$ 是 $n$ 維向量。

### 定義二：矩陣乘法形式

$$\mathbf{u} \cdot \mathbf{v} = \mathbf{u}^T \mathbf{v}$$

這可以表示為：

{% raw %}

$$\mathbf{u} \cdot \mathbf{v} = \begin{bmatrix} u_1 & u_2 & \cdots & u_n \end{bmatrix} \begin{bmatrix} v_1 \\ v_2 \\ \vdots \\ v_n \end{bmatrix}$$

{% endraw %}

<!-- more -->

## 重要性質

設 $\mathbf{u}$ 和 $\mathbf{v}$ 為向量，$A$ 為矩陣，$c$ 為純量，則內積具有以下性質：

### 1. 與範數的關係

{% raw %}

$$\mathbf{u} \cdot \mathbf{u} = \|\mathbf{u}\|^2$$

{% endraw %}

這個性質連接了內積與向量範數（長度）的平方

### 2. 零向量性質

$$\mathbf{u} \cdot \mathbf{u} = 0 \text{ 若且唯若 } \mathbf{u} = \mathbf{0}$$

一個向量與自身的內積為零，只有當 $\mathbf{u}$ 是零向量時成立

### 3. 交換律

$$\mathbf{u} \cdot \mathbf{v} = \mathbf{v} \cdot \mathbf{u}$$

內積滿足交換律，這與矩陣乘法不同

### 4. 分配律（左分配）

$$\mathbf{u} \cdot (\mathbf{v} + \mathbf{w}) = \mathbf{u} \cdot \mathbf{v} + \mathbf{u} \cdot \mathbf{w}$$

### 5. 分配律（右分配）

$$(\mathbf{v} + \mathbf{w}) \cdot \mathbf{u} = \mathbf{v} \cdot \mathbf{u} + \mathbf{w} \cdot \mathbf{u}$$

### 6. 純量乘法

$$(c\mathbf{u}) \cdot \mathbf{v} = c(\mathbf{u} \cdot \mathbf{v}) = \mathbf{u} \cdot (c\mathbf{v})$$

純量可以從內積中提出

### 7. 純量乘法下的範數

{% raw %}

$$\|c\mathbf{u}\| = |c| \|\mathbf{u}\|$$

{% endraw %}

### 8. 與矩陣乘法的關係

$$A\mathbf{u} \cdot \mathbf{v} = (A\mathbf{u})^T \mathbf{v} = \mathbf{u}^T(A^T \mathbf{v}) = \mathbf{u} \cdot A^T \mathbf{v}$$

這個性質展示了矩陣乘法與內積以及矩陣轉置之間的關係

## 幾何意義

### 1. 向量長度

內積與向量長度的關係：

{% raw %}

$$\|\mathbf{u}\| = \sqrt{\mathbf{u} \cdot \mathbf{u}}$$

{% endraw %}

### 2. 角度關係

對於非零向量 $\mathbf{u}$ 和 $\mathbf{v}$：

{% raw %}

$$\mathbf{u} \cdot \mathbf{v} = \|\mathbf{u}\| \|\mathbf{v}\| \cos\theta$$

{% endraw %}

其中 $\theta$ 是兩向量之間的夾角

### 3. 正交性

兩個向量正交（垂直）：

{% raw %}

$$\mathbf{u} \cdot \mathbf{v} = 0$$

{% endraw %}

## 具體例子

### 例子 1：二維向量

{% raw %}

$$\mathbf{u} = [3, 4] \quad \mathbf{v} = [1, 2]$$

{% endraw %}

$$\mathbf{u} \cdot \mathbf{v} = 3 \cdot 1 + 4 \cdot 2 = 3 + 8 = 11$$

### 例子 2：正交向量

$$\mathbf{u} =[1, 2] \quad \mathbf{v} = [-2, 1]$$

$$\mathbf{u} \cdot \mathbf{v} = 1 \cdot (-2) + 2 \cdot 1 = -2 + 2 = 0$$

## 相關連結

[轉置矩陣](/2025/09/27/轉置矩陣/)

## 參考資料

[Linear Algebra Lecture 29: Orthogonality](https://youtu.be/hxI7stenqaw?si=H62Iv81SjRqXhlAH&t=320)
