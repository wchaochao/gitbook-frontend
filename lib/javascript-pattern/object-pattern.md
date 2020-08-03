# 对象式模式

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
  attribute1: true

  method1 () {
    // ...
  }

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

  function privateMethod1 () {
    // ...
  }

  return {
    publicAttribute1: true

    publicMethod1 () {
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

共享模块

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
    const bicycle = this.createBicycle(model)

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

## 代理模式

代理本体

* 使用代理对象包装本体，控制对本体的访问
* 代理对象与本体实现相同的接口，代理对象执行接口时调用本体的同名接口

### 虚拟代理

将本体的实例化推迟到需要的时候

```javascript
class LibraryVirtualProxy {
  constructor (catalog) {
    this.catalog = catalog
    this.library = null
  }

  _init () {
    if (!this.library) {
      this.library = new Library(this.catalog)
    }
  }

  findBooks (searchString) {
    this._init()
    return this.library.findBooks(searchString)
  }
}
```

### 远程代理

将网络请求当作本地方法处理

```javascript
const StatsProxy = {
  getPageViews (callback, startDate, endData, page) {
    fetchData(urls.pageviews, callback, startDate, endData, page)
  },

  getUniques (callback, startDate, endData, page) {
    fetchData(urls.uniques, callback, startDate, endData, page)
  }
}
```

## 装饰者模式

装饰对象

### 类装饰者

* 使用装饰对象包装组件，装饰对象对组件的接口进行装饰
* 装饰对象与组件实现相同的接口，装饰对象执行接口时执行自定义操作，可以选择调用组件的同名接口
* 装饰对象可以被另一个装饰对象装饰

```javascript
class BicycleDecorator {
  constructor (bicycle) {
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

给原函数添加新功能

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

组合对象

* 使用组合对象包装一组子对象，组合对象与子对象组形成树结构
* 组合对象与子对象实现相同的接口，组合对象执行接口时调用所有子对象的同名接口

```javascript
class Composite {
  constructor () {
    this.children = []
  }

  add (child) {
    this.children.push(child)
  }

  display () {
    this.children.forEach(child => child.display())
  }
}
```

## 享元模式

将大量类似对象转换为少量共享对象

* 将对象的内部状态划分为内在数据和外在数据
 * 内在数据组成享元对象，使用工厂控制享元对象的实例化
 * 外在数据由管理器对象保存并将其提供给享元实例的方法

### 单体控制

```javascript
class Tooltip {
  constructor () {
    this.timer = null
    this.delay = 1500

    this.element = document.createElement('div')
    this.element.className = 'tooltip'
    this.element.style.position = 'absolute'
    this.element.style.display = 'none'
    document.body.appendChild(this.element)
  }

  startDelay (e, text) {
    if (!this.timer) {
      this.timer = setTimeout(() => {
        this.show(e.clientX, e.clientY, text)
      }, this.delay)
    }
  }

  show (x, y, text) {
    clearTimeout(this.timer)
    this.timer = null
    this.element.innerHTML = text
    this.element.style.left = x + 'px'
    this.element.style.top = y + 20 + 'px'
    this.element.style.display = 'block'
  }

  hide () {
    clearTimeout(this.timer)
    this.timer = null
    this.element.style.display = 'none'
  }
}

const TooltipManager = (function () {
  const instance = null

  return {
    getTooltip () {
      if (!instance) {
        instance = new Tooltip()
      }
      return instance
    },
    addTooltip (target, text) {
      const tooltip = this.getTooltip()

      target.addEventListener('mouseover', e => tooltip.startDelay(e, text))
      target.addEventListener('mouseout', e => tooltip.hide())
    }
  }
})()
```

### 缓存控制

```javascript
class Car {
  constructor (make, model, year) {
    this.make = make
    this.model = model
    this.year = year
  }
}

const CarFactory = (function() {
  const createdCars = {}

  return {
    createCar (make, model, year) {
      const key = `${make}-${model}-${year}`
      if (!createdCars[key]) {
        const car = new Car(make, model, year)
        createdCars[key] = car
      }
      return createdCars[key]
    }
  }
})()

const CarRecordManager = (function () {
  const carRecordDataBase = {}

  return {
    addCarRecord (make, model, year, owner, tag, renewDate) {
      const car = CarFactory.createCar(make, model, year)
      carRecordDataBase[tag] = {
        owner,
        renewDate,
        car
      }
    }
  }
})()
```

### 对象池控制

```javascript
class DialogBox {
  show (header, body, footer) {
    // ...
  }

  hide () {
    // ...
  }

  state () {
    // ...
  }
}

const DialogBoxManager = (function () {
  const created = []

  return {
    getDialogBox () {
      for (const box of created) {
        if (box.state() === 'hidden') {
          return box
        }
      }

      const dialogBox = new DialogBox()
      created.push(dialogBox)
      return dialogBox
    },
    displayDialogBox (header, body, footer) {
      const dialogBox = getDialogBox()
      dialogBox.show(header, body, footer)
    }
  }
})()
```
