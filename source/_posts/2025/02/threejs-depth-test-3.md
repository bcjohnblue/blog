---
title: Three.js 中物體的遠近關係 (3) - 深度值的轉換函式
date: 2025-02-28 23:52:36
categories: Three.js
tags:
mathjax: true
---

## 前言

上一篇提到了所有 **像素(fragment)** 對應的深度值都會落在 **[0, 1]** 區間內，**0** 代表這個 **像素(fragment)** 離相機最近，而 **1** 代表離相機最遠，此時有趣的地方來了，每個物體都有自己的 **z** 值，這個 **z** 值指的是相機與物體之間的距離，而場景上的物體這麼多，要如何把這些物體的 **z** 值轉換成深度值呢？這就是這篇文章探討的主軸 - 如何將場景中所有物體的 **z** 值轉換到深度值 **[0, 1]** 之間

<!--more-->

## z 值的座標系

首先我們先了解什麼是 **z** 值，在 [Vertex shader 做的事情](https://bcjohnblue.github.io/blog/2024/12/06/%E4%BD%BF%E7%94%A8-three-js-%E6%93%8D%E4%BD%9C-shader-%E7%95%AB%E5%87%BA%E5%9C%8B%E6%97%97/#Vertex-shader-%E5%81%9A%E7%9A%84%E4%BA%8B%E6%83%85) 這篇文章中，提到在 shader pipeline 執行的過程中會將物體的頂點數據轉換到不同的座標系統，從一開始的 **本地座標 (local coordinate)** 最終轉換到顯示在螢幕上的 **螢幕座標 (screen coordinate)** ，而這篇文章探討用來深度測試的 **z** 值，代表的是相機與物體的距離，所以指的就是 **VIEW SPACE** 的 **視圖座標(view coordinates)** 中的 **z** 值

<img src="https://learnopengl.com/img/getting-started/coordinate_systems.png" width="75%" height="75%">

[圖片來源](https://learnopengl.com/Getting-started/Coordinate-Systems)

## 深度值的線性轉換

要將所有物體的 **z** 值轉換到 **[0, 1]** 區間，最簡單的方式就是所謂的線性轉換：

{% raw %}

$$
\large F_{\text{depth}} = \frac{z - \text{near}}{\text{far} - \text{near}}
$$

{% endraw %}

套用以上方程式後

- 在 **近平面** 上的物體 $ z = near => F\_{\text{depth}} = 0 $
- 在 **遠平面** 上的物體 $ z = far => F\_{\text{depth}} = 1 $

假設 $ near = 1, far = 50 $ 可以畫出以下的圖，在 `near` 與 `far` 之間的所有 **z** 值其深度值都轉換到了 **[0, 1]** 之間

<div style="display: flex; justify-content: center;">
  <img src="./linear.png" width="75%" height="75%" >
</div>

## 實際上深度值的轉換函式

雖然上面的線性轉換方程可以很好的把所有 **z** 值都轉換到 **[0, 1]** 的區間內，但實際上深度值的轉換並不會使用這種簡單的方式，原因是人的眼睛總是對於位於近處的物體比較敏感，對於眼前的物體能夠很好的辨別他們之間的前後關係，但對於很遠的物體來說呢？越遠的物體在視野內會顯得越小，而人眼能夠辨別遠處物體前後關係肯定不如近處的物體好。這麼說來上面的線性方程就不能很好的描述這種狀況，因此實際上將深度值轉換的函式其 $ F\_{\text{depth}} $ 是和 $ 1/z $ 成正比的

{% raw %}
$$
F_{\text{depth}} = \frac{1/z - 1/near}{1/far - 1/near}
$$
{% endraw %}

- 在 **近平面** 上的物體 $ z = near => F\_{\text{depth}} = 0 $
- 在 **遠平面** 上的物體 $ z = far => F\_{\text{depth}} = 1 $

<div style="display: flex; justify-content: center;">
  <img src="./reciprocal.png" width="75%" height="75%" >
</div>

以 $ z = 10 $ 來看，原本的線性函式只涵蓋了 **[0, 0.2]** 深度值區間的範圍，而這個與 1/z 成正比的函式則是涵蓋了 **[0, 0.9]** 這麼多的深度值範圍，這代表了使用以 $ 1/z $ 轉換的函式，對於在近處的物體有更大範圍的深度值可以去覆蓋，也就是所謂的精度更高，在近處更能夠分辨物體的遠近關係

## 數學推導

以 $ 1/z $ 成比例的轉換函式可以很好的將原本的 **z** 值轉換到深度值的 **[0, 1]** 區間，也符合實際上人眼的感知狀況，但不知道有沒有人會覺得疑惑，為什麼實際上用來轉換深度值的函式就是這個呢？為什麼分母會是 $ 1/far - 1/near $ 呢，不會是其他數值的表達方式嗎？以下我們就用數學推導來解釋為什麼實際上深度值的轉換函式會是長成上面這樣子的

一開始有提到 z 值是 **VIEW SPACE 視圖座標(view coordinates)** 中 3D 物體座標的 z 值，**VIEW SPACE** 座標系是以相機為原點看出去各個物體呈現的 3D 座標，基本上指的就是一般我們人眼睛看出去的景象，但所有 3D 的物體最終都必須呈現在電腦 2D 的螢幕上，而這就是 **視圖座標(view coordinates)** 轉換到 **裁剪座標 (clip coordinates)** 的過程，由下圖可以看到 3D 場景上有紅球、黃球及綠球，但因為綠球並不在 **近平面** 及 **遠平面** 這個範圍的 [視錐體(frustum)](https://zh.wikipedia.org/zh-tw/%E8%A7%86%E4%BD%93) 中，所以最終投影到 2D 的 **近平面(near clip plane)** 時只留下了紅球及黃球，而這個 **近平面(near clip plane)** 實際上對應的就是電腦螢幕。再仔細看投影到 2D **近平面(near clip plane)** 的物體大小，會發現 Perspective 的透視投影會根據物體的遠近(z 值)，改變最終投影到 2D 平面上的大小，由於紅球較遠所以投射到 2D 平面上會變得比黃球還要小，但另一方面 Orthographic 的正交投影則不論物體遠近，投射到 2D 平面上的紅球及黃球都是一樣的大小。我們知道 Perspective 的透視投影就是模仿人類眼睛的投影方式，近處的物體看起來會比遠處的物體大，而在 **Three.js** 中用的就是 [PerspectiveCamera](https://threejs.org/docs/#api/en/cameras/PerspectiveCamera) 來模仿這一行為

<img src="https://glumpy.readthedocs.io/en/latest/_images/projection.png" width="75%" height="75%" >

[圖片來源](https://glumpy.readthedocs.io/en/latest/tutorial/cube-ugly.html#projection-matrix)

在 **視圖座標(view coordinates)** 的 3D 物體都有 $ [x, y, z] $ 三個點的座標代表其所在位置，而當投射到 **裁剪座標 (clip coordinates)** 時雖然已經變成 2D 的 $ [x, y] $ 座標了，但其實我們一樣可以用數學的方式求出物體在 2D 座標上的深度值 ($ F*{\text{depth}} $)，此時 2D 座標上的物體可以表示成 $ [x, y, F*{\text{depth}}] $，深度值 ($ F\_{\text{depth}} $) 可以從 3D 座標的 z 推算出來，下面我們以代表人眼的 Perspective projection 來進行推導

#### 透視除法 (Perspective division)

透視變換的概念也應用在 OpenGL 的渲染管道 (pipeline) 裡，在

<img src="https://learnopengl.com/img/getting-started/coordinate_systems.png" width="75%" height="75%">

[圖片來源](https://learnopengl.com/Getting-started/Coordinate-Systems)

<img src="https://leeyngdo.github.io/assets/images/computer-graphics/rendering-pipeline/vertex-processing.png" width="75%" height="75%" />

[圖片來源](https://leeyngdo.github.io/blog/computer-graphics/2024-02-29-graphics-pipeline/)

#### 標準化設備座標 (Normalized Device Coordinates)

第二個必備的知識是 **標準化設備座標(NDC)** ，**NDC** 主要用於將三維世界中的物體位置映射到二維螢幕空間，在 **Three.js** 底層 **OpenGL** 中 NDC 的範圍被定義在 [-1.0, 1.0] 之間，

<img src="https://www.songho.ca/opengl/files/gl_projectionmatrix01.png" width="75%" height="75%" >

## 參考資料

[深度测试](https://learnopengl-cn.readthedocs.io/zh/latest/04%20Advanced%20OpenGL/01%20Depth%20testing/)
[OpenGL Tutorial 14 - Depth Buffer](https://www.youtube.com/watch?v=3xGKu4T4SCU)
[Cesium 和 three.js 对数深度缓冲原理简析](https://blog.csdn.net/qq_21476953/article/details/141713244)
[Transformation matrix for projection of 3D objects into a 2D plane (projection transform)](https://www.mauriciopoppe.com/notes/computer-graphics/viewing/projection-transform/)
[一看就懂的OpenGL ES教程——走进3D的世界之坐标系统（下篇）](https://juejin.cn/post/7246334166950527034)
[Projection Matrix](https://hackmd.io/@23657689/projection_matrix#) - 數學推導的主要參考文章