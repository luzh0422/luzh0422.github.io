---
title: FocusSummary
date: 2020-10-18 14:17:09
tags:
    - UI
---

# Focus Summary

### 基础属性介绍

#### focusable

用来定义一个View是否可以被focus。只有当一个View的focusable属性为true时才可以被focus，换言之当一个View的focusable属性为false， 则它永远不会被focus。

#### focusMode

用来定义一个CompositeView获取焦点时，是由CompositeView的子孙节点先获得焦点，还是由CompositeView自己先获得焦点。(注：这个属性生效的前提是CompositeView.focusable = true)

focusMode的值为枚举类型：[CompositeView.FocusMode.SelfFirst, CompositeView.FocusMode.DescendantFirst]。

1. CompositeView.FocusMode.SelfFirst：由CompositeView自己先获得焦点；
2. CompositeView.FocusMode.DescendantFirst：由CompositeView的子孙节点先获得焦点；

<!--more-->

### focus()及focus链的构建；

#### focus()介绍

focus()是让一个控件获得焦点的主要方法。

一个View可以获得焦点有几个必要条件：

1. focusable属性为true；
2. view在一棵绘制树上；
3. view及view的祖先节点的visibility属性和enabled属性都为true；

#### view获得焦点后如何调整一颗绘制树的focus链

假设当前一棵View绘制树如下，此时View\_1获取了焦点。整个focus链为Window->CompositeView\_2->View\_4。

{% asset_img focus_1.jpg focus焦点链 %}

当用户调用View\_1.focus()，View\_1会通知CompositeView\_1更新focus链，CompositeView\_1会通知Window更新focus链，Window会通知CompositeView\_2更新focus链，CompositeView\_2会通知View\_4更新focus链并取消focus。更新后的Focus链如下。

{% asset_img focus_2.jpg focus焦点链 %}

这种链式结构可以方便地在一颗Window Tree上进行focus转移，有效地保证一个节点在获取焦点时，原先获取焦点的节点被blur掉，保证了focus的唯一性。并且通过链式结构可以轻易获取到Window Tree上正处于focus的节点。

### 按键和旋钮事件与Focus的转移

#### FocusManager概念介绍

FocusManager是CompositeView上的焦点管理器，当产生按钮事件或旋钮事件的时候，FocusManager会按照用户配置好的条件找到下一个要focus的节点并调用focus()函数，此时view会按照上述的focus链机制focus&blur相应节点。

#### 按键事件与focus的转移

按键事件目前主要包括"ArrowUp"、"ArrowLeft"、"ArrowRight"、"ArrowDown"四个类型，由focus的View首先接收到按键事件，但默认情况下这个View不会进行处理，而是交给View的parent即CompositeView处理，CompositeView会按照按键方向调用FocusManager.toUp(或者是toLeft、toRight、toDown)函数，然后FocusManager会按照View之间的方向&距离计算下一个要focus的View。如果找不到可以focus的节点，则交给CompositeView的parent继续处理，直到找到Window为止。

{% asset_img focus_3.jpg focus转移 %}
{% asset_img focus_4.jpg focus转移 %}

#### 旋钮事件与focus的转移

旋钮事件目前主要包括"Tab"，并通过事件属性shiftKey判断是正向旋转还是逆向旋转。旋钮事件主要通过三种方式获取下一个要focus的view，下面将一一介绍，获取方式的优先级递减。

1. 通过设置focusNextTarget和focusPreviousTarget属性，来直接获取在旋钮旋转时下一个和上一个要focus的view，这个属性的优先级最高，但是不够灵活；
2. 通过设置tabIndex属性，直接通过Window的FocusManager按照tabIndex顺序在Window内找到下一个要focus的view，这个优先级次之；
3. 交给View的parent处理，通过父容器的FocusManager在父容器内找到下一个要focus的View，这个优先级最低，也是目前的通用算法。

### Window切换间Focus逻辑

应用开发经常会在两个Window之间切换，那么Window之间切换时，Focus要如何处理，本节主要讲解这个逻辑。

首先在Window deActive时找到当前Window下正focus的View，并记录在该Window上。然后在Window active的时候判断这个View是否还在这个Window下面并且判断这个View的Visibility属性，当判断都为true时将这个View设为focus。如果判断为false，则将Window按照本身的FocusMode属性进行 focus。

以上就达到了Window切换时的Focus记录功能。

### 失焦处理

目前有两处逻辑会引起失焦处理：1. 当一个View获得焦点后，一段时间没有key事件处理，做失焦处理；2. key事件转换到touch事件时，做失焦处理；

失焦处理后会将失去焦点的View记录在Window上，当再次有Key事件上来时，判断这个View是否还在这个Window上，如果在则focus View， 如果不在则按Window的FocusMode对Window进行focus。

### 控件重写按键与旋钮逻辑

每一个控件都可以重写onKeyUp方法，并利用FocusManager按照每个控件各自的需求与特点重写事件与focus的逻辑。