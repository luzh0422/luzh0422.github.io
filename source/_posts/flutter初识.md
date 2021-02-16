---
title: flutter初识
date: 2021-02-15 16:06:00
tags:
---

## flutter初学

Flutter应用本身就是一个widget，大部分widget都有一个build()方法。在应用程序的build方法中创建会在设备上显示的widget。

<!--more-->

```javascript
class _myHomePageState extends State<MyHomePage> {
    @override
    Widget build(BuildContext context) {
        return new Scaffold(
            appBar: new AppBar(
                title: new Text(widget.title),
            ),
            body: new Center(
                child: new Text('Hello World', style: new TextStyle(fontSize: 32.0)),
            ),
        )
    }
}
```

### flutter的widget，element，renderObject之间的关系

可以这样理解：

1. widget是数据层，只是数据结构，方便用户编程；

2. element是真正构建的UI树，用于管理UI属性结构；

3. renderObject是真正构建的UI绘制树；

### flutter UI 渲染流程

用户通过调用setState()方法记录所有的脏元素，添加到BuildOwner对象的_dirtyElements成员变量，然后调用scheduleFrame来注册Vsync回调。当下一次vsync信号的到来时会执行handleBeginFrame()和handleDrawFrame()来更新UI。

sublinear Layout的原理就是靠relayoutBoundary，当一个Layout需要改变的时候，就判断它是不是relayoutBoundary，如果是，就把它标记位dirty，如果不是就查找它的parent节点，然后再判断parent的relayoutBoundary。

## Flutter Layout 初级篇

在Flutter中几乎所有的东西都是组件, 包括layout模型也是组件。布局类组件都会包含一个或多个子组件，不同的布局类组件对子组件排版方式不同。本文将Layout分为线性布局、弹性布局、流式布局、层叠布局、相对布局五个模块进行介绍。

### 线性布局

线性布局，即指沿水平或垂直方向排布子组件。Flutter中通过Row和Column来实现线性布局，类似于Android中的LinearLayout控件。同时，Row和Column都继承自Flex，因此Flex支持的属性，Row和Column都支持；

这个可以对照AliOS的RowLayout和ColumnLayout

### Row和Column支持的属性

Row和Column只有在方向上不同，其余属性基本一致，下面将以Row为例进行介绍；

1. textDirection： 表示水平方向子组件的布局顺序（是从左往右还是从右往左），默认为系统当前Locale环境的文本方向（如中文、英语都是从左往右，而阿拉伯语是从右往左）。

2. mainAxisSize: 表示Row在主轴（水平）方向占用的空间，默认是MainAxisSize.max，表示尽可能多的占用水平方向的空间，可以类比matchParent；而MainAxisSize.min表示尽可能少的占用水平空间，Row的实际宽度等于所有子组件占用的水平空间，可以类比wrapContent；

3. mainAxisAlignment： 表示子组件在Row所占用的水平空间内对齐方式；如果mainAxisSize值为MainAxisSize.min，则此属性无意义，因为此时Row的宽度等于子组件的宽度。只有当mainAxisSize的值为MainAxisSize.max时，MainAxisAlignment.start表示沿textDirection的初始方向对齐，如textDirection取值为TextDirection.ltr时，则MainAxisAlignment.start表示左对齐，textDirection取值为TextDirection.rtl时，则MainAxisAlignment.start表示右对齐。而MainAxisAlignment.end和MainAxisAlignment.start正好相反；MainAxisAlignment.center表示居中对齐。

4. verticalDirection: 表示Row纵轴（垂直）的对齐方向，默认是VerticalDirection.Down，表示从上到下。

5. crossAxisAlignment： 表示子组件在纵轴方向的对齐方式, Row的高度等于子组件中最高的子元素高度， 它的取值和MainAxisAlignment一样（包含start、end、center三个值）；

6. children: 子组件数组

**实际上，Row和Column都只会在主轴方向占用尽可能大的空间，而纵轴的长度取决于他们最大子元素的长度**

### 弹性布局

弹性布局允许子组件按照一定比例来分配父容器空间。弹性布局的概念在其他UI系统中也都存在，如H5中的弹性盒子布局，Android中的FlexboxLayout等。

Flex 组件可以沿着水平或垂直方向排列子组件，如果你知道主轴方向，使用Row和Column会方便一些，因为Row和Column都继承自Flex，所以能使用Flex的地方基本上都可以使用Row和Column。Flex本身功能是很强大的，它也可以和Expanded组件配合实现弹性布局。

这个可以对照AliOS的FlexLayout

### Expanded

可以按比例“扩展”Row、Column和Flex子组件所占用的空间。

```
const Expanded({
    int flex = 1,
    @required widget child
})
```

flex参数为弹性系数， 如果为0或null，则child是没有弹性的，即不会被扩伸占用的空间。如果大于0，所有的Expanded按照其flex的比例来分割主轴的全部空闲时间。

例, Flex与Expanded的配合：

```
Flex(
    direction: Axis.horizontal,
    children: <widget>[
        Expanded(
            flex: 1,
            child: Container(
                height: 30.0,
                color: Colors.red,
            ),
        ),
        Expanded(
            flex: 2,
            child: Container(
                height: 30.0,
                color: Colors.green,
            ),
        ),
    ]
)
```

### 流式布局

在介绍Row和Column时，如果子widget超出屏幕范围，则会报溢出错误，如：

```
Row(
    children: <widget>[
        Text("xxx" * 100)
    ],
);
```
￼

这是因为Row默认只有一行，如果超出屏幕不会折行。

我们把超出屏幕显示范围会自动折行的布局称为流式布局。Flutter中通过Wrap和Flow来支持流式布局。

### Wrap支持的属性

与Row和Column重复的属性不再重复介绍，可以参考前面介绍Row的部分。下面看一下Wrap特有的几个属性：

1. spacing：主轴方向子widget的间距；

2. runSpacing：纵轴方向的间距；

3. runAlignment：纵轴方向的对齐方式；

### Flow

### 层叠布局

层叠布局和Web中的绝对定位、Android中的Frame布局是相似的，子组件可以根据距父容器四个角的位置来确定自身的位置。Flutter中使用Stack和Positioned这两个组件来配合实现绝对定位。Stack允许子组件堆叠，而Positioned用于根据Stack的四个角来确定子组件的位置。

*层叠布局相对于绝对定位*

### Stack支持的属性
1. alignment：此参数决定如何去对齐没有定位或部分定位的子组件。

2. textDirection：用于确定alignment对齐的参考系。

3. fit：此参数用于确定没有定位的子组件如何适应Stack的大小。StackFit.loose表示使用子组件的大小，StackFit.expand表示扩伸到Stack的大小。

4. overflow：此属性决定如何显示超出Stack显示空间的子组件；值为Overflow.clip时，超出部分会被裁剪，值为Overflow.visible时则不会；

### Positioned支持的属性

1. left、top、right、bottom分别代表离Stack左、上、右、下四边的距离。width和height用于指定需要定位元素的宽度和高度。

### 相对定位

Align可以更简单地调整一个子元素在父元素中的位置。

Align组件可以调整子组件的位置，并且可以根据子组件的宽高类确定自身的宽高。

### Align支持的属性

alignment：表示子组件在父组件中的起始位置。

widthFactor和heightFactor是用于确定Align组件本身宽高的属性；它们是两个缩放因子，会分别乘以子元素的宽、高，最终的结果就是Align组件的宽高。如果值为null，则组件的宽高将会占用尽可能多的空间。

### 参考

[flutter中文网](https://book.flutterchina.club/chapter4/alignment.html)