# JavaScript模块

标签（空格分隔）： 前端知识体系

---

当代码越来越多时

* 分而治之：将代码拆分成多个文件
* 模块化：每个文件按照模块来组织

## 封装

### 函数

* 污染全局变量

```javascript
function foo () {
  // ...
}

function bar () {
  // ...
}
```

### NameSpace模式

* 减少了全局变量个数
* 未能保护内部数据

```javascript
const myApp = {
  foo () {},
  bar () {}
}
```

### IIFE模式

* 现代模块实现的基石

```javascript
const Module = (function () {
  const _private = 'safe now'
  const foo = function () {
    console.log(_private)
  }

  return {
    foo
  }
})()
```

#### 放大模式

一个模块分为了几个部分

```javascript
var module1 = (function (mod) {
   mod.m3 = function () {
     // ...
   }
   return mod
})(module1)
```

#### 宽放大模式

处理放大模式中module可能不存在的情况

```javascript
var module1 = (function (mod) {
   mod.m3 = function () {
     // ...
   }
   return mod
})(window.module1 || {})
```

#### 传入依赖

依赖从外界传入

```javascript
var module1 = (function ($, YAHOO) {
   // ...
})(jQuery, YAHOO)
```

## 加载

* 依赖不清晰
* 请求过多

```html
<script src="zepto.js"></script>
<script src="underscore.js"></script>
<script src="jquery.js"></script>
...
```

## 规范

解决依赖管理问题

### CommonJS规范

NodeJS的模块化方案

* module.exports导出值的拷贝
* require在运行时同步加载模块

```javascript
// 加载模块
const math = require('math')

// 导出模块
exports.add = function (a, b) {
  return math.add(a, b)
}
```

### AMD

浏览器的模块化方案

* define定义模块
* 回调导出模块：异步加载完依赖模块后再执行
* require加载模块

```javascript
// main.js
require(['moduleA'], function (a) {
  console.log(a)
})

// moduleA.js
define({
  count: 1
})
```

### CMD

浏览器的模块化方案

* define定义模块
* exports导出模块
* require加载模块

```javascript
define(function (require, exports) {
  const a = require('./a')
  a.doSomething()

  exports.foo = 'bar'
  exports.doSomething = function () {}
})
```

### ES6 Module

ECMAScript的模块化方案

* export导出值的引用
* import编译时加载模块

```javascript
// math.js
export default math = {
  PI: 3.14
}

// app.js
import math from './math'
console.log(math.PI)
```

## 打包

解决请求过多的问题

* Concat: 合并
* Minify: 压缩
* Uglify: 混淆
* Source Map: 逆向所有合并、压缩、混淆，用于打包后调试

### browserify

* CommonJS在浏览器环境的打包方案

### webpack

* 将任一静态资源当作模块打包
