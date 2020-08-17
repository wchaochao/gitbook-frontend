# 选区

标签（空格分隔）： 前端知识体系

---

## 介绍

表示node tree中两个boundary point之间的内容，左闭右开

### 组成

* start: 开始的bounary point
* end: 结束的bounary point

### 分类

* static range: 静态range，node tree变动时static range不变
* living range: 动态range，node tree变动时发生相应的变化，保证living range的内容不变

## bounary point

边界点，由node、offset组成的元组

* node: 非DocumentType、Attr节点
* offset: 偏移量，[0, node length]

### 位置比较

```
1. node相同时，比较offset，相同时返回0
2. nodeA在nodeB前
 a. nodeB为nodeA的后代节点，比较nodeB的祖先节点在nodeA的索引和offsetA，小于offsetA返回1，其他返回-1
 b. nodeB不为nodeA的后代节点，返回-1
3. nodeA在nodeB后，相当于-（nodeA在nodeB前）
```

## AbstractRange接口

```javascript
interface AbstractRange {
  readonly attribute Node startContainer;
  readonly attribute unsigned long startOffset;
  readonly attribute Node endContainer;
  readonly attribute unsigned long endOffset;
  readonly attribute boolean collapsed;
}
```

### 内部属性

* start: 开始边界点
* end: 结束边界点
* collapsed: start和end是否为同一位置

### 边界属性

* startContainer: range的start node
* startOffset: range的start offset
* endContainer: range的end node
* endOffset: range的end offset
* collapsed: range的collapsed

## StaticRange接口

```javascript
interface StaticRange: AbstractRange {
  constructor(StaticRangeInit init);
}

dictionary StaticRangeInit {
  required Node startContainer;
  required unsigned long startOffset;
  required Node endContainer;
  required unsigned long endOffset;
}
```

### new StaticRange()

创建静态Range

```
1. 创建StaticRange对象
2. 初始化startContainer、startOffset、endContainer、endOffset属性
```

## Range接口

```javascript
interface Range: AbstractRange {
  constructor();

  readonly attribute Node commonAncestorContainer;

  void setStart(Node node, unsigned long offset);
  void setEnd(Node node, unsigned long offset);
  void setStartBefore(Node node);
  void setStartAfter(Node node);
  void setEndBefore(Node node);
  void setEndAfter(Node node);
  void collapse(optional boolean toStart = false);
  void selectNode(Node node);
  void selectNodeContents(Node node);

  boolean isPointInRange(Node node, unsigned long offset);
  short comparePoint(Node node, unsigned long offset);
  boolean intersectsNode(Node node);

  const unsigned short START_TO_START = 0;
  const unsigned short START_TO_END = 1;
  const unsigned short END_TO_END = 2;
  const unsigned short END_TO_START = 3;

  short compareBoundaryPoints(unsigned short how, Range sourceRange)

  void deleteContents();
  DocumentFragment extractContents();
  DocumentFragment cloneContents();
  void insertNode(Node node);
  void surroundContents(Node newParent);

  Range cloneRange();
  void detach();
  String toString();

  DOMRect getBoundingClientRect()
  DOMRectList getClientRects()
}
```

### 术语

* live range root: start node的root节点
* contained: node在range内
* partially contained: node为start node或end node的inclusive ancestor

### new Range()

创建动态Range

```
1. 创建Range对象
2. 设置start和end为(document, 0)
```

### 祖先属性

* commonAncestorContainer: start node和end node的最近的共同的inclusive ancestor

### 设置边界点

```
1. node为doctype节点，抛出错误
2. offset超过node length, 抛出错误
3. 设置开始边界点，start设为(node, offset), 若start在end后，则end也设为start
4. 设置结束边界点，end设为(node, offset), 若end在start前，则start也设为end
```

### 设置操作

* setStart(node, offset): 设置start为(node, offset)
* setEnd(node, offset): 设置end为(node, offset)
* setStartBefore(node): 设置start为(parent, node index)
* setStartAfter(node): 设置start为(parent, node index + 1)
* setEndBefore(node): 设置end为(parent, node index)
* setEndAfter(node): 设置end为(parent, node index + 1)
* collapse(toStart): 折叠range
 * toStart为true时，设置end为start
 * toStart为false时，设置start为end
* selectNode(node): 设置start为(parent, node index)，设置end为(parent, node index + 1)
* selectNodeContents(node): 设置start为(node, 0)，设置end为(node, length)

### 比较操作

* isPointInRange(node, offset): 边界点(node, offset)是否在[start, end]中
* comparePoint(node, offset): 比较边界点(node, offset)和range
 * 边界点在start前，返回-1
 * 边界点在[start, end]中，返回0
 * 边界点在end后，返回1
* intersectsNode(node): node和range是否有交集，
 * (parent, node index) before end且(parent, node index + 1) after start时返回true
 * 其他返回false
* compareBoundaryPoints(how, sourceRange): 比较range和sourceRange的边界点
 * Range.START_TO_START: 比较range的start和sourceRange的start
 * Range.START_TO_END: 比较range的end和sourceRange的start
 * Range.END_TO_END: 比较range的end和sourceRange的end
 * Range.END_TO_START: 比较range的start和sourceRange的end

### 内容操作

* deleteContents(): 删除选区内容
* extractContents(): 剪切选区内容
* cloneContents(): 复制选区内容
* surroundContents(newParent):选区内容被newParent包围
* insertNode(node): 在start位置插入节点

### 选区操作

* cloneRange(): 克隆选区
* detach(): 无需做什么，用于向后兼容
* toString(): 返回选区内容中的所有文本

### 视口操作

* getBoundingClientRect(): 获取选区的视口信息
* getClientRects(): 获取选区中所有盒模型的视口信息
