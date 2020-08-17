# 中断

标签（空格分隔）： 前端知识体系

---

## AbortSignal接口

中断信号

```javascript
interface AbortSignal: EventTarget {
  readonly attribute boolean aborted;
  attribute EventHandler onabort;
}
```

### 内部属性

* aborted flag: 中断标志
* abort algorithms: 中断算法，默认为空Set

### 属性

* aborted: signal的aborted flag
* onabort: onabort事件

### signal abort

中断signal

```
1. signal的aborted flag已设置时，直接返回
2. 设置aborted flag
3. 触发abort事件
```

## AbortController接口

中断控制器

```javascript
interface AbortController {
  constructor();

  readonly attribute AbortSignal signal;
  void abort()
}
```

### 内部属性

* signal: 中断对象

### new AbortController()

创建中断控制器

```
1. 创建AbortController对象
2. 设置signal属性为AbortSignal对象
```

### 属性

* signal: controller的signal
* abort(): 触发signal的中断

## 应用

* 接收AbortSignal参数确定中断后执行什么操作
* 执行abort()中断AbortSignal

```javascript
const controller = new AbortController()
const signal = controller.signal

function doAmazingness({signal}) {
  if (signal.aborted) {
    return Promise.reject(new DOMException('Aborted', 'AbortError'))
  }

  return new Promise((resolve, reject) => {
    signal.addEventListener('abort', () => {
      reject(new DOMException('Aborted', 'AbortError'))
    })
  })
}

doAmazingness({signal}).catch(err => {
  if (err.name === 'AbortError') {
    return
  }
})

controller.abort()
```
