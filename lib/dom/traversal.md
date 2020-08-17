# 树遍历

标签（空格分隔）： 前端知识体系

---

使用NodeIterator和TreeWalker遍历节点树

## NodeIterator接口

```javascript
interface NodeIterator {
  readonly attribute Node root;
  readonly attribute unsigned long whatToShow;
  readonly attribute NodeFilter? filter;
  readonly attribute Node referenceNode;
  readonly attribute boolean pointerBeforeReferenceNode;

  Node? nextNode();
  Node? previousNode();

  void detach();
}
```

### 内部属性

* iterator collection：根节点及其所有后代节点的集合
* root: 遍历的根节点
* whatToShow: 内置过滤器，遍历哪类节点
* filter: 自定义过滤器，遍历符合条件的节点
* referenceNode: 正在遍历的节点
* pointerBeforeReferenceNode: 是否是往前遍历

### 创建

使用document.createNodeIterator()创建

```javascript
const nodeIterator = document.createNodeIterator(document.body, NodeFilter.SHOW_ELEMENT, node => {
  return node.tagName === 'P' ? NodeFilter.FILTER_ACCEPT : NodeFilter_REJECT
})
```

### 遍历

往后遍历

```
1. pointerBeforeReferenceNode为true时置为false，从referenceNode节点开始查找符合条件的节点
2. pointerBeforeReferenceNode为false时，从referenceNode的下一个节点开始查找符合条件的节点
```

往前遍历

```
1. pointerBeforeReferenceNode为false时置为true，从referenceNode节点开始查找符合条件的节点
2. pointerBeforeReferenceNode为true，从referenceNode的上一个节点开始查找符合条件的节点
```

### 特征属性

* root: nodeIterator的root
* whatToShow: nodeIterator的whatToShow
* filter: nodeIterator的filter
* referenceNode: nodeIterator的referenceNode
* pointerBeforeReferenceNode: nodeIterator的pointerBeforeReferenceNode

### 操作

* nextNode(): 返回下一个符合条件的节点
* previousNode(): 返回上一个符合条件的节点
* detach(): 不做任何处理（用于兼容）

## TreeWalker接口

```javascript
interface TreeWalker {
  readonly attribute Node root;
  readonly attribute unsigned long whatToShow;
  readonly attribute NodeFilter? filter;
  attribute Node currentNode;

  Node? parentNode();
  Node? firstChild();
  Node? lastChild();
  Node? previousSibling();
  Node? nextSibling();
  Node? previousNode();
  Node? nextNode();
}
```

### 内部属性

* root: 遍历的根节点
* whatToShow: 内置过滤器，遍历哪类节点
* filter: 自定义过滤器，遍历符合条件的节点
* current: 正在遍历的节点

### 创建

使用document.createTreeWalker()创建

```javascript
const treeWalker = document.createTreeWalker(document.body, NodeFilter.SHOW_ELEMENT, node => {
  return node.tagName === 'P' ? NodeFilter.FILTER_ACCEPT : NodeFilter_REJECT
})
```

### 特征属性

* root: nodeIterator的root
* whatToShow: nodeIterator的whatToShow
* filter: nodeIterator的filter
* currentNode: nodeIterator的current

### 遍历操作

#### parentNode()

返回符合条件的父节点

```
1. 检测current的parent
2. 过滤器返回FILTER_ACCEPT时，返回该parent
3. 过滤器不返回FILTER_ACCEPT时，继续检测parent的parent
```

#### firstChild()

返回符合条件的第一个子节点

```
1. 检测current的first child
2. 过滤器返回FILTER_ACCEPT，返回该first child
3. 过滤器返回FILTER_SKIP时
 a. first child有first child时，继续检测first child的first child
 b. first child没有first child时，相当于FILTER_REJECT
4. 过滤器返回FILTER_REJECT
 a. first child的next sibling存在，继续检测next sibling
 b. first child的next sibling不存在，继续检测parent的next sibling
```

#### lastChild()

返回符合条件的最后一个子节点

```
1. 检测current的last child
2. 过滤器返回FILTER_ACCEPT，返回该last child
3. 过滤器返回FILTER_SKIP时
 a. last child有last child时，继续检测last child的last child
 b. last child没有last child时，相当于FILTER_REJECT
4. 过滤器返回FILTER_REJECT
 a. last child的previous sibling存在，继续检测previous sibling
 b. last child的previous sibling不存在，继续检测parent的previous sibling
```

