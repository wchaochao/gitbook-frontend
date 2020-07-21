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
