# 设计模式

标签（空格分隔）： 前端知识体系

---

## 模块模式

将代码组织成一个逻辑单元

### 基本结构

对象字面量

* 将关联的属性和方法组织在一起
* 划分命名空间，避免命名冲突

```javascript
const module1 = {
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

### 成员

划分好公共成员和私有成员，面向接口编程

```javascript
const module2 = (function () {
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

## 链式调用

返回this以调用下一个对象方法

```javascript
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

function API2 () {
  let name = 'Hello world'

  this.setName = function (newName) {
    name = newName
    return this
  }

  // 通过回调返回数据
  this.getName = function (callback) {
    callback.call(this, name)
    return this
  }
}
```

## 单体模式

共享一个模块，实现懒加载

```javascript
const Singleton = (function () {
  let uniqueInstance

  function createInstance () {
    // ...
  }

  return {
    getInstance () {
      if (!uniqueInstance) {
        uniqueInstance = createInstance()
      }
      return uniqueInstance
    }
  }
})()
```

## 工厂模式

通过工厂创建模块

### 简单工厂

将成员对象的创建转交给一个外部对象

```javascript
const BicycleFactory = {
  createBicycle (model) {
    let bicycle

    switch (model) {
      case 'The Speedster':
        bicycle = new Speedster()
        break
      case 'The Lowrider':
        bicycle = new Lowrider()
        break
      case 'The Comfort Cruiser':
      default:
        bicycle = new ComfortCruiser()
    }

    Interface.ensureImplements(bicycle, Bicycle)
    return bicycle
  }
}

class BicycleShop {
  sellBicycle (model) {
    const bicycle = BicycleFactory.createBicycle(model)

    bicycle.assemble()
    bicycle.wash()

    return bicycle
  }
}
```

### 复杂工厂

将成员对象的创建推迟到子类

```javascript
class BicycleShop {
  sellBicycle (model) {
    const bicycle = BicycleFactory.createBicycle(model)

    bicycle.assemble()
    bicycle.wash()

    return bicycle
  }

  createBicycle (model) {
    throw new Error('Unsupported operation on an abtract class.')
  }
}

class AcmeBicycleShop extends BicycleShop {
  createBicycle (model) {
    let bicycle

    switch (model) {
      case 'The Speedster':
        bicycle = new AcmeSpeedster()
        break
      case 'The Lowrider':
        bicycle = new AcmeLowrider()
        break
      case 'The Comfort Cruiser':
      default:
        bicycle = new AcmeComfortCruiser()
    }

    Interface.ensureImplements(bicycle, Bicycle)
    return bicycle
  }
}
```

## 装饰者模式

装饰接口

### 类装饰者

* 使用装饰对象包装组件，装饰对象与组件形成线性结构
* 装饰对象与组件实现相同的接口
 * 装饰对象执行接口时执行自定义操作，可以使用或不使用组件的接口

```javascript
const Bicycle = new Interface('Bicycle', ['assemble', 'repair', 'getPrice'])

class BicycleDecorator {
  constructor (bicycle) {
    Interface.ensureImplements(bicycle, Bicycle)
    this.bicycle = bicycle
  }

  // 方法前添加行为
  assemble () {
    return 'Attach decorator. ' + this.bicycle.assemble()
  }

  // 方法后添加行为
  getPrice () {
    return this.bicycle.getPrice() + 9.00
  }

  // 替换方法
  repair () {
    return 'repair'
  }

  // 添加新方法
  ring () {
    return 'Bell rung.'
  }
}
```

### 函数装饰者

给原函数添加功能

```javascript
function before (fn, beforeFn) {
  return function () {
    let flag = beforeFn.apply(this, arguments)
    if (flag === false) {
      return
    }
    return fn.apply(this, arguments)
  }
}

function after (fn, afterFn) {
  return function () {
    let result = fn.apply(this, arguments)
    afterFn.apply(this, arguments)
    return result
  }
}
```

## 组合模式

* 使用组合对象包装子对象，组合对象与子对象形成树结构
* 组合对象与子对象实现相同的接口
 * 组合对象执行接口时往下传递
 * 叶对象执行接口时执行具体操作

```javascript
const Composite = new Interface('Composite', ['add', 'remove', 'getChild'])
const FormItem = new Interface('FormItem', ['save', 'restore'])

class CompositeForm {
  constructor (id, method = 'POST', action = '#') {
    this.formComponents = []

    this.element = document.createElement('form')
    this.element.id = id
    this.element.method = method
    this.element.action = action
  }

  has (child) {
    const index = this.formComponents.indexOf(child)
    return index !== -1
  }

  add (child) {
    if (this.has(child)) {
      return
    }
    Interface.ensureImplements(child, Composite, FormItem)
    this.formComponents.push(child)
    this.element.appendChild(child.getElement())
  }

  remove (child) {
    const index = this.formComponents.indexOf(child)
    if (index !== -1) {
      this.formComponents.splice(index, 1)
      this.element.removeChild(child)
    }
  }

  getChild (i) {
    return this.formComponents[i]
  }

  forEach (callback) {
    this.formComponents.forEach(item => {
      callback.call(this, item)
    })
  }

  save () {
    this.forEach(item => item.save())
  }

  restore () {
    this.forEach(item => item.restore())
  }
}

class Field {
  constructor (id) {
    this.id = id
    this.element = document.createElement('div')
    this.element.className = 'input-field'
  }

  add () {}
  remove () {}
  getChild () {}

  save () {
    setCookie(this.id, this.getValue)
  }

  getValue () {
    return this.element.value
  }

  restore () {
    this.element.value = getCookie(this.id)
  }

  getElement () {
    return this.element
  }
}
```

## 桥接模式

将抽象与实现相分离，以便二者独立变化

```javascript
addEvent(element, 'click', getBeerByIdBridge)

function getBeerByIdBridge (e) {
  getBeerById(this.id, beer => {
    console.log('Requested Beer: ', beer)
  })
}

function getBeerById (id, callback) {
  asyncRequest('GET', `beer.url?id=${id}`, res => {
    callback(res.responseText)
  })
}
```

## 门面模式

简化接口

* 处理浏览器兼容性
* 组合函数

```javascript
DED.util.Event = {
  addEvent (el, type, fn) {
    if (window.addEventListener) {
      el.addEventListener(type, fn, false)
    } else if (window.attachEvent) {
      el.attachEvent(`on${type}`, fn)
    } else {
      el[`on${type}`] = fn
    }
  },
  getEvent (e) {
    return e || window.event
  },
  getTarget (e) {
    return e.target || e.srcElement
  },
  stopPropagation (e) {
    if (e.stopPropagation) {
      e.stopPropagation()
    } else {
      e.cancelBubble = true
    }
  },
  preventDefault (e) {
    if (e.preventDefault) {
      e.preventDefault()
    } else {
      e.returnValue = false
    }
  },
  stopEvent (e) {
    this.stopPropagation(e)
    this.preventDefault(e)
  }
}
```

## 适配器模式

适配接口

```javascript
const clientObject = {
  string1: 'foo',
  string2: 'bar',
  string3: 'baz'
}

function interfaceMethod(str1, str2, str3) {
  // ...
}

function clientToInterfaceAdapter (o) {
  return interfaceMethod(o.string1, o.string2, o.string3)
}
```