#### previousSibling()

返回符合条件的上一个兄弟节点

```
1. 检测current的previous sibling，previous sibling不存在时相当于FILTER_REJECT
2. 过滤器返回FILTER_ACCEPT时，返回该previous sibling
3. 过滤器返回FILTER_SKIP时
 a. previous sibling有last child时，继续检测previous sibling的last child
 b. previous sibling没有last child时，相当于FILTER_REJECT
4. 过滤器返回FILTER_REJECT时
 a. previous sibling的previous sibling存在，继续检测previous sibling的previous sibling
 b. previous sibling的previous sibling不存在，继续检测parent的previous sibling
```

#### nextSibling()

返回符合条件的下一个兄弟节点

```
1. 检测current的next sibling，next sibling不存在时相当于FILTER_REJECT
2. 过滤器返回FILTER_ACCEPT时，返回该next sibling
3. 过滤器返回FILTER_SKIP时
 a. next sibling有first child时，继续检测next sibling的fist child
 b. next sibling没有first child时，相当于FILTER_REJECT
4. 过滤器返回FILTER_REJECT时
 a. next sibling的next sibling存在，继续检测next sibling的next sibling
 b. next sibling的next sibling不存在，继续检测parent的next sibling
```

#### previousNode()

返回符合条件的上一个节点

```
1. 检测current的previous sibling，previous sibling不存在时相当于FILTER_REJECT
2. 过滤器返回不为FILTER_REJECT时
 a. previous sibling有last child时，继续检测previous sibling的last child
 b. previous sibling没有last child时
   i. 过滤器返回FILTER_ACCEPT时，返回检测的节点
   ii. 过滤器返回FILTER_SKIP时，相当于FILTER_REJECT
4. 过滤器返回FILTER_REJECT时
 a. previous sibling的previous sibling存在，继续检测previous sibling的previous sibling
 b. previous sibling的previous sibling不存在，继续检测parent的previous sibling
```

#### nextNode()

返回符合条件的下一个节点

```
1. 检测current的first child，first child不存在时相当于FILTER_REJECT
2. 过滤器返回FILTER_ACCEPT时，返回检测的节点
3. 过滤器返回FILTER_SKIP时
 a. first child有first child时，继续检测first child的first child
 b. first child没有first child时，相当于FILTER_REJECT
4. 过滤器返回为FILTER_REJECT时
 a. first child有next sibling时，继续检测next sibling
 b. first child没有next sibling时，继续检测parent的next sibling
```

## NodeFilter接口

```javascript
callback interface NodeFilter {
  const unsigned short FILTER_ACCIPT = 1;
  const unsigned short FILTER_REJECT = 2;
  const unsigned short FILTER_SKIP = 3;

  const unsigned long SHOW_ALL = 0xFFFFFFFF;
  const unsigned long SHOW_ELEMENT = 0x1;
  const unsigned long SHOW_ATTRIBUTE = 0x2;
  const unsigned long SHOW_TEXT = 0x4;
  const unsigned long SHOW_CDATA_SECTION = 0x8;
  const unsigned long SHOW_PROCESSING_INSTRUCTION = 0x40;
  const unsigned long SHOW_COMMENT = 0x80;
  const unsinged long SHOW_DOCUMENT = 0x100;
  const unsigned long SHOW_DOCUMENT_TYPE = 0x200;
  const unsigned long SHOW_DOCUMENT_FRAGMENT = 0x400;

  unsigned short acceptNode(Node node);
}
```

### filter静态属性

* FILTER_ACCIPT: 接受节点
* FILTER_REJECT: 拒绝节点
* FILTER_SKIP: 跳过节点

### whatToShow静态属性

* SHOW_ALL: 显示所有节点
* SHOW_ELEMENT: 显示Element节点
* SHOW_ATTRIBUTE: 显示Attr节点
* SHOW_TEXT: 显示Text节点
* SHOW_CDATA_SECTION: 显示CDATA节点
* SHOW_PROCESSING_INSTRUCTION: 显示ProcessingInstruction节点
* SHOW_COMMENT: 显示Comment节点
* SHOW_DOCUMENT: 显示Document节点
* SHOW_DOCUMENT_TYPE: 显示DocumentType节点
* SHOW_DOCUMENT_FRAGMENT: 显示DocumentFragment节点
