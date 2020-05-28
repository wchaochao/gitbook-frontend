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

### 适合场所

* 在运行区间动态选择所用的类
* 多个小对象组合成的大对象
* 成员对象间存在设置耦合

xhr工厂

```javascript
onst Ajaxhandler = new Interface('AjaxHandler', ['createXhrObject', 'request'])

function SimpleHandler () {}
SimpleHandler.prototype = {
  createXhrObject () {
    let fn
    if (typeof XMLHttpRequest === 'function') {
      fn = function () {
        return new XMLHttpRequest()
      }
    } else if (typeof ActiveXObject === 'function') {
      fn = function () {
        return new ActiveXObject('MicroSoft.XMLHttp')
      }
    }

    this.createXhrObject = fn
    return fn()
  },
  request (method, url, callback, data) {
    const xhr = this.createXhrObject()
    xhr.onreadystatechange = function () {
      if (xhr.readystate !== 4) {
        return
      }

      if (xhr.status === 200) {
        callback.success(xhr.responseText, xhr.responseXML)
      } else {
        callbace.failure(xhr.status)
      }
    }

    xhr.open(method, url, true)
    xhr.send(method === 'POST' ? data : null)
  }
}

function QueuedHandler () {
  this.queue = []
  this.requestInProgress = false
  this.retryDelay = 5000
}
extend(QueueHandler, SimpleHandler)
Queued.prototype.request = function (method, url, callback, data, override) {
  if (this.requestInProgress && !override) {
    this.queue.push({
      method,
      url,
      callback,
      data
    })
  } else {
    this.requestInProgress = true
    const xhr = this.createXhrObject()
    xhr.onreadystatechange = function () {
      if (xhr.readystate !== 4) {
        return
      }

      if (xhr.status === 200) {
        callback.success(xhr.responseText, xhr.responseXML)
        this.advanceQueue()
      } else {
        callback.failure(xhr.status)
        setTimeout(() => {
          this.request(method, url, callback, data, true)
        }, this.retryDelay)
      }
    }
  }
}
Queued.prototype.advanceQueue = function () {
  if (this.queue.length === 0) {
    this.requestInProgress = false
    return
  }

  const req = this.queue.shift()
  this.request(req.method, req.url, req.callback, req.data, true)
}

function offlineHandler () {
  this.storedRequest = []
}
extend(offlineHandler, SimpleHandler)
OfflineHandler.prototype.request = function (method, url, callback, data) {
  if (XhrManager.isOffline()) {
    this.storedRequest.push({
      method,
      url,
      callback,
      data
    })
  } else {
    this.flushStoredRequest()
    OfflineHandler.super.request(method, url, callback, data)
  }
}
OfflineHandler.prototype.flushStoredRequest = function () {
  for (const req of this.storedRequest) {
    OfflineHandler.super.request(req.method, req.url, req.callback, req.data)
  }
}

const XhrManager = {
  createXhrHandler () {
    let xhr
    if (this.isOffline()) {
      xhr = new OfflineHandler()
    } else if (this.isHighLatency()) {
      xhr = new QueuedHandler()
    } else {
      xhr = new SimpleHandler()
    }

    Interface.ensureImplements(xhr, Ajaxhandler)
    return xhr
  },
  isOffline () {
    // ...
  },
  isHighLatency () {
    // ...
  }
}
```

RSS阅读器

```javascript
const DisplayModule = new Interface('DisplayModule', ['append', 'remove', 'clear'])

function ListDiaplay (id, parent) {
  this.list = document.createElement('ul')
  this.list.id = id
  parent.appendChild(this.list)
}

ListDisplay.prototype = {
  append (text) {
    const li = document.createElement('li')
    li.innerHTML = text
    this.list.appendChild(li)
    return li
  },
  remove (el) {
    this.list.removeChild(el)
  },
  clear (el) {
    this.list.innerHTML = ''
  }
}

const displayManager = {
  createDisplayModule (id, parent) {
    const displayModule = new ListDiaplay(id, parent)
    Interface.ensureImplements(displayModule, DisplayModule)

    return displayModule
  }
}

const conf = {
  id: 'cnn-top-stories',
  parent: $('feed-readers'),
  feedUrl: 'http://rss.cnn.com/rss/cnn_topstories.rss',
  updateInterval: 60000
}

function FeedReader (display, xhrHander, conf) {
  this.display = display
  this.xhrHander = xhrHander
  this.conf = conf

  this.startUpdates()
}

FeedReader.prototype = {
  fetchFeed () {
    this.xhrHander.request('GET', `feedProxy.php?feed=${this.conf.feedUrl}`, {
      success: (text, xml) => this.parseFeed(text, xml),
      failure: status => this.showError(status)
    })
  },
  parseFeed (responseText, responseXML) {
    this.display.clear()
    const items = responseXML.querySelectorAll('item')
    for (let i = 0, len = items.length; i < len; i++) {
      const title = items[i].querySelector('title')
      const link = items[i].qeurySelector('link')
      this.display.append(`<a href="${link.textContent}">${title.textContent}</a>`)
    }
  },
  showError (status) {
    this.display.clear()
    this.display.append('Error fetching feed.')
  },
  startUpdates () {
    this.fetchFeed()
    this.interval = setInterval(() => this.fetchFeed(), this.conf.updateInterval)
  },
  stopUpdates () {
    clearInterval(this.interval)
  }
}

const FeedManger = {
  createFeedReader = function (conf) {
    const displayModule = displayManager.createDisplayModule(`${conf.id}-display`, conf.parent)
    const xhrHander = xhrManger.createXhrHandler()

    return new FeedReader(displayModule, xhrHander, conf)
  }
}
```

## 桥接模式

将抽象与实现相分离，以便二者独立变化
