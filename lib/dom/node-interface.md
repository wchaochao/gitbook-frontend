# 节点接口

标签（空格分隔）： 前端知识体系

---

## Node接口

```javascript
interface Node: EventTarget {
  const unsigned short ELEMENT_NODE = 1;
  const unsigned short ATTRIBUTE_NODE = 2;
  const unsigned short TEXT_NODE = 3;
  const unsigned short CDATA_SECTION_NODE = 4;
  const unsigned short PROCESSING_INSTRUCTION_NODE = 7;
  const unsigned short COMMENT_NODE = 8;
  const unsigned short DOCUMENT_NODE = 9;
  const unsigned short DOCUMENT_TYPE_NODE = 10;
  const unsigned short DOCUMENT_FRAGMENT_NODE = 11;

  readonly attribute unsigned short nodeType;
  readonly attribute DOMString nodeName;

  attribute DOMString? nodeValue;

  attribute DOMString? textContent;
  void normalize();

  readonly attribute Node? parentNode;
  readonly attribute Element? parentElement;
  readonly attribute Node? previousSibling;
  readonly attribute Node? nextSibling;
  readonly attribute NodeList childNodes;
  readonly attribute Node? firstChild;
  readonly attribute Node? lastChild;

  Node insertBefore(Node node, Node? child);
  Node appendChild(Node node);
  Node replaceChild(Node node, Node child);
  Node removeChild(Node child);

  Node cloneNode(optional boolean deep = false);
  boolean isEqualNode(Node? otherNode);

  const unsigned short DOCUMENT_POSITION_DISCONNECTED = 0x01;
  const unsigned short DOCUMENT_POSITION_PRECEDING = 0x02;
  const unsigned short DOCUMENT_POSITION_FOLLOWING = 0x04;
  const unsigned short DOCUMENT_POSITION_CONTAINS = 0x08;
  const unsigned short DOCUMENT_POSITION_CONTAINED_BY = 0x10;
  const unsigned short DOCUMENT_POSITION_IMPLEMENTATION_SPECIFIC = 20;

  unsigned short compareDocumentPosition(Node other);
  boolean contains(Node? other);

  readonly attribute Document? ownerDocument;
  readonly attribute USVString baseURI;

  readonly attribute boolean isConnected;
  Node getRootNode(optional GetRootNodeOptions = {});

  DOMString? lookupPrefix(DOMString? namespace);
  DOMString? lookupNameSpaceURI(DOMString? prefix);
  boolean isDefaultNameSpace(DOMString? namespace);
}

dictionary GetRootNodeOptions {
  boolean composed = false;
}
```

### Node Document

关联的Document节点

* 创建时指定
* 通过adopt方法修改

### 节点特征

| 节点 | nodeType | nodeName |
| -- | -- | -- |
| Element | Node.ELEMENT_NODE(1) | 大写的标签名 |
| Attr | Node.ATTRIBUTE_NODE(2) | 属性名 |
| Text | Node.TEXT_NODE(3) | `#text` |
| CDATASection | Node.CDATA_SECTION_NODE(4) | `#cdata-section` |
| ProcessingInstruction | Node.PROCESSING_INSTRUCTION_NODE(7) | target属性 |
| Comment | Node.COMMENT_NODE(8) | `#comment` |
| Document | Node.DOCUMENT_NODE(9) | `#document` |
| DocumentType | Node.DOCUMENT_TYPE_NODE(10) | name属性 |
| DocumentFragment | Node.DOCUMENT_FRAGMENT_NODE(11) | `#document-fragment` |

### 节点值

读取

* Attr节点：返回属性值
* CharacterData节点：返回data值
* 其他节点：返回null

设置

* Attr节点：设置属性值
* CharacterData节点：设置data值
* 其他节点：不做任何处理

### 节点文本

读取

* Element、DocumentFragment节点：返回后代节点的文本组合
* Attr节点：返回属性值
* CharacterData节点：返回data值
* 其他节点：返回null

设置

* Element、DocumentFragment节点：使用文本节点替换所有子节点
* Attr节点：设置属性值
* CharacterData节点：设置data值
* 其他节点：不做任何处理

格式化

* normalize(): 移除空文本节点，合并相邻的文本节点，选区发生相应改变

### 节点关系

* parentNode: 父节点
* parentElement: 父元素节点
* previousSibling: 前一个兄弟节点
* nextSibling: 后一个兄弟节点
* childNodes: 所有子节点
* firstChild: 第一个子节点
* lastChild: 最后一个子节点
* hasChildNodes(): 是否有子节点

遍历

* childNodes
* firstChild + nextSibling
* lastChild + previousSibling

### 节点操作

* insertBefore(newChild, referenceChild): 插入到指定子节点前
* appendChild(newChild): 追加子节点
* removeChild(child): 删除子节点
* replaceNode(newChild, referenceChild): 替换子节点

### 节点克隆

* cloneNode(deep): 克隆节点，deep为true时同时克隆后代节点
 * Element节点：标签、属性相同
 * Attr节点：属性名、属性值相同
 * Text、Comment节点：data相同
 * ProcessingInstruction节点：data、target相同
* isEqualNode(otherNode): 是否是相同节点
 * Element节点：标签、属性、子节点相同
 * Attr节点：属性名、属性值相同
 * Text、Comment节点：data相同
 * ProcessingInstruction节点：data、target相同

### 节点位置

* compareDocumentPosition(other): 比较other相对于node的位置
 * 同一个节点：0
 * 根节点不同：Node.DOCUMENT_POSITION_DISCONNECTED(1)
 * other在node前：Node.DOCUMENT_POSITION_PRECEDING(2)
 * other在node后：Node.DOCUMENT_POSITION_FOLLOWING(4)
 * other包含node：Node.DOCUMENT_POSITION_CONTAINS(8)
 * other被node包含：Node.DOCUMENT_POSITION_CONTAINED_BY(16)
 * 其他情况：Node.DOCUMENT_POSITION_IMPLEMENTATION_SPECIFIC(32)
* contains(other): other是否是node的inclusive descendant节点

### Document相关

* ownerDocument: node document节点，document.ownerDocument为null
* baseURI: node document的base URL

### Root相关

* isConnected: shadow-including root是否为document节点
* getRoot(): 获取root节点
* getRoot({composed: true}): 获取shadow-including root节点

### namespace相关

* lookupPrefix(namespace): 根据namespace查找prefix
 * Element节点：从当前元素节点开始往上查找
 * Attr节点：从所属的元素节点开始往上查找
 * CharacterData节点：从parentElement节点开始查找
 * Document节点：查找根元素节点
 * DocumentType、DocumentFragemnt：返回null
* lookupNamespaceURI(prefix): 根据prefix查找namespace，同上
* isDefaultNameSpace(namespace): 是否是默认的命名空间
