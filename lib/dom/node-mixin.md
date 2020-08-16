# 节点混合

标签（空格分隔）： 前端知识体系

---

## ParentNode接口

父节点接口

```javascript
interface mixin ParentNode {
  readonly attribute HTMLCollection children;
  readonly attribute unsigned long childElementCount;
  readonly attribute Element? firstElementChild;
  readonly attribute Element? lastElementChild;

  Element? querySelector(DOMString selectors);
  NodeList querySelectorAll(DOMString selectors);

  void prepend((Node or DOMString)... nodes);
  void append((Node or DOMString)... nodes);
}
```

### 子元素属性

* children: 子元素节点集合
* childElementCount: 子元素节点个数
* firstElementChild: 第一个子元素节点
* lastElementChild: 最后一个子元素节点

### 查找节点

* querySelector(selectors): 根据选择器查找第一个匹配的元素节点
* querySelectorAll(selectors): 根据选择器查找匹配的元素节点集合

### 追加节点

* prepend(...nodes): 在第一个子节点前追加多个节点，字符串被自动处理为文本节点
* append(...nodes): 在最后一个子节点后追加多个节点，字符串被自动处理为文本节点

## NonElementParentNode接口

非Element的父节点接口

```javascript
interface mixin NonElementParentNode {
  Element? geElementById(DOMString elementId);
}
```

### 查找节点

* getElementById(elementId): 根据id查找元素节点

## ChildNode接口

子节点接口

```javascript
interface mixin ChildNode {
  void before((Node or DOMString)... nodes);
  void after((Node or DOMString)... nodes);
  void remove();
  void replaceWith((Node or DOMString)... nodes);
}
```

### 节点操作

* before(...nodes): 在节点前插入多个节点，字符串被自动处理为文本节点
* after(...nodes): 在节点后插入多个节点，字符串被自动处理为文本节点
* remove(): 删除节点
* replaceWith(...nodes): 用多个节点替换当前节点，字符串被自动处理为文本节点

## NonDocumentTypeChildNode接口

非DocumentType的子节点接口

```javascript
interface mixin NonDocumentTypeChildNode {
  readonly attribute Element? previousElementSibling
  readonly attribute Element? nextElementSibling
}
```

### 兄弟元素属性

* previousElementSibling: 上一个兄弟元素节点
* nextElementSibling: 下一个兄弟元素节点

## DocumentOrShadowRoot接口

Document和ShadowRoot的共有接口

```javascript
interface mixin DocumentOrShadowRoot {
  // 由具体的标记语言实现
}
```

## Slottable接口

```javascript
interface mixin Slottable {
  readonly attribute HTMLSlotElement? assignedSlot;
}
```

### 插槽属性

* assignedSlot: 对应的Slot元素节点

## NodeList接口

Node节点集合

```javascript
interface NodeList {
  readonly attribute unsigned long length;

  getter Node? item(unsigned long index);
  iterable<Node>
}
```

### 元素属性

* `[index]`: 索引对应的节点
* length: 节点集合长度

### 迭代操作

* item(index): 获取索引对应的节点
* forEach(callback, thisArg): 遍历节点
* keys(): key迭代器
* values(): value迭代器
* entries(): [key, value]迭代器

### 创建

* childNodes
* getElementsByName(qualifiedName)
* querySelectorAll(selectors): 静态集合

## HTMLCollection接口

HTML元素节点集合

```javascript
interface HTMLCollection {
  readonly attribute unsigned long length;
  getter Element? item(unsigned long index);
  getter Element? namedItem(DOMString name);
}
```

### 元素属性

* `[index]`: 索引对应的HTML元素节点
* `[id]`: id对应的HTML元素节点
* `[name]`: name对应的HTML元素节点
* length: 节点集合长度

### 操作

* item(index): 获取索引对应的节点
* namedItem(name): 获取id或name对应的节点

### 创建

* children
* HTMLDocument集合属性
 * document.scripts
 * document.images
 * document.links
 * document.forms
* getElementsByTagName(qualifiedName)
* getElementsByClassName(classNames)
