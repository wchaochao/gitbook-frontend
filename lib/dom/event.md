# 事件

标签（空格分隔）： 前端知识体系

---

## 事件模型

发布订阅

```
EventTarget
    addListener
    removeListener
    用户操作或网络请求触发事件
listener
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
constructor (type, EventInit)

interface EventInit {
  bubbles: boolean,
  cancelable: boolean
}
```

### 事件属性

* type: 事件类型
* eventPhase: 事件阶段
 * `Event.NONE`: 0，事件未发生
 * `Event.CAPTURING_PHASE`: 1，捕获阶段
 * `Event.AT_TARGET`: 2，目标阶段
 * `Event.BUBBLING_PHASE`: 3，冒泡阶段

### 目标属性

* target: 真正触发事件的对象（srcElement历史遗留）
* currentTarget：当前执行的监听器绑定的对象
* composedPath(): 事件传播经过的所有节点，从target开始

### 冒泡属性

* bubbles: 是否可以冒泡
* stopPropagation(): 阻止事件传播，currentTarget剩下的监听器仍可以执行
* stopImmediatePropagation(): 立刻阻止事件传播，currentTarget剩下的监听器不可以再执行

### 默认行为属性

* cancelable: 是否可以取消事件默认行为
* preventDefault(): 取消默认行为（returnValue历史遗留）
* defaultPrevented(): 是否取消了默认行为

### 其他属性

* isTrusted: 是否是真实用户行为
* timestamp: 距timing.orgin的时间戳
* componsed: 是否是shadowDOM触发的事件
