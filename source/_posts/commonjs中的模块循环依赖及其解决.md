---
title: commonjs中的模块循环依赖及其解决
date: 2021-02-15 15:41:40
tags:
---

## CommonJS中的模块循环依赖及其解决

Node.js是CommonJS规范的主要实践者

### CommonJS模块的加载原理

CommonJS的一个模块就是一个脚本文件, require 命令第一次加载该脚本, 就会执行整个脚本, 然后在内存生成一个对象.

<!--more-->

```javascript
id: '...',
exports: {...},
loaded: true,
...
```

上面代码就是Node内部加载模块后生成的一个对象. 该对象的 id 属性是模块名, exports 属性是模块输出的各个接口, loaded 属性是一个布尔值, 表示该模块的脚本是否执行完毕.

以后需要用到这个模块的时候, 就会到 exports 属性上面取值. 即时再次执行require 命令, 也不会再执行该模块, 而是到缓存中取值. 也就是说, CommonJS无论加载多少次, 都只会在第一次加载时运行一次, 以后再加载, 就返回第一次运行的结果, 除非手动清除系统缓存.

#### 循环引用问题的解决

实际上, Node.js官网上就有关于模块循环引用的说明.

给出的解决方案如下:

**************
When main.js loads a.js, then a.js in turn loads b.js. At that point, b.js tries to load a.js. In order to prevent an infinite loop, an unfinished copy of the a.js exports object is returned to the b.js module. b.js then finishes loading, and its exports object is provided to the a.js module.
**************

简单说就是，为了防止模块载入的死循环，Node.js 在模块第一次载入后会把它的结果进行缓存，下一次再对它进行载入的时候会直接从缓存中取出结果。所以在这种循环依赖情形下，不会有死循环，但是却会因为缓存造成模块没有按照我们预想的那样被导出。具体例子如下:

`A.js`

```javascript
let b = require('./B');

console.log('A: before logging b');
console.log(b);
console.log('A: after logging b');

module.exports = {
    A: 'this is a Object'
};
```

`B.js`

```javascript
let a = require('./A');

console.log('B: before logging a');
console.log(a);
console.log('B: after logging a');

module.exports = {
    B: 'this is b Object'
};
```

运行A.js, 结果如下:

```javascript
B: before logging a
{}
B: after logging a
A: before logging b
{ B: 'this is b Object' }
A: after logging b
```

在这个例子中，A.js 首先 require 了 B.js, 程序进入 B.js，在 B.js 中第一行又 require 了 A.js。

在 Node.js 运行 A.js 之后，它就被缓存了，但需要注意的是，此时缓存的仅仅是一个未完工的 A.js（an unfinished copy of the a.js）。所以在 B.js require A.js 时，得到的仅仅是缓存中一个未完工的 A.js，具体来说，它并没有明确被导出的具体内容（A.js 尾端）。所以 B.js 中输出的 a 是一个空对象。

### 解决问题:

在循环依赖的每个模块中先导出自身，然后再导入其他模块.其实就是延后引用, 一些模块可以在使用的时候再引用;

### 参考

[循环加载ES6模块](http://www.waibo.wang/bible/es6/html/23/23.4.html)

[Node.js 中的模块循环依赖及其解决](http://maples7.com/2016/08/17/cyclic-dependencies-in-node-and-its-solution/)