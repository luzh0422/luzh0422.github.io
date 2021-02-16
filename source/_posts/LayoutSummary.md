---
title: LayoutSummary
date: 2020-10-18 13:46:26
tags:
    -UI
---
# Layout算法 & 机制总结

## Layout机制

Layout作为CAF UI最重要的一个组成部分，它与控件之间的关系是通过Strategy模式绑定的，如下图所示，相对于Layout也作为一个控件处理，这样的设计可以方便控件在不更新控件的情况下快速切换Layout。

{% asset_img Layout机制.jpg 600 600 Layout类图 %}

<!--more-->

由于Layout的计算通常是比较耗时的，所以Layout的机制不应该是同步的，它应该将这一帧中所有变换的影响因素都记录下来，并在下一帧中计算出结果，设置给对应的控件。具体的时序图如下图所示。

{% asset_img Layout机制Sequence.jpg Layout流程图 %}

以上对Layout的结构和布局流程都有了一定的介绍，下面将着重介绍每个Layout的算法。

## Layout算法

Layout本身是一个Template模式，对外的接口是perform函数，而每个具体Layout算法的实现是在measure函数中。

### LayoutParam的概念

LayoutParam是为了记录控件内部每一个View的布局信息用的，如果说Layout和CompositeView是1：1的关系，那LayoutParam与容器内的View也是1：1的关系，即一个LayoutParam记录了一个View的布局信息。

### ColumnLayout和RowLayout算法

RowLayout与ColumnLayout都是线性布局，RowLayout负责横向线性布局，ColumnLayout负责纵向线性布局，二者的布局算法除了方向外并没有其他的区别，这里以RowLayout的布局算法为例进行介绍。

RowLayout算法的流程如图所示。

{% asset_img RowLayoutAlgorithm.jpg RowLayout算法流程图 %}

ColumnLayout算法与RowLayout相似，只是RowLayout在横向线性布局，在纵向限制高度。而ColumnLayout只在纵向线性布局，在横向限制宽度。

### GridLayout算法

GridLayout是将CompositeView的控件划分成一个个格子，然后根据每个View占用的格子的位置、占用的格子数、margin*、align等属性计算View的位置和尺寸。

GridLayout算法的流程如图所示。

{% asset_img GridLayoutAlgorithm.jpg GridLayout算法流程图 %}

### RelativeLayout

相对布局是用户最常用的布局，它的核心算法是利用拓扑排序计算View布局的先后顺序，当顺序确定后，再一个个去计算View的位置与高宽。

RelativeLayout算法的流程如图所示。


{% asset_img RelativeLayoutAlgorithm.jpg RelativeLayout算法流程图 %}

### FlowLayout

流式布局是瀑布流布局的一种方式，即先沿着一行进行布局，当一行放不下了，就新起一行再进行布局，直到所有的View都布局完成。

FlowLayout算法的流程如图所示。

{% asset_img FlowLayoutAlgorithm.jpg FlowLayout算法流程图 %}

