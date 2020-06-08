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
* 目标阶段：依次触发捕获监听器、冒泡监听器、on函数
* 冒泡阶段：事件能冒泡时，依次触发冒泡监听器、on函数

### 事件代理

在祖先节点上监听事件，代理后代节点的事件

* 减少内存消耗
* 动态添加的后代元素不用再监听事件

## Event接口

标志某件事发生

```javascript
interface Event {
  constructor(DOMString type, optional EventInit eventInitDict = {});

  readonly attribute DOMString type;
  readonly attribute boolean bubbles;
  readonly attribute boolean composed;
  readonly attribute boolean cancelable;

  readonly attribute EventTarget? target;
  readonly attribute EventTarget? currentTarget;
  sequence<EventTarget> composedPath();

  const unsigned short NONE = 0;
  const unsigned short CAPTURING_PHASE = 1;
  const unsigned short AT_TARGET = 2;
  const unsigned short BUBBLING_PHASE = 3;

  readonly attribute unsigned short eventPhase;
  void stop Propagation();
  void stopImmediatePropagation();

  void preventDefault();
  readonly attribute boolean defaultPrevented;

  readonly attribute boolean isTrusted;
  readonly attribute DOMHighResTimeStamp timeStamp;
}

dictionary EventInit {
  boolean bubbles = false;
  boolean composed = false;
  boolean cancelable = false;
}
```

### 内部属性

目标

* target: 事件触发的目标对象
* relatedTarget: 事件触发的相关目标对象
* touch target list: touch事件目标对象列表
* path: 事件传播信息列表，从target开始
* struct: 事件传播信息
 * invocation target: 传播到的目标对象
 * invocation-target-in-shadow-tree: invocation target是否在shadow tree里
 * shadow-adjusted target: 调整后的target对象
 * releatedTarget: relatedTarget相对invocation target进行retarget
 * touch target list: touch target list相对invocation target进行retarget
 * root-of-closed-tree: invocation target是否是closed shadow tree的root节点
 * slot-in-closed-tree: invocation target是否是closed shadow tree的slot节点

标志

* initialized flag
* composed flag
* dispatch flag
* stop propagation flag
* stop immediate propagation flag
* canceled flag
* in passive listener flag

### new Event(type[, eventInitDict])

创建Event对象

1. 创建Event对象
2. 初始化type、bubbles、composed、cancleable属性
3. 设置initialized flag、composed flag
4. 设置isTrusted、timestamp属性

### 初始化属性

* type: 事件类型
* bubbles: 事件是否可以冒泡
* composed: 事件是否可以冒泡通过阴影边界
* cancelable: 是否可以取消事件默认行为

### 目标属性

* target: event的target
 * target为Shadow Tree的非slotted节点时，跨越阴影边界时target会被重置为shadow host
* currentTarget：当前执行的监听器绑定的EventTarget对象
* composedPath(): 事件传播经过的所有节点，从target开始
 * shadow root的mode为closed时，跨越阴影边界后隐藏shadow root里的路径

### 传播行为

* eventPhase: 事件阶段
 * `Event.NONE`: 0，事件未发生
 * `Event.CAPTURING_PHASE`: 1，捕获阶段
 * `Event.AT_TARGET`: 2，目标阶段
 * `Event.BUBBLING_PHASE`: 3，冒泡阶段
* stopPropagation(): 阻止事件传播，currentTarget剩下的监听器仍可以执行
* stopImmediatePropagation(): 立刻阻止事件传播，currentTarget剩下的监听器不可以再执行

### 默认行为

* preventDefault(): 取消默认行为
 * cancelable为true且事件监听器的passive为false时才可以取消默认行为
* defaultPrevented: 是否取消了默认行为

### 其他属性

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

创建CustomEvent对象，比Event多初始化一个detail属性

### 特征属性

* detail: 自定义数据

## EventTarget接口

```javascript
interface EventTarget {
  constructor();

  void addEventListener(DOMString type, EventListener? callback, optional (AddEventListenerOptions or boolean) options = {});
  void removeEventListener(DOMString type, EventListener? callback, optional (AddEventListenerOptions or boolean) options = {});
  boolean dispatchEvent(Event event)
}

callback interface EventListener {
  void handleEvent(Event event);
}

dictionary EventListenerOptions {
  boolean capture = false;
}

dictionary AddEventListenerOptions: EventListenerOptions {
  boolean passive = false;
  boolean once = false;
}
```

### 内部属性

* event listener list: event listener列表
* event listener结构
 * type: 事件类型
 * callback: 事件回调
 * capture: 是否在捕获阶段触发，默认为false
 * passive: 是否不可取消默认行为，默认为false
 * once: 是否触发一次后移除，默认为false
 * removed: 是否已从列表中移除，默认为false

### new EventTarget()

创建EventTarget对象

### addEventListener(type, callback[, options])

添加事件监听器

1. callback为null时直接返回
2. 检查event listener是否在event listener list中（type, callback, capture是否都相同），不存在时添加

### removeEventListener(type, callback[, options])

移除事件监听器

1. 检查event listener是否在event listener list中（type, callback, capture是否都相同），存在时移除
2. 设置event listener的removed为true

### dispatchEvent(event)

触发事件

1. event未初始化或已触发时，抛出错误
2. 设置dispatch flag
3. 根据get the parent算法计算传播路径
 a. slotted的get the parent为slot
 b. shadow root的get the parent为shadow host
 c. 其他节点的get the parent为父节点
4. target的shadow-adjusted target为target本身
5. 当target的root不为parent的shadow-including inclusive ancestor时，设置shadow-adjusted target为parent
 a. shadow tree的非slotted节点触发事件时，在跨越阴影边界时shadow-adjusted target会被设为shadow root
 b. shadow tree的slotted节点触发事件时，在跨越阴影边界时shadow-adjusted target仍为null
6. path按照倒序触发invocation target的event listener
 a. shadow-adjusted target不为null时，eventPhase为AT_TARGET
 b. shadow-adjusted target为null时，eventPhase为CAPTURING_PHASE
7. path按照顺序触发invocation target的event listener
 a. shadow-adjusted target不为null时，eventPhase为AT_TARGET
 b. bubble为false时continue
 c. bubble不为false且shadow-adjusted target为null时，eventPhase为CAPTURING_PHASE
9. 触发invation target的event listener
 a. 设置target为前面最近的不为null的shadow-adjusted target
 b. 设置relatedTarget、touch target list为当前struct的relatedTarget、touch target list
 c. 设置了stop propagation flag时直接返回
 d. 设置currentTarget为invation target
 e. 根据type, capture查找符合条件的event listener
 f. once为true时，将event listener从event listeners list中移除
 g. passive为true时，设置in passive listener flag
 h. 执行event listener，参数为target，this为currentTarget
 i. stop immediate propagation flag为true时，直接返回event是否未阻止默认行为不继续执行剩下的event listener
 j. 其他情况执行完所有event listener后返回event是否未阻止默认行为
