# 节点监控

标签（空格分隔）： 前端知识体系

---

每个节点都有一个registered observer list，用于监控节点变化

## RegisteredObserver

在节点上注册的观察者

```javascript
interface RegisteredObserver {
  attribute MutationObserver observer;
  attribute MutationObserverInit options;
}
```

### 属性

* observer: 变动观察者，强引用
* options: 变动观察配置

## MutationObserver

变动观察者

```javascript
interface MutationObserver {
  constructor(MutationCallback callback);

  void observe(Node target, optional MutationObserverInit options = {});
  void disconnect();
}
```

### 内部属性

* callback: 变动观察回调，实例化时设置
* node list: 观察的节点列表，弱引用
* record queue: 变动记录队列

### MutationObserver(callback)

实例化

1. 创建一个MutationObserver对象
2. 设置callback为传入的参数
3. 将创建的MutationObserver对象放入Mutation observers队列中

### observer(target, options)

观察节点

1. 检验观察配置
2.  如果observer已被注册进target的观察者列表，设置变动观察配置为option
3. 如果没有，将observer, options注册进target的观察者列表，将target加入observer的node list列表

### disconnect()

解除观察

1. 遍历node list列表，移除已注册的observer
2. record queue置空

### takeRecords()

取出观察记录

* 克隆一份record queue
* record queue置空

## MutationCallback

变动观察回调

```javascript
callback MutationCallback = void (sequence<MutationRecord> mutations, MutationObserver observer);
```

### 参数

* mutations: 变动记录数组
* observer: 变动观察者本身

## MutationObserverInit

变动观察配置

```javascript
dictionary MutationObserverInit {
  boolean attributes;
  boolean attributeOldValue;
  sequence<DOMString> attributeFilter;
  boolean characterData;
  boolean characterDataOldValue;
  boolean childList = false;
  boolean subtree = false;
}
```

### 配置

* attributes: 是否观察元素节点的属性变化
* attributeOldValue: 是否记录旧属性值
* attributeFilter: 要观察的属性名列表
* characterData: 是否观察CharacterData节点的data变化
* characterDataOldValue: 是否记录旧data值
* childList: 是否观察子节点的变化（添加、删除、修改）
* subtree: 是否观察节点及其后代节点的变化

### 校验

* 设置了attributeOldValue或attributeFilter时，attributes省略（会被设为true）或为true
* 设置了characterDataOldValue时，characterData省略（会被设为true）或为true
* attributes、characterData、childList至少一个为true

## MutationRecord

变动记录

```javascript
interface MutationRecord {
  readonly attribute DOMString type;
  readonly attribute Node target;
  readonly attribute DOMString? attributeName;
  readonly attribute DOMString? attributeNamespace;
  readonly attribute DOMString? oldValue;
  readonly attribute NodeList addedNodes;
  readonly attribute NodeList removedNodes;
  readonly attribute Node? previousSibling;
  readonly attribute Node? nextSibling;
}
```

### 属性

* type: 变动类型
 * attributes: 属性变动
 * characterData: CharacterData变动
 * childList: 树变动（插入、删除、替换节点）
* target: 变动目标节点
 * 属性变动：属性所在的元素节点
 * CharacterData变动：CharacterData节点
 * 树变动：变动节点的父节点
* attributeName: 属性变动时变动的属性名
* attributeNamespace: 属性变动时变动属性的命名空间
* oldValue: 属性变动或CharacterData变动时记录的旧值
* addedNodes: 插入的节点列表
* removedNodes: 移除的节点列表
* previousSibling: 树变动节点的前一个兄弟节点
* nextSibling: 树变动节点的后一个兄弟节点

## 触发

1. 遍历变动的节点及其祖先节点，将MutationRecord存入符合的MutationObserver的record queue中
2. 生成一个mutation observer微任务，放入微任务队列中（已经有mutation observer微任务时不用再生成）
2. mutation observer微任务执行时从mutation observers队列中找出有变动记录的mutation observer，执行回调
