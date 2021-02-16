---
title: openGL搭建
date: 2020-10-25 21:52:50
tags:
    - opengl|webgl
---

# openGL搭建

本文主要讲解在Mac上搭建opengl开发环境，opengl开发主要要用到GLFW、GLAD、SOIL、GLM、ASSIMP这几个库，下面我会一一讲述这几个库在Mac上的环境配置。

<!--more-->

### GLFW

### GLAD

~~这个位置可以写一下GLAD这个库的作用~~

[//]: # (这个位置可以写一下GLAD这个库的作用)

1. 打开GLAD的在线服务。

2. 将语言设置为 C/C++，在 API 选项中，选择 3.3 及以上的 OpenGL 版本。

3. 将模式 Profile 设置为 Core ，保证生成加载器 Generate a loader 选项是选中的。

4. 先暂时忽略拓展 Extensions 中内容。

5. 点击生成 Generate 。

6. 下载生成的 zip 包（包含 glad.c、glad.h 和 khrplatform.h），解压后 include 目录下的 glad 和 KHR 文件夹复制到 /usr/local/include，glad.c 添加到项目中。

**注意： glad 头文件 必须在包含 glfw头文件之前，否则回报错：OpenGL header already included, remove this include, glad already provides it，而且不能与 glew 共同使用，否则也会报错：gl.h included before glew.h（glad头文件在glew头文件之前） 或 OpenGL header already included, remove this include, glad already provides it（glad头文件在glew头文件之后）**

### GLM

GLM是OpenGL Mathematics的缩写，它是一个只有头文件的库，也就是说我们只需包含对应的头文件就行了，不用链接和编译。GLM可以在它们的网站上下载。把头文件的根目录复制到你的includes文件夹，然后你就可以使用这个库了。

### SOIL

大多数图形学处理的任务都需要读取图片到内存当中。当前存在着各种各样的图片格式，例如 jpeg, bmp, png 等等；SOIL 提供了将图片加载到内存当中的功能，避免了因繁杂的图片格式读取问题。

1. 首先下载(http://www.lonesock.net/soil.html)[SOIL]安装包，一般选择最新版本就可以，下载下来后解压；

2. 然后在//Simple OpenGL Image Library/projects/makefile目录下新建一个名为obj的空文件夹;

3. 其次需要更改/Simple OpenGL Image Library/projects/makefile目录下的makefile文件，这是由于库文件默认是32位的，而mac上的执行环境是64位的，因此要做如下修改：

    `CXXFLAGS = -O2 -s -Wall -m64`

4. 最后依次执行make clean、make、make install。

此时会发现头文件被放到了 usr/local/include 文件夹下，库文件放在了 usr/local/lib 目录下，引用即可。

### Assimp

Assimp 能够导入很多种不同的模型文件格式（并也能够导出部分的格式），并将所有的模型数据加载至 Assimp 的通用数据结构中。

Assimp的安装需要用到CMake GUI，请提前安装好，此处没有难点，正常安装就可以

1. 首先下载(http://www.assimp.org/)[Assimp]，并解压；
2. 打开CMake gui，选择源代码目录和目标文件目录（自己创建一个就可以）
3. 点击 Configure(设置) 按钮，让 CMake 读取设置和源代码，然后选择工程的生成器 Unix Makefiles。
4. CMake 会显示可选的编译选项用来配置最终生成的库。这里使用默认设置，并再次点击Configure(设置)按钮保存设置。
5. 保存之后，点击 Generate(生成) 按钮，生成的工程文件会在 Build 文件夹中。
6. 打开终端，cd 进入 Assimp_Build 目录, 执行 make 命令进行编译安装库文件。执行 make install 命令进行安装，将会安装到 /usr/local/include 和 /usr/local/lib 中。

#### 建议

建议安装Assimp的新版本，本人用3.3.1版本make编译时发生错误，换了最新版本后就没有此问题；

#### 总结

以上五个库基本可以涵盖opengl学习的所有第三方库，当安装完以上五个库后，你就可以开启你的opengl学习旅程了。

#### 参考

(https://learnopengl-cn.readthedocs.io/zh/latest/)[LearnOpenGL-CN]
(https://www.cnblogs.com/RDaneelOlivaw/p/6839366.html)[MAC OS下安装SOIL]
(http://blog.shenyuanluo.com/LearnOpenGLNote18.html)[Assimp]
(http://blog.shenyuanluo.com/OpenGLEnvironment.html)[OpenGL学习笔记（二）—— (macOS)