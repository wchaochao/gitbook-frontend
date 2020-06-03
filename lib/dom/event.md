# 事件

标签（空格分隔）： 前端知识体系

---

## 事件模型

发布订阅模式

```
EventTarget
    addEventListener()
    removeEventListener()
    dispatchEvent(event)
Event listeners
```

### 事件传播

![事件传播](https://www.w3.org/TR/DOM-Level-3-Events/images/eventflow.svg)

* 捕获阶段：触发捕获监听器
* 目标阶段：按订阅顺序触发捕获监听器、冒泡监听器、on函数
* 冒泡阶段：事件能冒泡时，按订阅顺序触发冒泡监听器、on函数

### 事件代理

在祖先节点上监听事件，代理后代节点的事件

* 减少内存消耗
* 动态添加的后代元素不用再监听事件

## Event接口

```javascript
interface Event {
  constructor(DOMString type, optional EventInit eventInitDict = {});

  readonly attribute DOMString type;
  readonly attribute EventTarget? target;
  readonly attribute EventTarget? currentTarget;
  sequence<EventTarget> composedPath();

  const unsigned short NONE = 0;
  const unsigned short CAPTURING_PHASE = 1;
  const unsigned short AT_TARGET = 2;
  const unsigned short BUBBLING_PHASE = 3;

  readonly attribute boolean bubbles;
  readonly attribute unsigned short eventPhase;
  void stop Propagation();
  void stopImmediatePropagation();

  readonly attribute boolean cancelable;
  void preventDefault();
  readonly attribute boolean defaultPrevented;

  readonly attribute boolean composed;
  readonly attribute boolean isTrusted;
  readonly attribute DOMHighResTimeStamp timeStamp;
}

dictionary EventInit {
  boolean bubbles = false;
  boolean cancelable = false;
  boolean composed = false;
}
```

### 内部属性

目标

* target: 事件触发的目标
* relatedTarget: 事件触发的相关目标
* touch target list: touch事件目标列表
* path: 事件传播路径，从target开始

```
// path, a list of struct
<<invocation target, invocation-target-in-shadow-tree, shadow-adjusted target, relatedTarget, touch target list, root-of-closed-tree, slot-in-closed-tree>>
```

标志

* initialized flag
* dispatch flag
* stop propagation flag
* stop immediate propagation flag
* canceled flag
* in passive listener flag
* composed flag

### new Event(type[, eventInitDict])

创建Event对象，初始化type、bubbles、cancleable

### 特征属性

* type: 事件类型
* target: event的target
* currentTarget：当前执行的监听器绑定的对象
* composedPath(): 事件传播经过的所有节点，从target开始

### 传播行为

* bubbles: 是否可以冒泡
* eventPhase: 事件阶段
 * `Event.NONE`: 0，事件未发生
 * `Event.CAPTURING_PHASE`: 1，捕获阶段
 * `Event.AT_TARGET`: 2，目标阶段
 * `Event.BUBBLING_PHASE`: 3，冒泡阶段
* stopPropagation(): 阻止事件传播，currentTarget剩下的监听器仍可以执行
* stopImmediatePropagation(): 立刻阻止事件传播，currentTarget剩下的监听器不可以再执行

### 默认行为

* cancelable: 是否可以取消事件默认行为
* preventDefault(): 取消默认行为
* defaultPrevented: 是否取消了默认行为

### 其他属性

* composed: 是否是shadowDOM触发的事件
* isTrusted: 是否是user agent触发的事件
* timestamp: 距timing.orgin的时间戳

## CustomEvent接口

携带自定义数据的Event

```javascript
interface CustomEvent: Event {
  constructor(DOMString type, optional CustomEventInit eventInitDIct = {})

  readonly attribute any detail
}

dictionary CustomEventInit: EventInit {
  any detail = null;
}
```

### new CustomEvent(type[, eventInitDict])

创建CustomEvent对象，初始化type、bubbles、cancleable、detail

### 特征属性

* detail: 自定义数据
