# 事件

标签（空格分隔）： 前端知识体系

---

## 事件模型

发布订阅模式

```
EventTarget
    addEventListener(type, listener)
    removeEventListener(type, listener)
    dispatchEvent(event)
```

### 事件传播

![事件传播](https://www.w3.org/TR/DOM-Level-3-Events/images/eventflow.svg)

* 捕获阶段：触发捕获监听器
* 目标阶段：按注册顺序触发捕获监听器、冒泡监听器、on函数
* 冒泡阶段：事件能冒泡时，按注册顺序触发冒泡监听器、on函数

### 事件代理

在祖先节点上监听事件，代理后代节点的事件

* 减少内存消耗
* 动态添加的后代元素不用再监听事件

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
* get the parent：获取父EventTarget
 * slottable节点的父EventTarget为slot节点
 * event的composed flag为true时shadow root节点的父EventTarget为shadow host
 * event的type不为load时document节点的父EventTarget为全局对象
 * 其他node节点的父EventTarget为父节点
* activation behavior：默认行为

### new EventTarget()

创建EventTarget对象

### addEventListener(type, callback[, options])

添加事件监听器

```
1. callback为null时直接返回
2. 检查event listener是否在event listener list中（type, callback, capture是否都相同），不存在时添加
```

### removeEventListener(type, callback[, options])

移除事件监听器

```
1. 检查event listener是否在event listener list中（type, callback, capture是否都相同），存在时移除并设置removed属性为true
```

### dispatchEvent(event)

触发事件

```
1. event未初始化或已触发时，抛出错误
2. 设置event的dispatch flag为true，isTrusted属性为false
3. 根据get the parent算法计算event的path属性
  a. target的shadow-adjusted target为target本身
  b. 上一个shadow-adjusted target不为slottable节点时shadow host的shadow-adjusted target为shadow host本身
  c. 其他EventTarget的shadow-adjusted target为null
4. path按照倒序触发invocation target的event listener
 a. shadow-adjusted target不为null时，设置event的eventPhase属性为AT_TARGET
 b. shadow-adjusted target为null时，设置event的eventPhase属性为CAPTURING_PHASE
5. path按照顺序触发invocation target的event listener
 a. shadow-adjusted target不为null时，设置event的eventPhase属为AT_TARGET
 b. bubble为false时continue
 c. 且shadow-adjusted target为null且bubble不为false时，eventPhase为BUBBLING_PHASE
6. 触发invation target的event listener
 a. 设置target为前面最近的不为null的shadow-adjusted target
 b. 设置relatedTarget、touch target list为当前struct的relatedTarget、touch target list
 c. 设置了stop propagation flag时直接返回
 d. 设置currentTarget为invation target
 e. 根据type, capture查找符合条件的event listener
 f. once为true时，将event listener从event listeners list中移除
 g. passive为true时，设置in passive listener flag为true
 h. 执行event listener，参数为target，this为currentTarget
 i. stop immediate propagation flag为true时，直接返回event是否未阻止默认行为，不继续执行剩下的event listener
 j. 其他情况执行完所有event listener后返回event是否未阻止默认行为
```

## Event接口

标志某件事发生

```javascript
interface Event {
  constructor(DOMString type, optional EventInit eventInitDict = {});

  readonly attribute DOMString type;
  readonly attribute boolean composed;
  readonly attribute boolean bubbles;
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
  boolean composed = false;
  boolean bubbles = false;
  boolean cancelable = false;
}
```

### 内部属性

目标

* target: 事件触发的目标对象
 * target为Shadow Tree的非slotted节点时，跨越阴影边界时target会被重置为shadow host
* relatedTarget: 事件触发的相关目标对象
* touch target list: touch事件目标对象列表
* path: 事件传播信息列表，从target开始
* struct: 事件传播信息
 * invocation target: 传播到的目标对象
 * invocation-target-in-shadow-tree: invocation target是否在shadow tree里
 * root-of-closed-tree: invocation target是否是closed shadow tree的root节点
 * slot-in-closed-tree: invocation target是否是closed shadow tree的slot节点
 * shadow-adjusted target: 调整后的target对象
 * releatedTarget: relatedTarget相对invocation target进行retarget（relatedTarget和invocation target在一个ShadowTree下重置为shadow host）
 * touch target list: touch target list相对invocation target进行retarget

标志

* initialized flag：初始化标志
* composed flag：能否跨越阴影边界标志
* dispatch flag：触发标志
* stop propagation flag：阻止传播标志
* stop immediate propagation flag：立即阻止传播标志
* canceled flag：取消标志
* in passive listener flag：在当前listener不可取消标志

### new Event(type[, eventInitDict])

创建事件

```
1. 创建Event对象
2. 初始化type、composed、bubbles、cancleable属性
3. 设置initialized flag、composed flag
4. 设置isTrusted、timestamp属性
```

### 初始化属性

* type: 事件类型
* composed: 事件是否可以传播通过阴影边界
* bubbles: 事件是否可以冒泡
* cancelable: 事件是否可以取消默认行为

### 目标属性

* target: event的target
* currentTarget：当前执行的监听器绑定的EventTarget对象
* composedPath(): 事件传播经过的所有节点，从target开始
 * shadow root的mode为closed时，隐藏shadow root里的路径

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

自定义事件

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

创建自定义事件

```
1. 创建CustomEvent对象
2. 比Event多初始化一个detail属性
```

### 特征属性

* detail: 自定义数据
