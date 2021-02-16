---
title: GridSystem
date: 2021-02-15 14:59:56
tags: UI
---

## GridSystem Of BootStrap

BootStrap栅格系统功能简介

栅格系统用于通过一系列的行(row) 与列(column) 的组合来创建页面布局, 你的内容就可以放入这些创建好的布局中.

<!--more-->

1. “行(row)” 必须包含在 .container (固定宽度) 或 .container_fluid (100% 宽度) 中, 以便为其赋予合适的排列 (aligment) 和内补 (padding).
2. 通过 “行(row)” 在水平方向创建一组 “列(column)”
3. 内容应当放置于 “列 (column)” 内, 并且, 只有 “列 (column)” 可以作为行 (row)的直接子元素
4. 通过为 列 设置padding属性, 从而创建列与列之间的间隔. 通过为 .row 元素设置负值margin 从而抵消为 .container 元素设置的padding.
5. 栅格系统中的列是通过 指定1到12 的值来表示其跨越的范围, 例如, 三个等宽的列可以使用三个 .col-xs-4来创建.
6. 如果 一 “行” 中包含了的 “列 (column)” 大于12, 多余的 列 所在的元素将被作为一个整体另起一行排列.

<% asset_img BootStrapGridSystem.png BootStrap栅格系统功能 %>

### 重点属性

#### padding

通过padding设置列 与 列之间的偏移.

#### 多余的列将另起一行排列

如果在一个.row 内包含的列 大于12 个, 包含多余列 的元素将作为一个整体单元被另起一行排列.

#### 列偏移

使用 .col-md-offset-* 类可以将列向右侧偏移. 这些类实际是通过使用*选择器为当前元素增加了左侧的边距(margin). 例如, .col-md-offset-4 将类 .col-md-4 元素向右侧偏移了4个列的宽度.

#### 列排序
通过使用 .col-md-push-* 和 .col-md-pull-* 类就可以很容易的改变列的顺序.

<% asset_img ColumnOffset.jpg 横向偏移的设置 %>

```javascript
<div class="row">
  <div class="col-md-9 col-md-push-3">.col-md-9 .col-md-push-3</div>
  <div class="col-md-3 col-md-pull-9">.col-md-3 .col-md-pull-9</div>
</div>
```
### Android中的GridLayout实现栅格布局

网格由一组无限细线组成，将观察区域分隔成单元格. 内容主要放在这些单元格中.

#### GridLayout 功能

1. 可以自己设置布局中组件的排列方式;
2. 可以自定义网格布局中有多少行, 多少列;
3. 可以直接设置组件位于某行某列;
4. 可以设置组件横框几行或者几列;

<% asset_img AndroidGridLayout.jpg android栅格布局的实现 %>

#### 属性
1. Rows 和 columns

通过设定rows和columns 设定总共有多少个格子.

Although cells do not normally overlap in a GridLayout, GridLayout does not prevent children being defined to occupy the same cell or group of cells. In this case however, there is no guarantee that children will not themselves overlap after the layout operation completes.

尽管GridLayout的格子正常情况不会重叠, GridLayout不会阻止子元素占用同一个或同一组格子. 但是, 在这种情况下, 不能保证在布局操作完成后子项本身不会重叠

GridLayout 子元素属性
1. android: layout_column
    显示该空间的列. 例如, android::layout_column=”0”, 表示在第1列显示该控件; android:layout_column=”1”, 表示在第2列显示该控件.
2. android: layout_columnSpan
    该控件所占的列数. 例如, android:layout_columnSpan=”2”, 表示该控件占2列.
3. android: layout_row
    该控件所在行. 例如, android:layout_row=”0”, 表示在第1行显示该控件; android:layout_row=”1”, 表示在第2行显示该控件. 它和android:layout_column类似.
4. android:layout_rowSpan
    该控件所占的行数. 例如, android:layout_rowSpan=”2”, 表示该控件占2行. 它和android:layout_columnSpan类似.

#### 自定义GridLayout 在栅格系统中的应用

yunos GridLayout是基于Android GridLayout来做的. 为了适配栅格系统, GridLayoutParam新增加row, column属性, 类似于Android 的layout_column和layout_row, 表示子元素在第几列第几行展示.

具体用法如下:

```javascript
let gl = new GridLayout();
gl.columns = 12;
gl.rows = 1;
gl.setLayoutParam(0, "column", 1);
gl.setLayoutParam(0, "row", 1);
gl.setLayoutParam(0, "columnSpan", 2);
gl.setLayoutParam(0, "rowSpan", 2);
```

以上代码表示, 第一个子元素, 从第二行第二列开始, 占据两行两列;