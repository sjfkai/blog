---
title: 掉入循环require()的坑
date: 2015-1-24 18:18:46
tags:
  - node.js
---

今天在老项目里新加了一处`require('moduleA')`，奇怪的是从未修改过得文件却报错了。

去掉以后就可以正常运行。

经过调试发现，新加上`require('moduleA')`后，另一个文件里调用的`require('moduleB').func`，`func`为`undefined`

而`moduleB`里明确定义了`func`

简单走了一下require的流程，发现循环require确实会存在模块未初始化完成的问题。

官方手册对此也有提及：
[Modules > Cycles](http://nodejs.org/api/modules.html#modules_cycles)

一种解决方法就是不要使用`require('moduleB').func`这种用法。而是

```
var a = require('module');

function f() {
  var func = require('moduleB').func;
}
```

看来好好规划模块确实很有必要，而且以后也不能过于随心所欲的`require()`了
