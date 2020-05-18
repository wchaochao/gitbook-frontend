# 设计模式

标签（空格分隔）： 前端知识体系

---

## 单体模式

将代码组织成一个逻辑单元，只能实例化一次

### 基本结构

对象字面量

* 将关联的属性和方法组织在一起
* 划分命名空间，避免命名冲突

```javascript
const Singleton = {
  attribute1: true,
  attribute2: 10,

  method1 () {
    // ...
  },

  method2 () {
    // ...
  },

  init () {
    // ...
  }
}
```

### 私有成员

划分好公有成员和私有成员，重构私有成员时不必担心会殃及其他代码

* 使用下划线表示
* 使用闭包创建

```javascript
MyNamespace.singleton = (function () {
  let privateAttribute1 = false
  let privateAttribute2 = [1, 2, 3]

  function privateMethod1 () {
    // ...
  }
  function privateMethod2 () {
    // ...
  }

  return {
    publicAttribute1: true,
    publicAttribute2: 10,

    publicMethod1 () {
      // ...
    },
    publicMethod2 () {
      // ...
    }
  }
})()
```

### 惰性加载

将实例化推迟到需要使用的时候，常用于必须加载大量数据的单体

```javascript
MyNamespace.singleton = (function () {
  let uniqueInstance

  function constructor () {
    // ...
  }

  return {
    getInstance () {
      if (!uniqueInstance) {
        uniqueInstance = constructor()
      }
      return uniqueInstance
    }
  }
})()
```

### 分支

屏蔽选择细节，提供统一接口

```javascript
MyNamespace.singleton = (function () {
  let objectA = {
    method1 () {
      // ...
    }
  }

  let objectB = {
    method1 () {
      // ...
    }
  }

  return someCondition ? objectA : objectB
})()
```

## 链式调用

### 赋值器

* 返回this以调用下一个对象方法

```javascript
function _$(els) {
  this.elements = []
  for (let elm of els) {
    if (typeof elm === 'string') {
      elm = document.getElementById(elm)
    }
    this.elements.push(elm)
  }
}

_$.prototype = {
  each (fn) {
    for(const elm of this.elements) {
      fn.call(this, elm)
    }
    return this
  },
  setStyle (prop, val) {
    this.each(el => el.style[prop] = val)
    return this
  },
  show () {
    this.each(el => this.setStyle('display', 'block'))
    return this
  },
  addEvent (type, fn) {
    const add = el => {
      if (window.addEventListener) {
        el.addEventListener(type, fn, false)
      } else if (window.attachEvent) {
        el.attachEvent(`on${type}`, fn)
      }
    }
    this.each(el => add(el))
    return this
  }
}

window.$ = function () {
  return new _$(arguments)
}
```

辅助方法

```javascript
Function.prototype.method = function (name, fn) {
  this.prototype[name] = fn
  return this
}

// 安装器，避免命名冲突
window.installHelper = function (scope, interface) {
  scope[interface] = function () {
    return new _$(arguments)
  }
}
```

### 取值器

* 返回数据中断链式调用
* 返回this，数据通过回调返回

```javascript
// 返回数据
function API () {
  let name = 'Hello world'

  this.setName = function (newName) {
    name = newName
    return this
  }

  this.getName = function () {
    return name
  }
}

// 通过回调返回数据
function API2 () {
  let name = 'Hello world'

  this.setName = function (newName) {
    name = newName
    return this
  }

  this.getName = function (callback) {
    callback.call(this, name)
    return this
  }
}
```

## 工厂模式

### 简单工厂

### 复杂工厂
