---
title: 理解 Three.js 的 shader 架構
date: 2025-02-08 09:06:36
updated: 2025-05-11 02:06:00
categories: Three.js
tags:
---

## 前言

繼上一篇 [使用 Three.js 操作 shader 畫出國旗](https://bcjohnblue.github.io/2024/12/06/%E4%BD%BF%E7%94%A8-three-js-%E6%93%8D%E4%BD%9C-shader-%E7%95%AB%E5%87%BA%E5%9C%8B%E6%97%97/) 後，這一篇文章希望了解 Three.js 裡的 shader 架構

## WebGL 裡的 shader 綁定

因為 Three.js 的底層就是 WebGL 所以我們可以先從 WebGL 是如何綁定 shader 來找到 Three.js 中處理 shader 的起始點

```js
// WebGL 綁定 shader 部分片段

const canvas = document.querySelector('canvas');
const gl = canvas.getContext('webgl'); // WebGL 1.0
// 或
const gl = canvas.getContext('webgl2'); // WebGL 2.0

// 1. 創建 shader
const vertexShader = gl.createShader(gl.VERTEX_SHADER);

// 2. 撰寫 shader code
const vertexShaderSource = `
    attribute vec4 aPosition;
    void main() {
        gl_Position = aPosition;
    }
`;
gl.shaderSource(vertexShader, vertexShaderSource);

gl.compileShader(vertexShader); // 3. 編譯 shader
const shaderProgram = gl.createProgram(); // 4. 創建 program
gl.attachShader(shaderProgram, vertexShader); // 5. 附加 shader 到 program
gl.linkProgram(shaderProgram); // 6. 連接 program
gl.useProgram(shaderProgram); // 7. 使用 program
```

<!--more-->

在 Three.js 的原始碼裡面也可以看到類似的邏輯，利用 `WebGLShader` 這個函式將寫好的 shader 以 `string` 的變數名稱傳入 `gl.shaderSource`，接著就是往上找看什麼地方用到 `WebGLShader` 並且找到傳入的 `string` 是什麼

[WebGLShader.js](https://github.com/mrdoob/three.js/blob/426c39aa0ea42d4612dcc3fd9e20862af6413a30/src/renderers/webgl/WebGLShader.js)

```js
function WebGLShader(gl, type, string) {
  const shader = gl.createShader(type);

  gl.shaderSource(shader, string);
  gl.compileShader(shader);

  return shader;
}

export { WebGLShader };
```

## Three.js 裡預定義的 shaders

接著可以找到在 `WebGLProgram.js` 這個檔案裡使用了 `WebGLShader` 函式，會發現傳入 `WebGLShader` 函式的 `string` 實際上是三個變數加起來的字串，因為 `vertexGlsl` 跟 `fragmentGlsl` 字串連接的邏輯看起來是類似的，下面我們就只繼續往下看 `vertexGlsl` 中的 `versionString`, `prefixVertex` 跟 `vertexShader` 這三個變數是什麼東西

[WebGLProgram.js](https://github.com/mrdoob/three.js/blob/426c39aa0ea42d4612dcc3fd9e20862af6413a30/src/renderers/webgl/WebGLProgram.js#L895)

```js
const vertexGlsl = versionString + prefixVertex + vertexShader;
const fragmentGlsl = versionString + prefixFragment + fragmentShader;

const glVertexShader = WebGLShader(gl, gl.VERTEX_SHADER, vertexGlsl);
const glFragmentShader = WebGLShader(gl, gl.FRAGMENT_SHADER, fragmentGlsl);

gl.attachShader(program, glVertexShader);
gl.attachShader(program, glFragmentShader);
```

<h3 id="versionString">1. versionString</h3>

`versionString` 標記使用的 **glsl** 版本，[parameters.glslVersion](https://threejs.org/docs/#api/en/materials/ShaderMaterial.glslVersion) 是可以藉由 **ShaderMaterial** 傳入的其中一個參數

```js
let versionString = parameters.glslVersion
  ? '#version ' + parameters.glslVersion + '\n'
  : '';
```

<h3 id="prefixVertex">2. prefixVertex</h3>

`prefixVertex` 會隨著 `parameters.isRawShaderMaterial` 的有無，串接不同的字串

首先來看 `parameters.isRawShaderMaterial = true` 的狀況，這裡定義了兩個 shader 變數 `SHADER_TYPE` 跟 `SHADER_NAME` 分別是從 **material** 來的 [type](https://threejs.org/docs/?q=material#api/en/materials/Material.type) 跟 [name](https://threejs.org/docs/?q=material#api/en/materials/Material.name)，而 [parameters.defines](https://threejs.org/docs/#api/en/materials/ShaderMaterial.defines) 則是 `ShaderMaterial` 中傳進來自定義的數值

```js
const defines = parameters.defines;
const customDefines = generateDefines(defines);

if (parameters.isRawShaderMaterial) {
  prefixVertex = [
    '#define SHADER_TYPE ' + parameters.shaderType,
    '#define SHADER_NAME ' + parameters.shaderName,

    customDefines
  ]
    .filter(filterEmptyLine)
    .join('\n');

  if (prefixVertex.length > 0) {
    prefixVertex += '\n';
  }
}
```

而另一個狀況是 `parameters.isRawShaderMaterial = false`，可以看到相比上面這邊定義了很多 `#define` 開頭的數值，全部大約有一兩百多個吧，因為太多的關係所以下面的程式碼只節錄了前面的幾個

目前為止會發現在 `isRawShaderMaterial = false` 的狀況下，Three.js 偷偷在 shader 程式碼的開頭塞了很多東西，這也是前一篇文章提出的疑問 - [THREE.RawShaderMaterial 與 THREE.ShaderMaterial 的差別](https://bcjohnblue.github.io/2024/12/06/%E4%BD%BF%E7%94%A8-three-js-%E6%93%8D%E4%BD%9C-shader-%E7%95%AB%E5%87%BA%E5%9C%8B%E6%97%97/#:~:text=THREE.RawShaderMaterial%20%E8%88%87%20THREE.ShaderMaterial%20%E7%9A%84%E5%B7%AE%E5%88%A5) ，當使用 [RawShaderMaterial](https://threejs.org/docs/#api/en/materials/RawShaderMaterial) 時 `isRawShaderMaterial` 會設成 `true`，前面就不會偷塞一堆 `#define` 的東西，但當使用 [ShaderMaterial](https://threejs.org/docs/#api/en/materials/ShaderMaterial) 時 Three.js 偷塞入的東西就是下面這一堆

```js
prefixVertex = [
  generatePrecision(parameters),

  '#define SHADER_TYPE ' + parameters.shaderType,
  '#define SHADER_NAME ' + parameters.shaderName,

  customDefines,

  parameters.extensionClipCullDistance ? '#define USE_CLIP_DISTANCE' : '',
  parameters.batching ? '#define USE_BATCHING' : '',
  parameters.batchingColor ? '#define USE_BATCHING_COLOR' : '',
  parameters.instancing ? '#define USE_INSTANCING' : '',
  parameters.instancingColor ? '#define USE_INSTANCING_COLOR' : '',
  parameters.instancingMorph ? '#define USE_INSTANCING_MORPH' : '',

  parameters.useFog && parameters.fog ? '#define USE_FOG' : '',
  parameters.useFog && parameters.fogExp2 ? '#define FOG_EXP2' : ''

  // 以下略 ...
];
```

接著讓我們來看看 Three.js 偷塞的這一堆東西會有什麼樣的影響，我們先找一個比較簡單的例子看起 `parameters.useFog && parameters.fog ? '#define USE_FOG' : '',` 這一行看起來就是開啟霧氣效果的特效，而當我們搜尋 `USE_FOG` 這個關鍵字的時候發現有四個檔案分別都用到了這個變數 - [fog_fragment.glsl.js](https://github.com/mrdoob/three.js/blob/dev/src/renderers/shaders/ShaderChunk/fog_fragment.glsl.js), [fog_pars_fragment.glsl.js](https://github.com/mrdoob/three.js/blob/dev/src/renderers/shaders/ShaderChunk/fog_pars_fragment.glsl.js), [fog_pars_vertex.glsl.js](https://github.com/mrdoob/three.js/blob/dev/src/renderers/shaders/ShaderChunk/fog_pars_vertex.glsl.js), [fog_vertex.glsl.js](https://github.com/mrdoob/three.js/blob/dev/src/renderers/shaders/ShaderChunk/fog_vertex.glsl.js)，而且這四個檔案都放在 `src/renderers/shaders/ShaderChunk` 的這個資料夾底下，看來 `ShaderChunk` 資料夾下放的就是專門處理 three.js 預定義的這些 shader 邏輯，但到底 `ShaderChunk` 資料夾底下的這些 shader 檔案是怎麼實際套用到 shader 裡的呢？

讓我們先往下看 `vertexGlsl` 變數中串接的第三個 `vertexShader` 字串是從哪裡來的後，再回頭總結講這些 `ShaderChunk` 資料夾底下的 shader 們會比較全面

[fog_fragment.glsl.js](https://github.com/mrdoob/three.js/blob/dev/src/renderers/shaders/ShaderChunk/fog_fragment.glsl.js) - 看起來就是處理 fog 的相關 shader 程式碼

```js
export default /* glsl */ `
#ifdef USE_FOG

  #ifdef FOG_EXP2

    float fogFactor = 1.0 - exp( - fogDensity * fogDensity * vFogDepth * vFogDepth );

  #else

    float fogFactor = smoothstep( fogNear, fogFar, vFogDepth );

  #endif

  gl_FragColor.rgb = mix( gl_FragColor.rgb, fogColor, fogFactor );

#endif
`;
```

### glsl 的版本程式碼轉換

另外這邊額外想講的是 `prefixVertex` 這個變數最後還做了一個 glsl 的版本程式碼轉換，看起來是把舊版本的一些關鍵字轉成新版本的 (例如：`attribute` 變成 `in`)，這邊這樣寫看起來是使用非 `RawShaderMaterial` 的 `Material` 時，一律都認為瀏覽器可以支援 WebGL 2.0，所以 shader 的關鍵字可以轉換為 `in`, `out` 這些 glsl 較新的文法

推測是因為 WebGL 2.0 2016 年就推出了，現在瀏覽器的支援率也達到 [96% 以上](https://caniuse.com/webgl2)，所以預設的 `Material` 都直接使用 WebGL 2.0 文法就好，而需要兼容舊 glsl (WebGL 1.0) 的人就需要使用 `RawShaderMaterial` 撰寫自己的 shader 並設定 [glslVersion](https://threejs.org/docs/#api/en/materials/ShaderMaterial.glslVersion)

關於 glsl 程式碼關鍵字新舊版本的差異，請看 [OpenGL ES 与 GLSL ES 版本的对应关系](https://shaderfans.com/post/ban-ben-shu-li/index.html) 及 [OpenGL 和 GLSL 版本更迭](https://www.cnblogs.com/George1994/p/6418013.html)

```js
if (parameters.isRawShaderMaterial !== true) {
  // GLSL 3.0 conversion for built-in materials and ShaderMaterial

  versionString = '#version 300 es\n';

  prefixVertex =
    [
      customVertexExtensions,
      '#define attribute in',
      '#define varying out',
      '#define texture2D texture'
    ].join('\n') +
    '\n' +
    prefixVertex;

  // ... 下略
}
```

<h3 id="vertexShader">3. vertexShader</h3>

```js
let vertexShader = parameters.vertexShader;

vertexShader = resolveIncludes(vertexShader);
vertexShader = replaceLightNums(vertexShader, parameters);
vertexShader = replaceClippingPlaneNums(vertexShader, parameters);

vertexShader = unrollLoops(vertexShader);
```

來到最後 `vertexShader` 的部分了，首先這裡最重要的函式是 `resolveIncludes`，這會講的比較冗長，所以我們先把其他三個函式看完後再回頭來看 `resolveIncludes` 的作用

`replaceLightNums`, `replaceClippingPlaneNums` 及 `unrollLoops` 這三個函式其實做的事情都類似，把接進來 `vertexShader` 的特定字串替換成 `parameters` 裡的數值就這樣，以下只節錄 `replaceLightNums` 函式部分讓大家有點感覺

```js
function replaceLightNums(string, parameters) {
  const numSpotLightCoords =
    parameters.numSpotLightShadows +
    parameters.numSpotLightMaps -
    parameters.numSpotLightShadowsWithMaps;

  return string
    .replace(/NUM_DIR_LIGHTS/g, parameters.numDirLights)
    .replace(/NUM_SPOT_LIGHTS/g, parameters.numSpotLights)
    .replace(/NUM_SPOT_LIGHT_MAPS/g, parameters.numSpotLightMaps);
  // ... 下略
}
```

接著的 `resolveIncludes` 就是一切關鍵的鑰匙了，這個函式串連了我們上面所有講的部分，以及也解釋了 Three.js 這些預定義的 `#define` 字串是如何變成實際的 shader 程式碼而做出類似霧氣這些效果的，其實 `resolveIncludes` 做的事情意外的簡單，實際上就是在傳進來的 `vertexShader` 這一長串的字串中，找到 `#include` 開頭的字串，並替換成 `ShaderChunk` 資料夾底下各檔案實際撰寫的 shader 程式碼

```js
// Resolve Includes

const includePattern = /^[ \t]*#include +<([\w\d./]+)>/gm;

function resolveIncludes(string) {
  return string.replace(includePattern, includeReplacer);
}

const shaderChunkMap = new Map();

function includeReplacer(match, include) {
  let string = ShaderChunk[include];

  if (string === undefined) {
    const newInclude = shaderChunkMap.get(include);

    if (newInclude !== undefined) {
      string = ShaderChunk[newInclude];
      console.warn(
        'THREE.WebGLRenderer: Shader chunk "%s" has been deprecated. Use "%s" instead.',
        include,
        newInclude
      );
    } else {
      throw new Error('Can not resolve #include <' + include + '>');
    }
  }

  return resolveIncludes(string);
}
```

這麼講好像還是有點模糊，讓我們舉一個實際的例子就會很簡單了，我們以 [MeshBasicMaterial](https://threejs.org/docs/?q=basic#api/en/materials/MeshBasicMaterial) 當作範例解釋：

在 [WebGLPrograms.js](https://github.com/mrdoob/three.js/blob/dev/src/renderers/webgl/WebGLPrograms.js) 裡，注意！這個檔案是 **"s"** 結尾的，跟上面最一開始提到的 `WebGLProgram.js` 檔案是處理完全不一樣的東西，在 `WebGLPrograms.js` 這個檔案裡會根據不同的 `Material` 種類產出 `parameter.vertexShader` 而這個 `parameter.vertexShader` 變數會傳到 `WebGLProgram.js` 去，對應到的就是一開始提到的 `vertexGlsl` 變數中的第三個變數 `vertexShader`

[WebGLPrograms.js](https://github.com/mrdoob/three.js/blob/dev/src/renderers/webgl/WebGLPrograms.js)

```js
import { ShaderLib } from '../shaders/ShaderLib.js';

const shaderIDs = {
  MeshBasicMaterial: 'basic'
  // ...
};

function getParameters(material, lights, shadows, scene, object) {
  const shaderID = shaderIDs[material.type];

  if (shaderID) {
    const shader = ShaderLib[shaderID];

    vertexShader = shader.vertexShader;
    fragmentShader = shader.fragmentShader;
  }

  const parameters = {
    vertexShader,
    fragmentShader
    // ...
  };
}
```

[ShaderLib.js](https://github.com/mrdoob/three.js/blob/dev/src/renderers/shaders/ShaderLib.js)

```js
import { ShaderChunk } from './ShaderChunk.js';

const ShaderLib = {
  basic: {
    uniforms: /*@__PURE__*/ mergeUniforms([
      UniformsLib.common,
      UniformsLib.specularmap,
      UniformsLib.envmap,
      UniformsLib.aomap,
      UniformsLib.lightmap,
      UniformsLib.fog
    ]),

    vertexShader: ShaderChunk.meshbasic_vert,
    fragmentShader: ShaderChunk.meshbasic_frag
  }
};
```

[ShaderChunk.js](https://github.com/mrdoob/three.js/blob/dev/src/renderers/shaders/ShaderChunk.js)

```js
import * as meshbasic from './ShaderLib/meshbasic.glsl.js';

export const ShaderChunk = {
  meshbasic_vert: meshbasic.vertex,
  meshbasic_frag: meshbasic.fragment
  // ...
};
```

所有 Three.js 定義的 `Material` 都有自己的一個 shader 模板，為什麼我稱它叫做 shader 模板呢？因為它有一堆字串，並且這些字串包含著許多 `#include ...` 的關鍵字，每一行的 `#include ...` 之後都會被替換成可以實際執行 shader 的程式碼，以 `MeshBasicMaterial` 來說在 `WebGLPrograms.js` 檔案中 `getParameters` 函式傳出來的 `vertexShader` 及 `fragmentShader`，其來源的 shader 模板就是 `./ShaderLib/meshbasic.glsl.js`

- `vertexShader` 實際的值就是 `meshbasic.glsl.js` 匯出的 `vertex`
- `fragmentShader` 實際的值就是 `meshbasic.glsl.js` 匯出的 `fragment`

[meshbasic.glsl.js](https://github.com/mrdoob/three.js/blob/dev/src/renderers/shaders/ShaderLib/meshbasic.glsl.js)

```js
export const vertex = /* glsl */ `
#include <common>
#include <batching_pars_vertex>
#include <uv_pars_vertex>
#include <envmap_pars_vertex>
#include <color_pars_vertex>
#include <fog_pars_vertex>
#include <morphtarget_pars_vertex>
#include <skinning_pars_vertex>
#include <logdepthbuf_pars_vertex>
#include <clipping_planes_pars_vertex>

void main() {

  #include <uv_vertex>
  #include <color_vertex>
  #include <morphinstance_vertex>
  #include <morphcolor_vertex>
  #include <batching_vertex>
  // ...
}
`;

export const fragment = /* glsl */ `
uniform vec3 diffuse;
uniform float opacity;

#ifndef FLAT_SHADED

  varying vec3 vNormal;

#endif

void main() {

  vec4 diffuseColor = vec4( diffuse, opacity );
  #include <clipping_planes_fragment>

  #include <logdepthbuf_fragment>
  #include <map_fragment>
  #include <color_fragment>
  #include <alphamap_fragment>
  #include <alphatest_fragment>
  #include <alphahash_fragment>
  #include <specularmap_fragment>
  // ...
`;
```

所以讓我們來看看以第 ７ 行 `#include <fog_pars_vertex>` 來說，這一行字串最後會藉由 `resolveIncludes` 這個函式，將 `#include` 後面的 `<fog_pars_vertex>` 字串挑出並替換成 `ShaderChunk` 資料夾底下同名的檔案 `fog_pars_vertex.glsl.js` 引入真正的 shader 程式碼

[fog_pars_vertex.glsl.js](https://github.com/mrdoob/three.js/blob/dev/src/renderers/shaders/ShaderChunk/fog_pars_vertex.glsl.js)

```js
export default /* glsl */ `
#ifdef USE_FOG

  varying float vFogDepth;

#endif
`;
```

### 小結

到此我們終於搞清楚了 Three.js 裡整個 shader 的架構，首先是判斷是不是使用 `RawShaderMaterial`，如果是的話，就代表整個 shader 的程式碼使用者自己輸入，而如果是使用 `ShaderMaterial` 的話，Three.js 會幫你引用一堆預定義好的 shader 功能，例如: fog，而這整個套用預定義 shader 的流程會從每個各自的 Material shader 模板出發 (ex. `meshbasic.glsl.js`)，這個 shader 模板裡面寫有一堆 `#include ...` 開頭的字串，之後藉由 `resolveIncludes` 函式解析出 `#include` 後的字串是代表要開啟什麼樣的 shader 效果 (ex. `#include <fog_pars_vertex>`)，並引用 `ShaderChunk` 資料夾底下相對應的 shader 檔案替換成真正的 shader 程式碼 (ex. `fog_pars_vertex.glsl.js`)

**P.S. fog shader 廢除**
這裡我們用 fog 當作範例來講解 shader 的架構，但在看原始碼時我發現 fog 的 shader 使用方式應該是已經廢除了，原因是下面這一部分的程式會判斷 `parameters.useFog` 為 `true` 時才開啟 `USE_FOG` 的功能，但可以發現 `parameters.useFog = material.fog === true`, 而在官方的 [Material](https://threejs.org/docs/?q=Material#api/en/materials/Material) 文件裡已經沒有 `fog` 的屬性了，看樣子 `fog` 之類的功能已經額外移出來到 [Fog
](https://threejs.org/docs/?q=fog#api/en/scenes/Fog) 跟 [FogExp2](https://threejs.org/docs/#api/en/scenes/FogExp2) 了

[WebGLProgram.js](https://github.com/mrdoob/three.js/blob/dev/src/renderers/webgl/WebGLProgram.js)

```js
prefixVertex = [
  parameters.useFog && parameters.fog ? '#define USE_FOG' : '',
  parameters.useFog && parameters.fogExp2 ? '#define FOG_EXP2' : ''

  // 以下略 ...
];
```

**P.S. ShaderLib 與 ShaderChunk 資料夾**
藉由以上的討論可以知道 `ShaderLib` 底下放的就是所謂的 shader 模板 (許多 `#include ...` 開頭的字串)，而 `ShaderChunk` 底下則是放實際可以執行 shader 的程式碼 (ex. `fog_pars_vertex.glsl.js`)，這其實對應的就是官方文件的 [ShaderChunk](https://threejs.org/docs/#api/en/renderers/shaders/ShaderChunk) 與 [ShaderLib](https://threejs.org/docs/#api/en/renderers/shaders/ShaderLib) 頁面

---

## 改變 Three.js 內建的 shader 程式碼

上面我們搞清楚了整個 Three.js 中 shader 的架構，下面想要分享的是開發者可以透過 [Material.onBeforeCompile](https://threejs.org/docs/#api/en/materials/Material.onBeforeCompile) 這個函式塞入自己寫的 shader 邏輯，下面我們直接拿 Three.js 官方的範例 - [webgl_materials_modified](https://threejs.org/examples/#webgl_materials_modified) 來做例子

首先在這個範例中使用 `GLTFLoader` 載入了一個叫做 `LeePerrySmith.glb` 的 3D 模型並創建了兩個人臉的 mesh，兩個 mesh 所使用的 material 是藉由 `buildTwistMaterial` 這個函式產生出來的

```js
const loader = new GLTFLoader();
loader.load('models/gltf/LeePerrySmith/LeePerrySmith.glb', function (gltf) {
  const geometry = gltf.scene.children[0].geometry;

  let mesh = new THREE.Mesh(geometry, buildTwistMaterial(2.0));
  mesh.position.x = -3.5;
  mesh.position.y = -0.5;
  scene.add(mesh);

  mesh = new THREE.Mesh(geometry, buildTwistMaterial(-2.0));
  mesh.position.x = 3.5;
  mesh.position.y = -0.5;
  scene.add(mesh);
});
```

`buildTwistMaterial` 中使用了 `Material.onBeforeCompile` 更改 Three.js 內置的 shader 達到人臉左右扭曲的效果

```js
function buildTwistMaterial(amount) {
  const material = new THREE.MeshNormalMaterial();
  material.onBeforeCompile = function (shader) {
    shader.uniforms.time = { value: 0 };

    shader.vertexShader = 'uniform float time;\n' + shader.vertexShader;
    shader.vertexShader = shader.vertexShader.replace(
      '#include <begin_vertex>',
      [
        `float theta = sin( time + position.y ) / ${amount.toFixed(1)};`,
        'float c = cos( theta );',
        'float s = sin( theta );',
        'mat3 m = mat3( c, 0, s, 0, 1, 0, -s, 0, c );',
        'vec3 transformed = vec3( position ) * m;',
        'vNormal = vNormal * m;'
      ].join('\n')
    );

    material.userData.shader = shader;
  };

  // Make sure WebGLRenderer doesn't reuse a single program

  material.customProgramCacheKey = function () {
    return amount.toFixed(1);
  };

  return material;
}
```

首先來看第 3 行 `onBeforeCompile` 中取得的 `shader` 是什麼？可以從 three.js 的原始碼中找到在 [WebGLRenderer.js](https://github.com/mrdoob/three.js/blob/e0b7bd9ca547889d6a57dfcd32596317f2ccf28c/src/renderers/WebGLRenderer.js#L1693) 中呼叫了 `material.onBeforeCompile` 並把 `parameters` 傳進去

[WebGLRenderer.js](https://github.com/mrdoob/three.js/blob/e0b7bd9ca547889d6a57dfcd32596317f2ccf28c/src/renderers/WebGLRenderer.js#L1693)

```js
parameters.uniforms = programCache.getUniforms(material);

material.onBeforeCompile(parameters, _this);

program = programCache.acquireProgram(parameters, programCacheKey);
programs.set(programCacheKey, program);
```

接著會發現 `parameters` 是從 `programCache.getParameters` 來的，而 `programCache` 是 [WebGLPrograms.js](https://github.com/mrdoob/three.js/blob/7b4b9ff3f75ac97054b0f9e60bb5fe17815fde8d/src/renderers/webgl/WebGLPrograms.js#L9) 產生出來的實例

[WebGLRenderer.js](https://github.com/mrdoob/three.js/blob/e0b7bd9ca547889d6a57dfcd32596317f2ccf28c/src/renderers/WebGLRenderer.js#L1652)

```js
programCache = new WebGLPrograms(
  _this,
  cubemaps,
  cubeuvmaps,
  extensions,
  capabilities,
  bindingStates,
  clipping
);

const parameters = programCache.getParameters(
  material,
  lights.state,
  shadowsArray,
  scene,
  object
);
const programCacheKey = programCache.getProgramCacheKey(parameters);
```

最終在 `WebGLPrograms.js` 檔案裡終於找到了定義 `parameters` 變數的地方，看到這裡有沒有覺得熟悉了起來，這裡的 `vertexShader` 跟 `fragmentShader`，這兩個東西就是剛剛上面在探討的 <a href="#vertexShader">3. vertexShader</a>，所以這裡的 `vertexShader` 跟 `fragmentShader` 都是從 `ShaderLib` 資料夾底下的 shader 模板來的

[WebGLPrograms.js](https://github.com/mrdoob/three.js/blob/7b4b9ff3f75ac97054b0f9e60bb5fe17815fde8d/src/renderers/webgl/WebGLPrograms.js#L177)

```js
if (shaderID) {
  const shader = ShaderLib[shaderID];

  vertexShader = shader.vertexShader;
  fragmentShader = shader.fragmentShader;
}

const parameters = {
  shaderID: shaderID,
  shaderType: material.type,
  shaderName: material.name,

  vertexShader: vertexShader,
  fragmentShader: fragmentShader,
  defines: material.defines
  // ... 下略
};
```

回到上面 `buildTwistMaterial` 函式中用到的是 [MeshNormalMaterial](https://threejs.org/docs/#api/en/materials/MeshNormalMaterial)，對應到的 shader 模板是 `ShaderLib` 資料夾底下的 `meshnormal.glsl.js`

[meshnormal.glsl.js](https://github.com/mrdoob/three.js/blob/7b4b9ff3f75ac97054b0f9e60bb5fe17815fde8d/src/renderers/shaders/ShaderLib/meshnormal.glsl.js)

```js
void main() {

  #include <uv_vertex>
  #include <batching_vertex>

  #include <beginnormal_vertex>
  #include <morphinstance_vertex>
  #include <morphnormal_vertex>
  #include <skinbase_vertex>
  #include <skinnormal_vertex>
  #include <defaultnormal_vertex>
  #include <normal_vertex>

  #include <begin_vertex>
  #include <morphtarget_vertex>
  #include <skinning_vertex>
  #include <displacementmap_vertex>
  #include <project_vertex>
  #include <logdepthbuf_vertex>
  #include <clipping_planes_vertex>

  // ... 下略
}
```

所以在 `buildTwistMaterial` 函式中是將第 14 行的 `#include <begin_vertex>` 這一行程式碼替換為自己所撰寫的 shader，關鍵的一行是 `mat3 m = mat3( c, 0, s, 0, 1, 0, -s, 0, c );` 這裡創建了一個繞 Y 軸的旋轉矩陣，將原本的 vertex 位置 `vec3( position )` 及法線向量 `vNormal` 都乘上這個旋轉矩陣，做到模型圍繞著 Y 軸進行旋轉的效果

至於為什麼 `mat3( c, 0, s, 0, 1, 0, -s, 0, c )` 是圍繞著 Y 軸的旋轉矩陣呢？請參考這兩篇文章 [旋轉矩陣](https://www.cnblogs.com/MetaWang/p/10094784.html) 以及 [变换](https://learnopengl-cn.readthedocs.io/zh/latest/01%20Getting%20started/07%20Transformations/)

`buildTwistMaterial` 函式

```js
material.onBeforeCompile = function (shader) {
  shader.uniforms.time = { value: 0 };

  shader.vertexShader = 'uniform float time;\n' + shader.vertexShader;
  shader.vertexShader = shader.vertexShader.replace(
    '#include <begin_vertex>',
    [
      `float theta = sin( time + position.y ) / ${amount.toFixed(1)};`,
      'float c = cos( theta );',
      'float s = sin( theta );',
      'mat3 m = mat3( c, 0, s, 0, 1, 0, -s, 0, c );',
      'vec3 transformed = vec3( position ) * m;',
      'vNormal = vNormal * m;'
    ].join('\n')
  );

  material.userData.shader = shader;
};
```

另外還有一個問題是為什麼最後改變的變數名稱是 `transformed` 跟 `vNormal` 呢？原因是要看後面套用的 shader 程式碼，`transformed` 這個變數之後會在 `project_vertex.glsl.js` 裡最終傳遞給 `gl_Position`，而 `vNormal` 則是會在 `normal_fragment_begin.glsl.js` 檔案中轉換成 `normal`，`normal` 最終會在 `meshnormal.glsl.js` 檔案中傳給 `gl_FragColor` 決定每個像素點的顏色

[project_vertex.glsl.js](https://github.com/mrdoob/three.js/blob/7b4b9ff3f75ac97054b0f9e60bb5fe17815fde8d/src/renderers/shaders/ShaderChunk/project_vertex.glsl.js)

```js
export default /* glsl */ `
vec4 mvPosition = vec4( transformed, 1.0 );

#ifdef USE_BATCHING

  mvPosition = batchingMatrix * mvPosition;

#endif

#ifdef USE_INSTANCING

  mvPosition = instanceMatrix * mvPosition;

#endif

mvPosition = modelViewMatrix * mvPosition;

gl_Position = projectionMatrix * mvPosition;
`;
```

[normal_fragment_begin.glsl.js](https://github.com/mrdoob/three.js/blob/7b4b9ff3f75ac97054b0f9e60bb5fe17815fde8d/src/renderers/shaders/ShaderChunk/normal_fragment_begin.glsl.js)

```js
#ifdef FLAT_SHADED

  vec3 fdx = dFdx( vViewPosition );
  vec3 fdy = dFdy( vViewPosition );
  vec3 normal = normalize( cross( fdx, fdy ) );

#else

  vec3 normal = normalize( vNormal );

  #ifdef DOUBLE_SIDED

    normal *= faceDirection;

  #endif

#endif
```

[meshnormal.glsl.js](https://github.com/mrdoob/three.js/blob/7b4b9ff3f75ac97054b0f9e60bb5fe17815fde8d/src/renderers/shaders/ShaderLib/meshnormal.glsl.js)

```js
export const fragment = /* glsl */ `
  void main() {
    gl_FragColor = vec4( packNormalToRGB( normal ), diffuseColor.a );
  }
`;
```

### customProgramCacheKey

最後的最後還有個小問題，不知道大家有沒有注意到除了 `onBeforeCompile`，另外還定義了一個 [customProgramCacheKey](https://threejs.org/docs/#api/en/materials/Material)，這似乎是因為 Three.js 會覆用一個叫做 `program` 的變數，而每個 `program` 的變數是用一個叫做 `programCacheKey` 去記錄這個 `program` 唯一的 key 值

`buildTwistMaterial` 函式

```js
// Make sure WebGLRenderer doesn't reuse a single program

material.customProgramCacheKey = function () {
  return amount.toFixed(1);
};
```

[WebGLRenderer.js](https://github.com/mrdoob/three.js/blob/7b4b9ff3f75ac97054b0f9e60bb5fe17815fde8d/src/renderers/WebGLRenderer.js#L1653)

```js
const programCacheKey = programCache.getProgramCacheKey(parameters);
```

[WebGLPrograms](https://github.com/mrdoob/three.js/blob/7b4b9ff3f75ac97054b0f9e60bb5fe17815fde8d/src/renderers/webgl/WebGLPrograms.js#L377)

```js
function getProgramCacheKey(parameters) {
  // ... 上略
  array.push(parameters.customProgramCacheKey);

  return array.join();
}
```

而預設的 `Material.customProgramCacheKey()` 只會將 `onBeforeCompile` 函數整個 `toString()` 當作唯一的 cacheKey

[Material.js](https://github.com/mrdoob/three.js/blob/7b4b9ff3f75ac97054b0f9e60bb5fe17815fde8d/src/materials/Material.js#L109)

```js
customProgramCacheKey() {
  return this.onBeforeCompile.toString();
}
```

所以在 `buildTwistMaterial` 函式才需要根據 `amount` 的值去定義不同的 cacheKey，如果沒有自定義 `material.customProgramCacheKey` 這一段的話，兩個人臉 mesh 因為 `onBeforeCompile` 的函數一樣就會變成他們的 `programCacheKey` 也都相同了

最後關於這個範例，我也照樣複製了一個 [線上版本](https://codesandbox.io/p/github/bcjohnblue/threejs-onBeforeCompile-example/main) 出來，有興趣的人可以去更改自定義的 shader 讓整個變形的方向改成是圍繞 x 軸或是 z 軸旋轉

## 版本

這篇研究 shader 架構採用的版本是 three.js r173

## 參考資料

[通过方法 onBeforeCompile 为 three.js 现有材质增加功能](https://juejin.cn/post/6850418117509414925)
[threejs 材质的 shader 代码](http://www.webgl3d.cn/pages/b7b8ff/)
