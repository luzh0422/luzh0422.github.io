---
title: DesignPattern
date: 2021-02-17 17:55:20
tags:
---

## 概述

本文旨在一文记录23中设计模式，包括Iterator、Adapter、Template、Factory、Singleton、Prototype、Builder、AbstractFactory、Bridge、Strategy、Composite、Decorator、Visitor、ChainOfResponsibility、Mediator、Facade、State、Memento、Observer、Proxy、FlyWeight、Command、Interpretor。主要通过一句话说明这个设计模式的特点与用处，并附上对应的类图。

同时本文致力于一句话概括总结对应设计模式的功能特点，可能过于简洁，但是如果你对设计模式已经很了解，可以帮助你记忆和理解。如果你想了解更多设计模式的内容，可以参照《图解设计模式》这本书。

<!--more-->

## 类的功能 & 类图

### Iterator模式

将数据存储和数据遍历分割。

{% asset_img Iterator.jpg 400 Iterator %}

### Adapter

利用现有功能适配新的功能。

Adapter模式又分为类适配器（利用继承）和实例适配器（利用委托）。

{% asset_img Adapter1.jpg 400 类适配器 %}

{% asset_img Adapter2.jpg 400 实例适配器 %}

### Template

父类负责算法流程，子类负责具体的实现。

{% asset_img Template.jpg 200 Template %}

### Factory

利用Template模式构建实例。

{% asset_img Factory.jpg 400 Factory %}

### Singleton

全局唯一单例模式，静态的getInstance和构造函数私有。

{% asset_img Singleton.jpg 400 Singleton %}

### Prototype

利用现有实例创建新的实例，用于构造实例时，需要复杂的设置，这样通过现有实例创建新的实例，就不需要重新设置了。

主要是利用C++的拷贝构造函数。

{% asset_img Prototype.jpg 400 Prototype %}

### Builder

用于构造复杂的实例。

{% asset_img Builder.jpg 400 Builder %}

### AbstractFactory

抽象工厂通过组装抽象零件成为抽象产品。

### Bridge

将功能层侧接口和实现层次接口分开，避免过长的集成链。

{% asset_img Bridge.jpg 400 Bridge %}

### Strategy

利用委托模式，专门设置好Strategy类，便于整体替换算法。

{% asset_img Strategy.jpg 400 Strategy %}

### Composite

内容和容器的一致性。通过继承来达到一致性，通过一致性达到递归包含。

{% asset_img Composite.jpg 400 Composite %}

### Decorator

修饰物和被修饰物的一致性，可以递归地扩展类的功能。

{% asset_img Decorator.jpg 400 Decorator %}

### Visitor

将数据存储和数据访问分开。

{% asset_img Visitor.jpg 400 Visitor %}

### ChainOfResponsibility

自己能处理就自己处理，自己处理不了就交给下一个处理。

优点是每个类只需要负责自己的功能，问题是处理链过长，耗时会比较久。

{% asset_img ChainOfResponsibility.jpg 400 Responsibility %}

### Mediator

所有的处理都交给管理者来进行统一处理，避免各个类处理的问题。

{% asset_img Mediator.jpg 400 Mediator %}

### Facade

忽略内部复杂的调用，对外只暴露简单的接口。该模式主要适用于框架结构复杂，调用复杂的情况下。

{% asset_img Facade.jpg 400 Facade %}

### State

将依赖于状态的方法都放在State类中，这样就可以减少关于状态的判断。

{% asset_img State.jpg 400 State %}

### Memento

用于保存类的状态，这样就可以回退对象的状态。

主要是利用C++的友元函数。

{% asset_img Memento.jpg 400 Memento %}

### Obsever

根据对象状态的变化，通知Observer进行处理。

{% asset_img Observer.jpg 400 Obsever %}

### FlyWeight

通过尽量复用实例避免重复创建实例，来达到节省内存的用处。

{% asset_img FlyWeight.jpg 400 FlyWeight %}

### Proxy

能通过Proxy处理的就通过Proxy处理，不能通过的Proxy再通过本体处理。

Proxy主要用于处理一些本地初始化时间长的问题。

{% asset_img Proxy.jpg 400 Proxy %}

### Command

利用Command代替函数的直接调用，这样就可以记录函数的调用过程（Command实例）。

{% asset_img Command.jpg 400 Command %}

### Interpretor

利用一定的规则，将字符串解析为树形结构。

{% asset_img Interpretor.jpg 400 Interpretor %}
