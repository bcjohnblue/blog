---
title: Three.js 中物體的遠近關係 (4) - 對數深度值
date: 2025-03-17 01:06:00
categories: Three.js
tags:
mathjax: true
---

###### 此為 **Three.js 中物體的遠近關係** 系列文章 - 第 4 篇： 

1. <a href="/blog/2025/02/13/three-js-中物體的遠近關係-1-什麼是深度測試？/" target="_blank">Three.js 中物體的遠近關係 (1) - 什麼是深度測試？</a>
2. <a href="/blog/2025/02/28/three-js-中物體的遠近關係-2-左手-右手座標系與齊次座標/" target="_blank">Three.js 中物體的遠近關係 (2) - 左手/右手座標系與齊次座標</a>
3. <a href="/blog/2025/02/28/three-js-中物體的遠近關係-3-深度值的計算方式/" target="_blank">Three.js 中物體的遠近關係 (3) - 深度值的計算方式</a>
4. <a href="/blog/2025/03/17/three-js-中物體的遠近關係-4-對數深度值/" target="_blank">Three.js 中物體的遠近關係 (4) - 對數深度值</a>
5. <a href="/blog/2025/03/31/three-js-中物體的遠近關係-5-渲染物體的順序/" target="_blank">Three.js 中物體的遠近關係 (5) - 渲染物體的順序</a>
6. <a href="/blog/2025/05/04/three-js-中物體的遠近關係-6-如何正確的渲染透明粒子？/" target="_blank">Three.js 中物體的遠近關係 (6) - 如何正確的渲染透明粒子？</a>

## 前言

上一篇中我們知道**透視投影**的深度值 $ z\_{depth} $ 和 $ 1/z $ 成正比，如此與人眼感知的狀況相符，對於近處物體的分辨率較高，而遠處物體不容易分辨清楚互相的前後關係，大部分情況下這個深度值的轉換函式可以很好的描述物體的遠近，但如果套用到大尺度的場景，例如：太陽系、宇宙等，這種深度值的轉換函式就會出問題，這篇文章討論會出現哪些問題，以及最後如何使用對數深度值轉換函式解決

<!--more-->

## 深度值轉換函式的問題點

上一篇文章中推導出深度值轉換的函式如下：

{% raw %}

$$
\large z_{depth} = \frac{1/z - 1/near}{1/far - 1/near}
$$

{% endraw %}

<div style="display: flex; justify-content: center;">
  <img src="./reciprocal.png" />
</div>

