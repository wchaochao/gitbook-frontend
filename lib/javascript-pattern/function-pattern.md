# 函数式模式

标签（空格分隔）： 前端知识体系

---

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

## 命令模式

使用命令对象封装操作

```javascript
class Command {
  constructor (name) {
    this.name = name
  }

  execute () {
    // 执行操作
  }

  undo () {
    // 撤销操作
  }
}

class UndoManager {
  constructor () {
    this.stack = []
  }

  execute (command) {
    this.stack.push(command)
    command.execute()
  }

  undo () {
    const command = this.stack.pop()
    command.undo()
  }
}

class commandManager {
  constructor () {
    this.commands = {}
  }

  register (command) {
    this.commands.name = command
  }

  execute (commandName) {
    const command = this.commands[commandName]
    if (command) {
      command.execute()
    }
  }
}
```

## 观察者模式

对某个对象的状态进行观察，在其发生改变时得到通知

* 被观察者：订阅、退订、通知观察者
* 观察者：处理被观察者发送的数据

```javascript
class Observable {
  constructor () {
    this.subscribers = []
  }

  subscribe (subscriber) {
    if (this.subscribers.indexOf(subscriber) === -1) {
      this.subscribers.push(subscriber)
    }
    return this
  }

  unsubscribe (subscriber) {
    this.subscribers = this.subscribers.filter(item => item !== subscriber)
    return this
  }

  notify (...args) {
    this.subscribers.forEach(subscriber => {
      subscriber.apply(this, args)
    })
  }
}
```

## 发布订阅模式

* 发布者：监听、解绑、触发事件
* 订阅者：处理触发的事件

```javascript
class EventEmitter {
  constructor () {
    this.listeners = {}
  }

  on (type, listener) {
    if (!this.listeners[type]) {
      this.listeners[type] = []
    }

    if (this.listeners[type].indexOf(listener) === -1) {
      this.listeners[type].push(listener)
    }

    return this
  }

  off (type, listener) {
    if (arguments.length === 0) {
      this.listeners = {}
      return this
    }

    if (arguments.length === 1) {
      delete this.listeners[type]
      return this
    }

    if (!this.listeners[type]) {
      return this
    }

    this.listeners[type] = this.listeners[type].filter(item => item !== listener)
    return this
  }

  emit (type, ...args) {
    if (!this.listeners[type]) {
      return this
    }

    this.listeners[type].forEach(listener => {
      listener.apply(this, args)
    })

    return this
  }
}
```
