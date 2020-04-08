# 节点混合

标签（空格分隔）： 前端知识体系

---

## 父节点接口

### ParentNode接口

父节点接口

```javascript
interface mixin ParentNode {
  readonly attribute HTMLCollection children;
  readonly attribute unsigned long childElementCount;
  readonly attribute Element? firstElementChild;
  readonly attribute Element? lastElementChild;

  void prepend((Node or DOMString)... nodes);
  void append((Node or DOMString)... nodes);

  Element? querySelector(DOMString selectors);
  NodeList querySelectorsAll(DOMString selectors);
}
```

#### 子元素属性

* children: 子元素节点集合
* childElementCount: 子元素节点个数
* firstElementChild: 第一个子元素节点
* lastElementChild: 最后一个子元素节点

#### 追加方法

* prepend(...nodes): 在第一个子节点前追加多个节点，字符串被自动处理为文本节点
* append(...nodes): 在最后一个子节点后追加多个节点，字符串被自动处理为文本节点

#### 查找方法

* querySelector(selectors): 根据选择器查找第一个匹配的元素节点
* querySelectorsAll(selectors): 根据选择器查找匹配的元素节点集合，静态集合

### NonElementParentNode接口

非Element的父节点接口

```javascript
interface mixin NonElementParentNode {
  Element? geElementById(DOMString elementId);
}
```

#### 查找方法

* getElementById(elementId): 根据id查找元素节点

## 子节点接口

### ChildNode接口

子节点接口

```javascript
interface mixin ChildNode {
  void before((Node or DOMString)... nodes);
  void after((Node or DOMString)... nodes);
  void remove();
  void replaceWith((Node or DOMString)... nodes);
}
```

#### 操作方法

* before(...nodes): 在节点前插入多个节点，字符串被自动处理为文本节点
* after(...nodes): 在节点后插入多个节点，字符串被自动处理为文本节点
* remove(): 删除节点
* replaceWith(...nodes): 用多个节点替换当前节点，字符串被自动处理为文本节点

### NonDocumentTypeChildNode接口

非DocumentType的子节点接口

```javascript
interface mixin NonDocumentTypeChildNode {
  readonly attribute Element? previousElementSibling
  readonly attribute Element? nextElementSibling
}
```

#### 兄弟元素属性

* previousElementSibling: 上一个兄弟元素节点
* nextElementSibling: 下一个兄弟元素节点

## 根节点接口

### DocumentOrShadowRoot接口

Document和ShadowRoot的共有接口

```javascript
interface mixin DocumentOrShadowRoot {
  // 由具体的标记语言实现
}
```

## 插槽接口

### Slottable接口

```
interface mixin Slottable {
  readonly attribute HTMLSlotElement? assignedSlot;
}
```

#### 插槽属性

* assignedSlot: 对应的Slot元素节点

## 节点集合接口

类数组对象

* 关联root和filter，按Tree Order排序
* 分为实时集合和静态集合，一般为实时集合

### NodeList接口

Node节点集合

```javascript
interface NodeList {
  getter Node? item(unsigned long index);
  readonly attribute unsigned long length;
  iterable<Node>
}
```

#### 属性

* `<index>`: 索引对应的节点
* length: 节点集合长度

#### 方法

* item(index): 获取索引对应的节点
* forEach(callback, thisArg): 遍历节点
* keys(): key迭代器
* values(): value迭代器
* entries(): [key, value]迭代器

#### 创建

* childNodes
* getElementsByName(name)
* querySelector(selectors): 静态集合

### HTMLCollection接口

HTML元素节点集合

```javascript
interface HTMLCollection {
  getter Element? item(unsigned long index);
  getter Element? namedItem(DOMString name);
  readonly attribute unsigned long length;
}
```

#### 属性

* `<index>`: 索引对应的HTML元素节点
* `<id>`: id对应的HTML元素节点
* `<name>`: name对应的HTML元素节点
* length: 节点集合长度

#### 方法

* item(index): 获取索引对应的节点
* namedItem(name): 获取id或name对应的节点

#### 创建

* children
* document集合属性
 * document.scripts
 * document.images
 * document.links
 * document.forms
* getElementsByTagName()
* getElementsByClassName()