以 **Z-value** 的範圍在 **[1, 50]** 來看，大約有九成的深度值都位在 **Z-value** 小於 10 的範圍內，這意味著離相機越遠的物體，深度值可以覆蓋的範圍越少，也就是說越遠的物體越難分辨他們的前後關係，對人眼來說這是正常的機制，但如果打算在 **Three.js** 中渲染大尺度的場景，可以想像位於非常遠處的兩個物體深度值會非常接近於 1，例如：0.9999991 v.s. 0.9999992，再加上電腦能儲存浮點數後的小數位數是有限制的，當四捨五入後兩個物體的深度值很有可能都等於 1，無法判別這兩個物體究竟誰在前誰在後，此時會看到畫面兩個物體互相重疊出現閃爍，這就是所謂的 [z-fighting 問題](https://en.wikipedia.org/wiki/Z-fighting)

## 大尺度場景下導致的 z-fighting

**Three.js** 的其中一個 [官方範例](https://threejs.org/examples/#webgl_camera_logarithmicdepthbuffer) 很好的展示了這個問題，畫面的左邊套用的是正常深度值轉換函式，也就是 $ z\_{depth} $ 與 $ 1/z $ 成正比，而右邊的是 **Three.js** 內建的對數深度值轉換函式，一進去這個場景會從 {% raw %} $ 10^{-6} \; \small m $ {% endraw %} 距離開始逐步將相機拉遠到 {% raw %} $ 10^{19} \; \small m $ {% endraw %} 遠的地方，從以下截圖可以看到當相機拉遠到太陽的尺度 {% raw %} $ 1.4 \times 10^{5} \; \small km $ {% endraw %} 時，左邊的畫面不斷閃爍出現很明顯的 **z-fighting**

<div style="display: flex; justify-content: center;">
  <img src="./scene-sun.png" />
</div>

## 對數深度值

在 **Three.js** 中啟用對數深度值的方式非常簡單，只要在 [WebGLRenderer](https://threejs.org/docs/#api/en/renderers/WebGLRenderer) 中將 `logarithmicDepthBuffer` 設為 `true` 就好了，以上範例左、右畫面在實作上就只是 `logarithmicDepthBuffer` 有沒有開啟而已

```js
const renderer = new THREE.WebGLRenderer({ logarithmicDepthBuffer: true });
```

光調整一個參數就可以讓畫面的呈現有這麽大的差別真是神奇，以下讓我們來看看 `logarithmicDepthBuffer` 底層的邏輯是如何實作的

P.S. 在這之前建議先閱讀 <a href="/blog/2025/02/08/理解-three-js-的-shader-架構/" target="_blank">理解-three-js-的-shader-架構</a>，了解 **Three.js** 中的 shader 是如何作用的

#### 定義 USE_LOGDEPTHBUF 常數

首先在 [WebGLProgram.js](https://github.com/mrdoob/three.js/blob/a6bbc0e4e2126f075327d667973443060fdd9e63/src/renderers/webgl/WebGLProgram.js#L654) 檔案裡，當 `logarithmicDepthBuffer = true` 時，會在 shaders 中定義 `USE_LOGDEPTHBUF` 常數

```js
parameters.logarithmicDepthBuffer ? '#define USE_LOGDEPTHBUF' : '',
```

當 `USE_LOGDEPTHBUF` 被定義時，會在 vertex shader 及 fragment shader 中添加一些變數，這些變數最後用來計算對數深度值

#### Vertex shader

- [logdepthbuf_pars_vertex.glsl.js](https://github.com/mrdoob/three.js/blob/db7233612cb0d6372605f414c0fa955d01d7916b/src/renderers/shaders/ShaderChunk/logdepthbuf_pars_vertex.glsl.js)

```js
export default /* glsl */ `
#ifdef USE_LOGDEPTHBUF

  varying float vFragDepth;
  varying float vIsPerspective;

#endif
`;
```

- [logdepthbuf_vertex.glsl.js](https://github.com/mrdoob/three.js/blob/db7233612cb0d6372605f414c0fa955d01d7916b/src/renderers/shaders/ShaderChunk/logdepthbuf_vertex.glsl.js)

```js
export default /* glsl */ `
#ifdef USE_LOGDEPTHBUF

  vFragDepth = 1.0 + gl_Position.w;
  vIsPerspective = float( isPerspectiveMatrix( projectionMatrix ) );

#endif
`;
```

#### Fragment shader

- [logdepthbuf_pars_fragment.glsl.js](https://github.com/mrdoob/three.js/blob/db7233612cb0d6372605f414c0fa955d01d7916b/src/renderers/shaders/ShaderChunk/logdepthbuf_pars_fragment.glsl.js)

```js
export default /* glsl */ `
#if defined( USE_LOGDEPTHBUF )

  uniform float logDepthBufFC;
  varying float vFragDepth;
  varying float vIsPerspective;

#endif
`;
```

- [logdepthbuf_fragment.glsl.js](https://github.com/mrdoob/three.js/blob/db7233612cb0d6372605f414c0fa955d01d7916b/src/renderers/shaders/ShaderChunk/logdepthbuf_fragment.glsl.js)

```js
export default /* glsl */ `
#if defined( USE_LOGDEPTHBUF )

  // Doing a strict comparison with == 1.0 can cause noise artifacts
  // on some platforms. See issue #17623.
  gl_FragDepth = vIsPerspective == 0.0 ? gl_FragCoord.z : log2( vFragDepth ) * logDepthBufFC * 0.5;

#endif
`;
```

## 對數深度值公式

其中最重要的變數是 `gl_FragDepth`，[gl_FragDepth](https://learnopengl-cn.readthedocs.io/zh/latest/04%20Advanced%20OpenGL/08%20Advanced%20GLSL/#:~:text=gl_FrontFacing%E6%AF%AB%E6%97%A0%E6%84%8F%E4%B9%89%E3%80%82-,gl_FragDepth,-%E8%BE%93%E5%85%A5%E5%8F%98%E9%87%8Fgl_FragCoord) 代表這個 fragment 的深度值，在這個式子中先利用 `vIsPerspective` 判斷是否為透視投影，當 `vIsPerspective == 0` 時不是透視投影，深度值套用預設的 `gl_FragCoord.z`，那如果是透視投影的話套用 `log2( vFragDepth ) * logDepthBufFC * 0.5`

```js
gl_FragDepth =
  vIsPerspective == 0.0
    ? gl_FragCoord.z
    : log2(vFragDepth) * logDepthBufFC * 0.5;
```

其中 `vFragDepth` 與 `logDepthBufFC` 的數值是從其他檔案引入進來的：

- [logdepthbuf_vertex.glsl.js](https://github.com/mrdoob/three.js/blob/db7233612cb0d6372605f414c0fa955d01d7916b/src/renderers/shaders/ShaderChunk/logdepthbuf_vertex.glsl.js)

```js
vFragDepth = 1.0 + gl_Position.w;
```

- [WebGLRenderer.js](https://github.com/mrdoob/three.js/blob/4f03f16366ca4ca2a49d4f19b7ec2db2ac8b0736/src/renderers/WebGLRenderer.js#L2409)

```js
p_uniforms.setValue(
  _gl,
  'logDepthBufFC',
  2.0 / (Math.log(camera.far + 1.0) / Math.LN2)
);
```

綜合以上資訊，透視投影的對數深度值公式以數學形式表示如下：

{% raw %}

$$
z_{depth} = \log_2(1 + w) \times \frac{2}{\frac{\log_e(f + 1)}{\log_e2}} \times 0.5
$$

{% endraw %}

套用以下已知條件，可以進一步將公式改寫：

##### 1. $ w $ 替換成 $ -z $

在上一篇文章 - <a href="/blog/2025/02/28/three-js-中物體的遠近關係-3-深度值的計算方式/#透視除法-Perspective-division" target="_blank">透視除法</a> 有求出 $ w = -z $，$ w $ 位於 **裁剪座標(clip coordinates)** 下，而  $ z $ 位於 **觀察座標(view coordinates)**

上面計算 `vFragDepth` 中的 `gl_Position.w` 指的是 **螢幕座標 (screen coordinate)** 下的 $ w $，由於**裁剪座標**的 $ w $ 在經過 [視口變換(viewport transform)](https://www.songho.ca/opengl/gl_viewport.html) 成**螢幕座標**後 [$ w $ 維持不變](https://youtu.be/k55KvDFu3_4?t=680)，因此計算 `vFragDepth` 中的 $ 1 + w $ 可以變成 $ 1 - z $，其中的 $ z $ 是**觀察座標**下的 $ z $

##### 2. 改寫對數部分

套用 [換底公式、倒數公式](https://zh.wikipedia.org/zh-tw/%E5%AF%B9%E6%95%B0%E6%81%92%E7%AD%89%E5%BC%8F#%E6%8D%A2%E5%BA%95%E5%85%AC%E5%BC%8F)：

{% raw %}

$$
\begin{align*}
\displaystyle
\frac{2}{\frac{\log_e(f + 1)}{\log_e2}} \times 0.5 &= \frac{\log_e2}{\log_e(f + 1)} = \frac{1}{\log_2(f + 1)} \\
\end{align*}
$$

{% endraw %}

所以改寫後的深度值如下：

{% raw %}

$$
z_{depth} = \frac{\log_2(-z + 1)}{\log_2(f + 1)}
$$

{% endraw %}

## 為什麼對數深度值的公式長這樣？

剛看到這個公式其實我想了很久，不知道為什麼對數深度值的公式是這個形式，後來研究許久後得出一些結論：

##### 1. 為什麼分子是 $ -z + 1 $ ?

一開始可以想到最簡單取對數的方式就是 $ \log_2{z} $，我們知道 $ z $ 的範圍會從 **近平面(-n)** 到 **遠平面(-f)**，其中**近平面**的距離可以小到完全接近相機也就是 $ z = 0 $，此時深度值的函式會變成 $ z_{depth} = \log_2{0} = -\infty $，由於深度值的範圍定義在 **[0, 1]** 之間，所以很明顯這樣是不合理的，而當分子改成 $ -z + 1 $ 時， $ z_{depth} = \log_2(-0 + 1) = \log_21 = 0 $，如此我們可以將 $ z $ 的最小值 $ 0 $ 正確映射到深度值區間的最小值 $ 0 $

##### 2. 為什麼分母是 $ f + 1 $ ?

當 $ z $ 值拉到最遠等於 **遠平面(-f)** 時，分子會變成 $ z\_{depth} = \log_2(f + 1) $，為了要符合深度值最大為 $ 1 $ 的限制，因此就必須除以分母 $ \log_2(f + 1) $

## 正常深度值與對數深度值的比較

接著我們以一開始提到 **Three.js** 大尺度場景的範例來看深度值套用兩種不同公式間的差別，設定物體與相機的距離 $ z $ 範圍為 $ 10^{-6} $ 到 $ 10^{19} $，畫出正常深度值(左圖) 與 對數深度值(右圖)

<div class="two-column">
  <div class="center">
    <b>正常深度值</b>
    <img src="./large-scale-reciprocal.png" />
  </div>
  <div class="center">
   <b>對數深度值</b>
    <img src="./large-scale-log.png" />
  </div>
</div>

可以看到之前正常深度值的優點是符合人眼感知的狀況，在近的地方深度值覆蓋範圍大，適合分辨近處物體，但當場景需要容納大尺度的 $ z $ 值時，這就變成缺點了，可以看到從 $ 10^3 $ 左右一直到 $ 10^{18} $ 的距離，轉換成深度值後幾乎都等於 $ 1 $，這導致 shader 在判定物體的遠近時，兩個物體深度值的差異小到無法正確判別，就導致畫面上閃爍的 **z-fighting** 問題了。反過來看對數深度值則是在 $ 10^0 $ 到 $ 10^{18} $ 之間分佈平均，可以正常判別物體深度值之間的差異

## 深度值的精度

最終我們來討論深度值的精度是如何設置的，上面提到藉由比較兩個物體的深度值判斷誰應該在前誰應該在後，而在電腦中是以浮點數的方式儲存深度值的，當兩個浮點數的差值小到電腦無法分辨的時候就會引起 **z-fighting** 的問題。上面我們在 fragment shader 中使用 `gl_FragDepth` 賦予每個 fragment 的深度值，在 <a href="/blog/2024/12/06/使用-three-js-操作-shader-畫出國旗/#Shader-數值的精度設置" target="_blank">Shader 數值的精度設置</a> 的部分有提過，fragment shader 中可設置的精度有三種 - **highp**, **mediump**, **lowp**

| 精細度  | 說明                 | 適用場景                                       |
| ------- | -------------------- | ---------------------------------------------- |
| highp   | 高精度 (32 位浮點數) | 需要高精度的計算，例如：光照、物理模擬等       |
| mediump | 中精度 (16 位浮點數) | 對精度要求中等的場景，例如：紋理 UV 坐標、顏色 |
| lowp    | 低精度 (8 位浮點數)  | 不太需要精度的場景，例如：簡單的顏色計算       |

在 Three.js 中創建 [WebGLRenderer](https://threejs.org/docs/#api/en/renderers/WebGLRenderer) 時預設將 **精度(precision)** 設為 **highp**，或是在 [Material.precision](https://threejs.org/docs/#api/en/materials/Material.precision) 中也可以個別修改 fragment 的**精度**

接著我們以大尺度場景的範例來看，當 $ near = 10^{-6}, far = 10^{19} $ 時，兩個很遠的物體 $ z_1 = 10^{15}, z_2 = 10^{18} $ 時他們的深度值差異是多少：

- 正常深度值的差異

{% raw %}

$$
z_{depth1} = \frac{1/z_1 - 1/near}{1/far - 1/near} = \frac{10^{-15} - 10^6}{10^{-19} - 10^6} \approx \frac{-10^6}{-10^6} = 1
$$

$$
z_{depth2} = \frac{1/z_2 - 1/near}{1/far - 1/near} = \frac{10^{-18} - 10^6}{10^{-19} - 10^6} \approx \frac{-10^6}{-10^6} = 1
$$

{% endraw %}

- 對數深度值的差異

{% raw %}

$$
z_{depth1} = \frac{\log_2(z_1 + 1)}{\log_2(far + 1)} = \frac{\log_2(10^{15} + 1)}{\log_2(10^{19} + 1)} \approx 0.7895
$$

$$
z_{depth2} = \frac{\log_2(z_2 + 1)}{\log_2(far + 1)} = \frac{\log_2(10^{18} + 1)}{\log_2(10^{19} + 1)} \approx 0.9474
$$

{% endraw %}

由於電腦只能用有限的位數儲存浮點數，所以當數值相差過大的數值進行加減時，有些位數就會直接被忽略 (ex. $ 10^{-15} - 10^6 \approx -10^6 $)，所以正常深度值的公式至少在 $ z > 10^{15} $ 以上的區間就已經無法分辨物體的遠近關係了，而對數深度值公式即使是 $ z $ 很大的狀況下一樣可以求出有效的深度值

## 延伸閱讀

#### 1. gl_FragDepth 的相容性

[gl_FragDepth](https://registry.khronos.org/webgl/specs/latest/2.0/#5.33) 是 WebGL 2.0 中改變 fragment 深度值的方式，在 WebGL 1.0 中則是使用 [gl_FragDepthEXT](https://developer.mozilla.org/en-US/docs/Web/API/EXT_frag_depth)，在 **Three.js** 的原始碼中對這部分進行了相容性的處理如果是 WebGL 1.0 的 `gl_FragDepthEXT` 的話會重新命名成 `gl_FragDepth`，因此最後賦予深度值的變數只需要使用 `gl_FragDepth` 就好

[WebGLProgram.js](https://github.com/mrdoob/three.js/blob/1095272ee053480256ea3b0d3a5827fc0340936f/src/renderers/webgl/WebGLProgram.js#L875)

```js
prefixFragment = [
  '#define varying in',
  ( parameters.glslVersion === GLSL3 ) ? '' : 'layout(location = 0) out highp vec4 pc_fragColor;',
  ( parameters.glslVersion === GLSL3 ) ? '' : '#define gl_FragColor pc_fragColor',
  '#define gl_FragDepthEXT gl_FragDepth',
  ...
]
```

#### 2. isPerspectiveMatrix 的實作

shader 中的 `isPerspectiveMatrix` 函式定義在 `common.glsl.js` 檔案中，是用投影矩陣的第三行第四列是否等於 -1 判斷透視投影矩陣

<div class="two-column">
  <div class="center">
    <b>透視投影矩陣</b>
    {% raw %}

    $$
    \begin{bmatrix}
    \displaystyle \frac{2n}{r-l} & 0 & \displaystyle \frac{r+l}{r-l} & 0 \\
    0 & \displaystyle \frac{2n}{t-b} & \displaystyle \frac{t+b}{t-b} & 0 \\
    0 & 0 & \displaystyle -\frac{f+n}{f-n} & \displaystyle -\frac{2 f n}{f - n} \\
    0 & 0 & -1 & 0 \\
    \end{bmatrix}
    $$

    {% endraw %}

  </div>
  <div class="center">
    <b>正交投影矩陣</b>
    {% raw %}

    $$
    \begin{bmatrix}
    \displaystyle \frac{1}{r} & 0 & 0 & 0 \\
    0 & \displaystyle \frac{1}{t} & 0 & 0 \\
    0 & 0 & \displaystyle -\frac{2}{f-n} & \displaystyle -\frac{f+n}{f-n} \\
    0 & 0 & 0 & 1 \\
    \end{bmatrix}
    $$

    {% endraw %}

  </div>
</div>

透視投影矩陣與正交投影矩陣的推導過程請見 [Projection Matrix](https://hackmd.io/@23657689/projection_matrix#)

[common.glsl.js](https://github.com/mrdoob/three.js/blob/1095272ee053480256ea3b0d3a5827fc0340936f/src/renderers/shaders/ShaderChunk/common.glsl.js#L88)

```js
bool isPerspectiveMatrix( mat4 m ) {
  return m[ 2 ][ 3 ] == - 1.0;
}
```

#### 3. gl_FragCoord.z 的定義

在 fragment shader 中可以取得的 [gl_FragCoord](https://registry.khronos.org/OpenGL-Refpages/gl4/html/gl_FragCoord.xhtml) 是 OpenGL 中定義的變數，其中 `gl_FragCoord.z` 指的就是 **[0, 1]** 區間的深度值，因此當不是透視投影時(`vIsPerspective == 0.0`)，將預設的深度值 `gl_FragCoord.z` 賦值給 `gl_FragDepth`

```glsl
gl_FragDepth = vIsPerspective == 0.0 ? gl_FragCoord.z : log2( vFragDepth ) * logDepthBufFC * 0.5;
```

#### 4. issue #17623 的浮點數精度 bug

在計算 `gl_FragDepth` 的地方有一段註解說明 [issue #17623](https://github.com/mrdoob/three.js/issues/17623) 的 bug：

```js
export default /* glsl */ `
#if defined( USE_LOGDEPTHBUF )

  // Doing a strict comparison with == 1.0 can cause noise artifacts
  // on some platforms. See issue #17623.
  gl_FragDepth = vIsPerspective == 0.0 ? gl_FragCoord.z : log2( vFragDepth ) * logDepthBufFC * 0.5;

#endif
`;
```

進一步查詢可以發現之前的程式是這樣寫的：

```js
export default /* glsl */ `
#if defined( USE_LOGDEPTHBUF )

  gl_FragDepth = vIsPerspective == 1.0 ? log2( vFragDepth ) * logDepthBufFC * 0.5 : gl_FragCoord.z;

#endif
`;
```

可以看到計算深度值的邏輯完全沒變，差別只在以前是 `vIsPerspective == 1.0` 而現在是判斷 `vIsPerspective == 0.0`，經過一些研究後看起來是判斷 `vIsPerspective == 1.0` 時會有浮點數精度誤差的問題，也就是說有些 GPU 的實作 `1.0` 可能會變成 `0.99999994` 或 `1.0000001` 導致判斷失效，因此最後解決方案改為判斷 `vIsPerspective == 0.0`

## 參考資料

[Logarithmic depth buffer optimizations & fixes](https://outerra.blogspot.com/2013/07/logarithmic-depth-buffer-optimizations.html)
[Cesium 和 three.js 对数深度缓冲原理简析](https://blog.csdn.net/qq_21476953/article/details/141713244)
[gl_FragCoord.z 的定義討論](https://community.khronos.org/t/gl-fragcoord-z/54092)
[gl_FragCoord 官方文檔](https://registry.khronos.org/OpenGL-Refpages/gl4/html/gl_FragCoord.xhtml)
[gl_FragCoord 的含义](https://blog.csdn.net/fatcat123/article/details/103861403)
[How Does a camera convert from clip space into screen space?](https://stackoverflow.com/questions/57938025/how-does-a-camera-convert-from-clip-space-into-screen-space)
[OpenGL clip space, NDC, and screen space](https://www.youtube.com/watch?v=k55KvDFu3_4)

- 浮點數精度
  [淺談 DeepLearning 的浮點數精度 FP32/FP16/TF32/BF16........(以 LLM 為例)](https://vocus.cc/article/65ee9d51fd89780001eb4d59)
  [CPU 與 GPU 計算浮點數的差異](https://tigercosmos.xyz/post/2020/12/system/floating-number-cpu-gpu/)
  [15 张图带你深入理解浮点数](https://polarisxu.studygolang.com/posts/basic/diagram-float-point/)
  [详解浮点数](https://zhaoshihan.github.io/posts/16352/)

{% raw %}

<style>
.two-column {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 10px;

  .center {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
  }

  img {
    width: 100%;
    object-fit: cover;
  }
}

table {
  th, td {
   padding: 8px 12px;
  } 
}

@media (max-width: 600px) {
  img {
      width: 100%;
  }
}

@media (min-width: 601px) {
  img {
      width: 75%;
  }
}
</style>

{% endraw %}
