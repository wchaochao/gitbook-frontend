# 设计模式

标签（空格分隔）： 前端知识体系

---

## 单体模式

将代码组织成一个逻辑单元，最多实例化一次

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

划分好公共成员和私有成员，面向接口编程

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

需要时才实例化

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

根据分支返回拥有相同接口的单体

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

通过工厂创建单体

* 在运行时动态选择所用的类
* 多个小对象组合成大对象
* 成员对象间存在设置耦合

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

function BicycleShop () {}
BicycleShop.prototype.sellBicycle = function (model) {
  const bicycle = BicycleFactory.createBicycle(model)

  bicycle.assemble()
  bicycle.wash()

  return bicycle
}
```

### 复杂工厂

将成员对象的创建推迟到子类

```javascript
function BicycleShop () {}
BicycleShop.prototype = {
  sellBicyclev(model) {
    const bicycle = this.createBicycle(model)

    bicycle.assemble()
    bicycle.wash()

    return bicycle
  },
  createBicycle (model) {
    throw new Error('Unsupported operation on an abtract class.')
  }
}

function AcmeBicycleShop () {}
extend(AcmeBicycleShop, BicycleShop)
AcmeBicycleShop.prototype.createBicycle = function (model) {
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
```

## 桥接模式

将抽象与实现相分离，以便二者独立变化

### 抽象处理

* 封装相同部分，再桥接具体实现
* 解除对特定变量的耦合，便于单元测试

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

### 多维变化

封装每个变化，再进行桥接组合

```javascript
// 运动单元
function Speed (x, y) {
  this.x = x
  this.y = y
}
Speed.prototype.run = function () {
  console.log('run')
}

// 着色单元
function Color (color) {
  this.color = color
}
Color.prototype.draw = function () {
  console.log('draw')
}

// 球类
function Ball (x, y, c) {
  this.speed = new Speed(x, y)
  this.color = new Color(c)
}
Ball.prototype.init = function () {
  this.speed.run()
  this.color.draw()
}
```

## 组合模式

将大批子对象按照组合对象、叶对象组织成树结构

* 每个节点实现相同的接口
 * 组合对象执行接口时往下传递
 * 叶对象执行接口时执行具体操作
* 适用于动态用户界面

### 动态表单

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
    throw new Error('Unsupported operation on the class Field')
  }

  restore () {
    this.element.value = getCookie(this.id)
  }

  getElement () {
    return this.element
  }
}

class InputField extends Field {
  constructor (id, label) {
    super(id)

    this.input = document.createElement('input')
    this.input.id = id

    this.label = document.createElement('label')
    const labelTextNode = document.createTextNode(label)
    this.label.appendChild(labelTextNode)

    this.element.appendChild(this.label)
    this.element.appendChild(this.input)
  }

  getValue () {
    return this.input.value
  }
}

class TextareaField extends Field {
  constructor (id, label) {
    super(id)

    this.textarea = document.createElement('textarea')
    this.textarea.id = id

    this.label = document.createElement('label')
    const labelTextNode = document.createTextNode(label)
    this.label.appendChild(labelTextNode)

    this.element.appendChild(this.label)
    this.element.appendChild(this.textarea)
  }

  getValue () {
    return this.textarea.value
  }
}

class SelectField extends Field {
  constructor (id, label) {
    super(id)

    this.select = document.createElement('select')
    this.select.id = id

    this.label = document.createElement('label')
    const labelTextNode = document.createTextNode(label)
    this.label.appendChild(labelTextNode)

    this.element.appendChild(this.label)
    this.element.appendChild(this.select)
  }

  getValue () {
    return this.select.options[this.select.selectedIndex].value
  }
}
```

## 门面模式

简化接口

* 处理浏览器兼容性
* 简化常见的重复性任务
* 组合函数

### 便利方法

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
