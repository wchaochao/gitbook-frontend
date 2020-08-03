# UEditor DOM操作

标签（空格分隔）： 前端知识体系

---

## UE.dom.domUtils接口

dom工具函数

```javascript
interface UE.dom.domUtils {
  DOMString fillChar;

  void on(EventTarget element, (sequence<DOMString> or DOMString) types, EventListener handler);
  void un(EventTarget element, (sequence<DOMString> or DOMString) types, EventListener handler);

  boolean isEmptyBlock(Element element);
  boolean isCustomeNode(Element element);

  unsigned long getNodeIndex(Node node, optional boolean ignoreTextNode)
  sequence<Node> getElementsByTagName(Element element, DOMString names, (NodeFilterCallback or DOMString) filter);

  boolean hasClass(Element element, DOMString classNames);
  void addClass(Element element, DOMString classNames);
  void removeClass(Element element, DOMString classNames);

  boolean hasNoAttributes();
  void setAttributes(Element element, record<DOMString, DOMString> attrs)
  void removeAttributes(Element element, DOMString attrNames)
}

callback NodeFilterCallback = boolean (Node node)
```

### 属性

* fillChar：光标占位符`\u200B`

### DOM事件

* on(element, types, handler)：监听事件，types可以是type数组或空白符分隔的type
* un(element, types, handler)：解绑事件，types可以是type数组或空白符分隔的type

### DOM判断

* isEmptyBlock(element)：是否是空元素（元素节点，无文本，无UE.dtd中定义的$isNotEmpty元素）
* isCustomeNode(element)：是否是UEditor的辅助节点

### DOM查询

* getElementsByTagName(element, names, filter): node.getElementsByTagName的封装
 * names可以为空格分隔的多个name
 * filter为过滤函数，为字符串时按className过滤
* getNodeIndex(node, ignoreTextNode)：获取node在父节点中的索引位置，ignoreTextNode为true时多个连续的文本节点算作一个

### class操作

* hasClass(element, classNames)： 是否有classNames，classNames可以为空格分隔的className
* addClass(element, classNames)：添加classNames（不重复添加），classNames可以为空格分隔的className
* removeClass(element, classNames)：移除classNames，classNames可以为空格分隔的className

### attribute操作

* hasNoAttributes()：是否没有属性
* setAttributes(element, attrs)：设置属性
 * class时设置className
 * style时设置style.cssText
 * innerHTML时设置innerHTML
 * value时设置value
 * 其他使用setAttribute设置属性
* removeAttributes(element, attrNames)：移除属性，attrNames可以为空格分隔的attrName

## UE.Range接口

Range的封装

```javascript
interface UE.Range {
  constructor(Document document);

  Node? startContainer;
  unsigned long? startOffset;
  Node? endContainer;
  unsigned long? endOffset;
  boolean collapsed;
  Document document;
}
```

### 内部属性

* startContainer：start边界点的容器节点
* startOffset：start边界点在容器节点的偏移量
* endContainer：end边界点的容器节点
* endOffset：end边界点在容器节点的偏移量
* collapsed：选区是否是闭合的
* document：选区所属的document对象

### 选区边界操作

* setStart(node, offset): 设置start边界点为(node, offset)
* setEnd(node, offset): 设置end边界点为(node, offset)
* setStartBefore(node): 设置start边界点为(parent, node index)
* setStartAfter(node): 设置start边界点为(parent, node index + 1)
* setEndBefore(node): 设置end边界点为(parent, node index)
* setEndAfter(node): 设置end边界点为(parent, node index + 1)
* collapse(toStart)：闭合range，默认向end边界点闭合，toStart为true时向start边界点闭合

### 选区内容操作

* cloneContents：克隆选区内容到DocumentFragment中
