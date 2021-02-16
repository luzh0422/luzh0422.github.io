---
title: TransitionSummary
date: 2020-10-17 22:22:04
tags:
    - UI
---
# Transition算法 & 机制总结

## Transition概念介绍

Transition是UI动画系统的重要方面，它在两个状态之间增加了“时间轴”，而不是原先的“即时生效”。这为UI界面的交互设计提供了更多的可能性，使得应用交互更加自然。

Transition主要包括PropertyTransition和LayoutTransition，它与UI其他模块的类图如图所示。

<!-- more -->

![](/images/TransitionClassDiagram.png)

## PropertyTransiton

PropertyTransition是为了在属性变换之间增加过渡效果，例如一个View的background由blue变为red，如果不使用Transition，则颜色是直接变化的，如果使用了PropertyTransition，则在background由blue变化为red会有一个过渡效果。

而Transition与Animation最大的区别就是Transition只需要定义对应的Property，而不需要定义类似于Animation的from、to等属性，同时也不需要主动调用类似Animation的start、stop等方法，这样大大简化了开发过程。一个最简单的PropertyTransition使用例子如下：

```javascript
let view = new View();
let propertyTransition = new PropertyTransition();
propertyTransition.property = "background";
view.addTransition(propertyTransition);
```

PropertyTransition的调用流程如图所示

![](/images/PropertyTransitionSequenceDiagram.png)

### MultiStateTransition

MultiStateTransition是PropertyTransition最主要的一个应用场景。在UI开发过程中，多态的转变是最常用的功能之一，而多态变换伴随的主要就是属性变化，但是如果为这些属性都创建PropertyTransition并绑定到View上，工作量其实很大。所以MultiStateTransition主要是为了方便用户为多态改变设置Transition，其接口形式类似于MultiState的接口形式。具体例子如下：

```javascript
let view  = new View();

view.multiState = {
    normal: {
        background: "red",
        scaleX: 1,
        scaleY: 1,
        borderRadius: 0,
        opacity: 1.0
    },
    pressed: {
        background: "yellow",
        scaleX: 1.2,
        scaleY: 1.2,
        borderRadius: 50,
        opacity: 1.0
    },
    disabled: {
        background: "blue",
        scaleX: 1,
        scaleY: 1,
        borderRadius: 0,
        opacity: 0.2
    }
}

view.multiStateTransition = [ 
{
    states: {
        from: "pressed",
        to: "normal",
        bidirectional: false
    },
    default: {
        delay: 0,
        duration: 500
    }
}, {
    states: {
        from: "normal",
        to: "pressed"，
    },
    properties: {
        background: {
            delay: 100,
            duration: 500
        },
        scaleX: "default",
        scaleY: "default",
        borderRadius: "default"
    },
    default: {
        delay: 100,
        duration: 500,
        timingFunction: "easeIn"
    }
}, {
    default: {
        delay: 0,
        duration: 1000
    }
}
];
```

MultiStateTransition的流程图如图所示

![](/images/MultiStateTransitionSequenceDiagram.jpg)

## LayoutTransition

LayoutTransition主要针对更换Layout、增删子View、父容器高宽改变等引起Layout重新计算的场景，对容器内所有的View做一个几何变换的动画，即从当前top、left、width、height属性值过渡到重新计算到的top、left、width、height属性值。

它的编程模型如下。

```javascript
let cv = new CompositeView;

let layoutTransition = new LayoutTransition;
cv.addTransition(layoutTransition);
```

它的流程如图所示。

![](/images/LayoutTransitionSequenceDiagram.jpg)

## 设计小技巧

* 在设计PropertyTransition时，利用TransitionManager控制所有的动画，这样不论有多少PropertyTransition，只需要监听一次vsync信号驱动动画，以此减少函数调用，进行优化。
* 在设计LayoutTransition重新梳理Layout和Animation的关系，从而达到在Layout环境下Animation依然可以执行的效果。