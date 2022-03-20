---
title: npm版本控制
date: 2022-03-20 18:15:50
tags:
    - node
---

## 为什么需要NPM包版本规范

首先，由于目前项目组没有一套明确的版本管理规范来对node package进行规范，每个人对版本号的理解差异导致目前导致目前package版本号混乱，且无法整理。

<!--more-->

其次，由于版本号的混乱，在应用层使用时，经常出现使用了错误的版本，而导致的线上问题。

为了解决这些问题，必须给出一套明确的npm package发版规范，来指导每一个版本包的发版。

## NPM版本规范

node package版本号由四部分组成：major.minor.patch[-prerelease]，比如1.0.2-beta.1，其中prerelease可选。

* major：代表主版本号，通常需要提交不能向下兼容的情况下对该版本号进行升级
* minor：代表次版本号，通常在新增功能时才对该版本号进行升级
* patch：代表修复版本号，升级该版本号通常代表修复一些bug，但没有新增功能或者存在不向下兼容的功能
* prerelease：带有该版本号的包通常表示在测试阶段，尚未稳定，通常不建议用户安装

prerelease说明

通常我们会看到三种类型的prerelease，分别是alpha、beta、RC；

* alpha：代表内部测试版本，会有很多bug，是比beta更早的版本，一般不建议对外发布；
* beta：相对alpha版已有了很大的赶紧，但还是存在一些缺陷，需要经过多次测试来进一步消除；
* rc：正式发布的候选版本。与beta版本最大的差别在于beta阶段会一直加入新的功能，但是rc版本几乎就不会加入新的功能了，而主要着重于修改问题。

## 针对于项目的NPM版本规范

1. 对发正式版本的权限进行收口，只能由1个人来进行审核与发布；
2. 确定版本内容：major.minor.patch[-prerelease]
    * major：当发布不向下兼容的版本时，升级major；
    * minor：当进行功能升级迭代时，升级minor；
    * patch：当进行bug修复时，升级patch；
    * prerelease：功能开发阶段 或 bug修复验证阶段，升级prerelease；
3. prerelease只保留beta，并且beta版本号从0开始，比如1.0.0-beta.0
4. 只保留latest和beta两个标签，同时 latest 标签永远指向最新的稳定版本，beta标签永远指向最新的公测版本；
5. 为了确保latest 版本永远指向最新的稳定版本， 发布beta版本时，npm publish时必须加上 --tag beta参数；
6. 使用npm version工具进行版本升级
    * npm version [ | major | minor | patch | premajor | preminor | prepatch | prerelease [--preid=] | from-git]
    * prerelease版本升级：npm version prerelease --preid=beta
7. 同时，每次升级major、minor或patch版本时，要记录版本升级内容；
