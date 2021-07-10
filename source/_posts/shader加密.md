---
title: shader加密
date: 2021-07-11 00:00:52
tags:
---

# shader加密

## 为什么要做shader加密

正常情况下，在部署到线上环境的时候，会对代码进行压缩与混淆，从而达到代码加密的效果。但是由于webgl shader代码是写在字符串中的，在部署到线上环境的时候，对这部分代码会被当做普通字符串来处理，只会对其进行压缩而不会混淆。这种情况会导致在webgl shader中写的算法代码存在泄密风险。因此需要在开发过程中对这部分代码进行加密，在传给GPU的时候再进行解密，从而保证代码的私密性。

<!--more-->

## 怎么做shader加密

加密的手段总体上就像上文所说的，在开发过程中对shader代码进行编码，在传给GPU之前再对shader代码进行解码。

编码的手段是通过将shader字符串中的每一个字符的Unicode码向右平移，然后根据平移后的Unicode码求出新的字符，再拼成“shader字符串”。

解码的手段是编码的逆过程，首先拿到编码后的“shader字符串”，然后将每一个字符的Unicode码向左平移，还原“shader字符串”。

通过这个过程，既保证了在代码层面，只能看到编码后的shader代码，又能保证传给GPU可用的shader代码。

具体代码

```js
const fs = require('fs')

const shaderFiles = {
    'fragmentShader': './_fragmentShader.glsl',
    'vertexShader': './_vertexShader.glsl',
    'depthFragmentShader': './_depthFragmentShader.glsl',
    'depthVertexShader': './_depthVertexShader.glsl',
}

for (let name in shaderFiles) {
    let code = fs.readFileSync(shaderFiles[name], 'utf8')
    // code = code.replace(/\n+/g, '').replace(/\s+/g, ' ')
    fs.writeFileSync(`./${name}.js`, codeTransform(code))
}

function codeTransform (code) {
    const defsFunctionString = defs.toString()
    const content = JSON.stringify(obfs(code))
    return `export default (${defsFunctionString}(\n${content}\n))`
}

function obfs () {
    const key = 13, n = 126, o = 0, spliter = ''
    return arguments[o].split(spliter).map(
        char => char.charCodeAt(o) > n ?
        char : String.fromCharCode((char.charCodeAt(o) + key) % n)
    ).join(spliter)
}

function defs () {
    const key = 113, n = 126, o = 0, spliter = ''
    return arguments[o].split(spliter).map(
        char => char.charCodeAt(o) > n ?
        char : String.fromCharCode((char.charCodeAt(o) + key) % n)
    ).join(spliter)
}
```

代码编码前后对比

代码编码前

```js
varying vec3 vNormal;
varying vec3 vViewPosition;

uniform vec3 pano0Position;
uniform mat4 pano0Matrix;
uniform vec3 pano1Position;
uniform mat4 pano1Matrix;

varying vec3 pano0WorldPosition;
varying vec3 pano1WorldPosition;

#include <common>
#include <uv_pars_vertex>
#include <uv2_pars_vertex>
#include <color_pars_vertex>

void main() {

#include <uv_vertex>
#include <uv2_vertex>
#include <color_vertex>
#include <beginnormal_vertex>
#include <defaultnormal_vertex>

vec3 transformed = vec3(position);
vec4 mvPosition = modelViewMatrix * vec4(transformed, 1.0);
vNormal = normalize(transformedNormal);
vViewPosition = - mvPosition.xyz;

vec4 worldPosition = modelMatrix * vec4(position, 1.0);

vec3 vector0 = worldPosition.xyz - pano0Position;
pano0WorldPosition = (vec4(vector0, 1.0) * pano0Matrix).xyz;

vec3 vector1 = worldPosition.xyz - pano1Position;
pano1WorldPosition = (vec4(vector1, 1.0) * pano1Matrix).xyz;

gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
}
```

代码编码后

