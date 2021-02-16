---
title: webgl编程顺序
date: 2020-10-23 18:46:54
tags:
    -opengl|webgl
---

# webgl编程顺序

1. 准备容器，在HTML5中，使用的是Canvas作为容器；

2. 初始化上下文， `gl = canvas.getContext("webgl") || canvas.getContext("experimental-webgl");`;

3. 初始化着色器，即构建shader和program；

4. 生成加载模型数据，即给vertex和uniform传数据；

5. 渲染，调用`gl.drawArrays()` 或者 `gl.drawElements()`;
