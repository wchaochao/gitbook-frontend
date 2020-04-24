# AMD规范

标签（空格分隔）： 前端知识体系

---

Asynchronous Module Definition，异步模块定义

## define

定义模块

```
define(id?, dependencies?, factory)
```

* `id`: 模块ID，默认为模块加载器请求的脚本
* `dependencies`: 模块依赖，优先加载执行，结果被传递到factory的参数中，默认为CommonJS的`['require', 'exports', 'module']`
* `factory`: 模块工厂，初始化模块，在模块依赖加载执行后执行

```javascript
// object factory
define({
  add (x, y) {
    return x + y
  }
})

// function factory
define(function () {
  return {
    add (x, y) {
      return x + y
    }
  }
})

// CommonJS dependency
define('alpha', function (require, exports) {
  exports.verb = function () {
    return require('beta').verb()
  }
})

// module dependency
define(['alpha'], function (alpha) {
  return {
    verb () {
      return alpha.verb() + 2
    }
  }
})
```

### define.amd

用于指定该define函数服从AMD规范

```javascript
define.amd = {}
```

## require

## RequireJS

AMD规范的具体实现

### 加载RequireJS

```html
<script src="js/require.js"></script>
```

### 加载入口文件

RequireJS加载好后，会去异步加载data-main属性指定的入口文件

```html
<script data-main="scripts/main" src="js/require.js"></script>
```

### 加载模块

使用require函数加载模块

```javascript
// main.js
require(['moduleA'], function (a) {
  console.log(a)
})
```

### 定义模块

使用define函数定义模块

```javascript
// moduleA.js
define('moduleA', ['moduleB'], function (b) {
  return b.add(10)
})

// moduleB.js
define({
  add (n) {
    return n + 1
  }
})
```

### 配置

使用require.config函数在入口文件中配置加载行为

```javascript
// main.js
require.config({
  baseUrl: 'test', // 模块路径的baseUrl
  paths: { // 模块路径映射
    'moduleA': 'lib/moduleA.min'
  }
})

require(['moduleA'], function (a) {
  console.log(a)
})
```

#### baseUrl

* 有config时，baseUrl以config配置为准
* 没有config有data-main时，baseUrl为入口文件前面部分
* 没有config也没有data-main时，baseUrl为当前页面目录

#### 模块路径

* moduleID以.js结束，则模块路径为相对于当前HTML文档的moduleID地址
* moduleID有URL协议，则模块路径为指定的路径
* 其他情况，moduleID为baseUrl + (paths[moduleID] || moduleID) + '.js'

#### shim

加载非规范模块

```javascript
require.config({
  shim: {
    underscore: {
      exports: '_'
    },
    backbone: {
      deps: ['undescore', 'jquery'],
      exports: 'Backbone'
    },
    'jquery.scoll': {
      deps: ['jquery'],
      exports: 'jQuery.fn.scroll'
    }
  }
})
```

### 懒加载

在模块实现中使用require函数实现懒加载

```javascript
// main.js
define(function () {
  console.log('main')
  document.onclick = function () {
    require(['a'], function (a) {
      a.test()
    })
  }
})
```

### 包装CommonJS

使用保留的require, exports, module包装CommonJS在浏览器端进行

```javascript
define(function (require, exports, module) {

})
```

示例

```javascript
// a.js
define(function (require, exports, module) {
  const a = 100
  module.exports.a = a
})

// b.js
define(function (require, exports, module) {
  const result = require('./a')
  console.log(result.a)
})
```

### 插件

实现一些特定的功能

#### domReady插件

判定何时DOM已经Ready

```javascript
require(['domready!'], function () {
  console.log('ready')
})
```

#### Text插件

加载html、css等文本文件

```javascript
require(['some/module', 'text!some/module.html', 'text!some/module.css'], function (module, html, css) {
  // ...
})
```