```js

export default (function defs () {
const key = 113, n = 126, o = 0, spliter = ''
return arguments[o].split(spliter).map(
char => char.charCodeAt(o) > n ?
char : String.fromCharCode((char.charCodeAt(o) + key) % n)
).join(spliter)
}(
"\u0005n\u0001\bv{t-\u0005rp@-\u0005[|\u0001znyH\u0017\u0005n\u0001\bv{t-\u0005rp@-\u0005cvr\u0006]|\u0002v\u0003v|{H\u0017\u0017\u0004{vs|\u0001z-\u0005rp@-}n{|=]|\u0002v\u0003v|{H\u0017\u0004{vs|\u0001z-zn\u0003A-}n{|=Zn\u0003\u0001v\u0007H\u0017\u0004{vs|\u0001z-\u0005rp@-}n{|>]|\u0002v\u0003v|{H\u0017\u0004{vs|\u0001z-zn\u0003A-}n{|>Zn\u0003\u0001v\u0007H\u0017\u0017\u0005n\u0001\bv{t-\u0005rp@-}n{|=d|\u0001yq]|\u0002v\u0003v|{H\u0017\u0005n\u0001\bv{t-\u0005rp@-}n{|>d|\u0001yq]|\u0002v\u0003v|{H\u0017\u00170v{py\u0004qr-Ip|zz|{K\u00170v{py\u0004qr-I\u0004\u0005l}n\u0001\u0002l\u0005r\u0001\u0003r\u0007K\u00170v{py\u0004qr-I\u0004\u0005?l}n\u0001\u0002l\u0005r\u0001\u0003r\u0007K\u00170v{py\u0004qr-Ip|y|\u0001l}n\u0001\u0002l\u0005r\u0001\u0003r\u0007K\u0017\u0017\u0005|vq-znv{56-\n\u0017\u0017--0v{py\u0004qr-I\u0004\u0005l\u0005r\u0001\u0003r\u0007K\u0017--0v{py\u0004qr-I\u0004\u0005?l\u0005r\u0001\u0003r\u0007K\u0017--0v{py\u0004qr-Ip|y|\u0001l\u0005r\u0001\u0003r\u0007K\u0017--0v{py\u0004qr-Iortv{{|\u0001znyl\u0005r\u0001\u0003r\u0007K\u0017--0v{py\u0004qr-Iqrsn\u0004y\u0003{|\u0001znyl\u0005r\u0001\u0003r\u0007K\u0017\u0017--\u0005rp@-\u0003\u0001n{\u0002s|\u0001zrq-J-\u0005rp@5}|\u0002v\u0003v|{6H\u0017--\u0005rpA-z\u0005]|\u0002v\u0003v|{-J-z|qrycvr\u0006Zn\u0003\u0001v\u0007-7-\u0005rpA5\u0003\u0001n{\u0002s|\u0001zrq9->;=6H\u0017--\u0005[|\u0001zny-J-{|\u0001znyv\tr5\u0003\u0001n{\u0002s|\u0001zrq[|\u0001zny6H\u0017--\u0005cvr\u0006]|\u0002v\u0003v|{-J-:-z\u0005]|\u0002v\u0003v|{;\u0007\b\tH\u0017\u0017--\u0005rpA-\u0006|\u0001yq]|\u0002v\u0003v|{-J-z|qryZn\u0003\u0001v\u0007-7-\u0005rpA5}|\u0002v\u0003v|{9->;=6H\u0017\u0017--\u0005rp@-\u0005rp\u0003|\u0001=-J-\u0006|\u0001yq]|\u0002v\u0003v|{;\u0007\b\t-:-}n{|=]|\u0002v\u0003v|{H\u0017--}n{|=d|\u0001yq]|\u0002v\u0003v|{-J-5\u0005rpA5\u0005rp\u0003|\u0001=9->;=6-7-}n{|=Zn\u0003\u0001v\u00076;\u0007\b\tH\u0017\u0017--\u0005rp@-\u0005rp\u0003|\u0001>-J-\u0006|\u0001yq]|\u0002v\u0003v|{;\u0007\b\t-:-}n{|>]|\u0002v\u0003v|{H\u0017--}n{|>d|\u0001yq]|\u0002v\u0003v|{-J-5\u0005rpA5\u0005rp\u0003|\u0001>9->;=6-7-}n{|>Zn\u0003\u0001v\u00076;\u0007\b\tH\u0017\u0017--tyl]|\u0002v\u0003v|{-J-}\u0001|wrp\u0003v|{Zn\u0003\u0001v\u0007-7-z|qrycvr\u0006Zn\u0003\u0001v\u0007-7-\u0005rpA5}|\u0002v\u0003v|{9->;=6H\u0017\f"
))
```