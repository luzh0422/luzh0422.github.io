---
layout: p
title: cb_never_called
date: 2020-12-24 18:30:05
tags: 
    - NodeJS
---

# cb() never called解决方法

执行npm install更新依赖库的时候，报错 `npm ERR!cb() never called`

1. sudo npm cache clean -f

2. 删除package-lock.json

3. 删除node_modules

4. 执行npm install
