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
  boolean hasChildNodes();

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

### 内部属性

* node document: 所属的document节点
 * 创建时指定，可通过adopt算法修改
* registered observer list: 注册的观察者列表

### 节点特征

| 节点 | nodeType | nodeName |
| -- | -- | -- |
| Element | Node.ELEMENT_NODE(1) | tagName属性 |
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

* Element、DocumentFragment节点：返回所有后代文本节点的组合
* Attr节点：返回属性值
* CharacterData节点：返回data值
* 其他节点：返回null

设置

* Element、DocumentFragment节点：使用文本节点替换所有子节点
* Attr节点：设置属性值
* CharacterData节点：设置data值
* 其他节点：不做任何处理

格式化

* normalize(): 移除空文本节点，合并相邻的文本节点，live range发生相应改变

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
 * ProcessingInstruction节点：target、data相同
 * Document、DocumentType节点：内部属性相同
* isEqualNode(otherNode): 是否是相同节点（类型相同、内部属性相同）
 * Element节点：标签、属性、子节点相同
 * Attr节点：属性名、属性值相同
 * Text、Comment节点：data相同
 * ProcessingInstruction节点：data、target相同
 * DocumentType节点：内部属性相同

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
 * DocumentType、DocumentFragment：返回null
* lookupNamespaceURI(prefix): 根据prefix查找namespace，同上
* isDefaultNameSpace(namespace): 是否是默认的命名空间

## Document接口

```javascript
interface Document: Node {
  constructor()

  readonly attribute DOMImplementation implementation;
  readonly attribute DOMString compatMode;
  readonly attribute DOMString contentType;
  readonly attribute DOMString characterSet;
  readonly attribute USVString URI;
  readonly attribute USVString documentURI;

  readonly attribute DocumentType? doctype;
  readonly attribute Element? documentElement;

  HTMLCollection getElementsByTagName(DOMString qualifiedName)
  HTMLCollection getElementsByTagNameNS(DOMString? namespace, DOMString localName);
  HTMLCollection getElementsByClassName(DOMString classNames);

  Document createDocumentFragment();
  Element createElement(DOMString localName, optional (DOMString or ElementCreationOptions) options = {});
  Element createElementNS(DOMString? namespace, DOMString qualifiedName, optional (DOMString or ElementCreationOptions) options = {});
  Attr createAttribute(DOMString localName);
  Attr createAttributeNS(DOMString? namespace, DOMString qualifiedName)
  Text createTextNode(DOMString data);
  CDATASection createCDATASection(DOMString data);
  Comment createComment(DOMString data);
  ProcessionInstruction createProcessingInstruction(DOMString target, DOMString data);

  Node importNode(Node node, optional boolean deep = false);
  Node adoptNode(Node node);

  Event createEvent(DOMString interface);
  Range createRange();
  NodeIterator createNodeIterator(Node root, optional unsigned long whatToShow = 0xFFFFFFFF, optional NodeFilter? filter = null)
  TreeWalker createTreeWalker(Node root, optional unsigned long whatToShow = 0xFFFFFFFF, optional NodeFilter? filter = null)
}

interface XMLDocument: Document {}

dictionary ElementCreationOptions {
  DOMString is;
}
```

### 内部属性

* DOMImplementation: DOM实现对象，document创建时附加
* type: 文档类型，默认为'xml'
 * xml: XML document
 * html: HTML document
* mode: 文档模式，默认为'no-quirks'
 * no-quirks: 标准模式
 * limited-quirks: 接近标准模式
 * quirks: 混杂模式
* content type: 媒体类型，默认为'application/xml'
* encoding: 编码方式，默认为'utf-8'
* URL: 文档地址，默认为'about: blank'
* origin: 文档源，默认为'null'

### new Document()

1. 创建Document对象
2. 初始化内部属性

### 特征属性

* implementation: document的DOMImplementation对象
* compatMode: 兼容模式
 * document的mode为quirks时返回'BackCompat'
 * document的mode为其他时返回'CSS1Compat'
* contentType: document的content type
* characterSet: document的encoding
* URL: document的URL
* documentURL: document的URL

### 节点引用

* doctype: document的doctype节点
* documentElement: document的根元素节点

### 查找节点

#### getElementsByTagName(qualifiedName)

根据qualifiedName匹配后代元素节点

1. qualifiedName为'*'时匹配所有后代元素节点
2. document为HTML document节点时
 a. 元素命名空间为HTML namespace时匹配小写qualifiedName
 b. 元素命名空间不为HTML namespace时匹配qualifiedName
3. 其他情况匹配qualifiedName

#### getElementsByTagNameNS(namespace, localName)

根据namespace和localName匹配后代元素节点

1. namespace和localName都为'*'时，匹配所有后代元素节点
2. namespace为'*'，localName不为'*'时，匹配localName
3. namespace不为'*'，localName为'*'时，匹配namespace
4. namespace不为'*'，localName不为为'*'时，匹配namespace和localName

#### getElementsByClassName(classNames)

根据classNames匹配后代元素节点

1. 将classNames转换为ordered set
2. 匹配classList包含ordered set所有元素的后端元素节点

### 创建节点

#### createDocumentFragemnt()

创建DocumentFragment节点，初始化node document

#### createElement(localName[, options])

根据localName创建元素节点

1. document为HTML document时，localName自动转换为小写
2. document为HTML document时，namespace为HTML namespace，否则为null
3. prefix为null
4. options提供is选项时，is为传入的is，否则为null
5. 使用document, localName, namespace, prefix, is创建元素节点

#### createElementNS(namespace, qualifiedName[, options])

根据namespace, qualifiedName创建元素节点

1. 从qualifiedName中拆出prefix, localName
2. 校验namespace, prefix是否匹配
3. options提供is选项时，is为传入的is，否则为null
4. 使用document, localName, namespace, prefix, is创建元素节点

#### createAttribute(localName)

根据localName创建属性节点

1. document为HTML document时，localName自动转换为小写
2. 创建Attr节点，初始化local name、node document

#### createAttributeNS(namespace, qualifiedName)

根据localName创建属性节点

1. 从qualifiedName中拆出prefix, localName
2. 校验namespace, prefix是否匹配
3. 创建Att节点，初始化localName、namespace、prefix、node document

#### createTextNode(data)

创建Text节点，初始化data、node document

#### createCDATASection(data)

创建CDATA节点

1. document为HTML document时，抛出不支持的错误
1. 创建CDATASection节点，初始化data、node document

#### createComment(data)

创建Comment节点，初始化data、node document

#### createProcessionInstruction(target, data)

创建ProcessionInstruction节点，初始化target、data、node document

### 外部节点

#### importNode(node[, deep=false])

复制外部节点

1. node为document或shadow root节点时，抛出错误
2. 复制外部节点node, deep为true时复制后代节点
3. 设置复制节点的node document为document

#### adoptNode(node)

移动外部节点

1. node为document或shadow root节点时，抛出错误
2. 外部节点从它的树中移除，并设置移动节点及其后代节点的node document为document
3. 节点或后代节点为自定义元素时触发adoptedCallback事件

### 创建其他

* createEvent(interface): 创建指定类型的事件对象，使用Event构造函数替代
* createRange(): 创建Range对象，使用Range构造函数替代
* createNodeIterator(root, whatToShow, filter): 创建NodeIterator对象
* createTreeWalker(root, whatToShow, filter): 创建TreeWalker对象

## DOMImplementation接口

```javascript
interface DOMImplementation {
  XMLDocument createDocument(DOMString? namespace, DOMString qualifiedName, optional DocumentType? doctype = null)
  Document createHTMLDocument(optional DOMString title)
  DocumentType createDocumentType(DOMString qulifiedName, DOMString publid, systemId)
}
```

### createDocument(namespace, qualifiedName, doctype)

创建XMLDocument节点及XML基本结构

* 创建XMLDocument节点
* doctype提供时，append到XMLDocument节点
* 使用namespace, qualifiedName创建根元素节点，append到XMLDocument节点

### createHTMLDocument(title)

创建HTMLDocument节点及HTML基本结构

1. 创建HTMLDocument节点
2. 创建HTML的doctype节点，并append到HTMLDocument节点中
3. 创建html根元素节点，并append到HTML节点
4. 创建head元素节点，并append到html节点
5. 创建body元素节点，并append到html节点
6. title提供时，创建title元素节点，文本为title，并append到head节点

### createDocumentType(qulifiedName, publicId, systemId)

创建DocumentType节点，初始化name、publicId、systemId、node document

## Doctype接口

```javascript
interface DocumentType: Node {
  readonly attribute DOMString name;
  readonly attribute DOMString publicId;
  readonly attribute DOMString systemId;
}
```

### 内部属性

* name: 名称
* publicId: 公共ID，默认为空字符串
* systemId: 系统Id，默认为空字符串

### 特征属性

* name: doctype的name
* publicId: doctype的publicId
* systemId: doctype的systemId

## DocumentFragment接口

```javascript
interface DocumentFragment: Node {
  constructor();
}
```

### 内部属性

* host: 宿主元素，用于template元素

### new DocumentFragment()

创建DocumentFragment节点

1. 创建DocumentFragment节点
2. 设置node document为全局对象的document属性

## ShadowRoot接口

```javascript
interface ShadowRoot: DocumentFragment {
  readonly attribute ShadowRootMode mode;
  readonly attriute Element host;
  attribute EventHandler onslotchange;
}

enum ShadowRootNode {
  "open",
  "close"
}
```

### 内部属性

* mode: 模式
 * open: 开放模式，shadow host可以通过shadowRoot属性访问
 * closed: 关闭模式，shadow host不可以通过shadowRoot属性访问
* delegates focus: 委托聚焦，默认为false
* host: 宿主元素

### 特征属性

* mode: shadow root的mode
* host: shadow root的host
* onslotchange: shadow root的slot change事件

## Element接口

```javascript
interface Element: Node {
  readonly attribute DOMString? namespaceURI;
  readonly attribute DOMString? prefix;
  readonly attribute DOMString localName;
  readonly attribute DOMString tagName;

  attribute DOMString id;
  attribute DOMString className;
  readonly attribute DOMTokenList classList;
  attribute DOMString slot;

  readonly attribute NamedNodeMap attributes;
  Attr? getAttributeNode(DOMString qualifiedName);
  Attr? getAttributeNodeNS(DOMString? namespace, DOMString localName);
  Attr? setAttributeNode(Attr attr);
  Attr? setAttributeNodeNS(Attr attr);
  Attr removeAttributeNode(Attr attr);

  boolean hasAttributes();
  boolean hasAttribute(DOMString qualifiedName);
  boolean hasAttributeNS(DOMString? namespace, DOMString localName);
  sequence<DOMString> getAttributeNames();
  DOMString? getAttribute(DOMString qualifiedName)
  DOMString? getAttributeNS(DOMString? namespace, DOMString localName)
  void setAttribute(DOMString qualifiedName, DOMString value);
  void setAttributeNS(DOMString? namespace, DOMString qualifiedName, DOMString value);
  void removeAttribute(DOMString qualifiedName);
  void removeAttributeNS(DOMString? namespace, DOMString localName);
  boolean toogleAttribute(DOMString qualifiedName, optional boolean force);

  readonly attribute ShadowRoot? shadowRoot;
  ShadowRoot attachShadow(ShadowRootInit init);

  Element? closest(DOMString selectors);
  boolean matches(DOMString selectors);

  HTMLCollection getElementsByTagName(DOMString qualifiedName);
  HTMLCollection getElementsByTagNameNS(DOMString? namespace, DOMString localName);
  HTMLCollection getElementsByClassName(DOMString classNames);

  Element? insertAdjacentElement(DOMString where, Element element);
  void insertAdjacentText(DOMString where, DOMString data);
}

dictionary ShadowRootInit {
  required ShadowRootMode mode;
  boolean delegatesFocus = false;
}
```

### 内部属性

命名空间

* namespace: 命名空间
* namespace prefix: 命名空间前缀
* local name: 名称
* qualified name: 冒号分隔的namespace prefix和local name
 * namespace prefix为null时，为local name
* HTML-uppercased qualified name
 * namespace为HTML namespace且node document为HTML document时，是大写的qualified name
 * 其他情况为qualified name

自定义元素

* custom element state: 自定义元素状态
 * undefined: 元素未定义
 * uncustomized: 内置元素
 * custom: 自定义元素
 * failed: 自定义元素实例化失败
* custome element definition: 自定义元素定义
* is: 自定义内置元素名

属性

* attribute list: 属性列表，默认为空列表
* unique identifier: 元素ID

shadow tree

* shadow root: 挂载的shadow tree的根节点

### 特征属性

* namespaceURI: element的namespace
* prefix: element的namespace prefix
* localName: element的local name
* tagName: element的HTML-uppercased qualified name

### 映射属性

* id: 映射元素的id属性
* className: 映射元素的class属性
* classList: className的ordered set形式
* slot: 映射元素的slot属性

映射操作

* getter: 获取对应的content attribute值
* setter: 设置对应的content attribute值

### 属性基本操作

#### attributes

element的attribute list

#### 属性改变

1. 触发attribute mutation
2. 元素为自定义元素时，触发attributeChangedCallback

#### 修改属性节点

1. 触发属性改变，由oldValue变为newValue
2. 属性节点的属性值设为新值

#### 追加属性节点

1. 触发属性改变，由null变为value
2. 将attr追加到element的attribute list
3. 设置attr的element属性为element

#### 移除属性节点

1. 触发属性改变，由value变为null
2. 从element的attribute list中移除attr
3. 设置attr的element属性为null

#### 替换属性节点

1. 触发属性改变，由oldValue变为newValue
2. 将element的attribute list的oldAttr替换为newAttr
3. 设置newAttr的element属性为element
4. 设置oldAttr的element属性为null

### 属性节点操作

#### getAttributeNode(qualifiedName)

根据qualifiedName获取匹配的第一个属性节点

1. 元素节点namespace为HTML namespace且node document为HTML时，qualifiedName转换为小写
2. 从元素节点的属性列表中获取第一个qualified name为qualifiedName的属性节点

#### getAttributeNodeNS(namespace, localName)

根据namespace、localName获取匹配的第一个属性节点

1. 从元素节点的属性列表中获取第一个namespace为namespace, local name为localName的属性节点

#### setAttributeNode(attr)

设置属性节点

1. 根据attr的namespace、localName获取匹配的第一个属性节点oldAttr
2. oldAttr存在时，替换属性节点
3. oldAttr不存在时，追加属性节点

#### setAttributeNodeNS(attr)

同setAttributeNode(attr)

#### removeAttributeNode(attr)

移除属性节点

1. attr不在元素节点的属性列表中时，抛出错误
2. 移除属性节点attr

### 属性操作

* hasAttributes(): 是否有属性
* hasAttribute(qualifiedName): 是否有qualifiedName匹配的属性节点
* hasAttributeNS(namespace, localName): 是否有namespace、localName匹配的属性节点
* getAttributeNames(): 获取元素属性列表的所有属性名
* getAttribute(qualifiedName): 根据qualifiedName获取匹配的第一个属性节点的属性值
* getAttributeNS(namespace, localName): 根据namespace、localName获取匹配的第一个属性节点的属性值
* setAttribute(qualifiedName, value): 根据qualifiedName获取匹配的第一个属性节点，存在时修改，不存在时追加
* setAttributeNS(namspace, qualifiedName, value): 根据namespace、localName获取匹配的第一个属性节点，存在时修改，不存在时追加
* removeAttribute(qualifiedName): 根据qualifiedName获取匹配的第一个属性节点，移除该节点
* removeAttributeNS(namespace, localName): 根据namespace、localName获取匹配的第一个属性节点，移除该节点
* toggleAttribute(qualifiedName, force): 根据qualifiedName获取匹配的第一个属性节点，存在且force不存在或为false时移除节点，不存在且force未提供或为true时追加属性节点

### shadow操作

* shadowRoot: element的shadow root, shadow root的mode为closed时，返回null

#### attachShadow(init)

挂载shadow tree

* element的local name不为自定义元素名或`article, aside, blockquote, body, div, footer, h1, h2, h3, h4, h5, h6, header, main, nav, p, section, span`时抛出错误
* element已挂载shadow tree时抛出错误
* 创建shadowRoot节点，初始化mode, delegates focus, host
* 设置element的shadow root为创建的shadowRoot节点

### 匹配元素

* matches(selectors): element是否匹配selectors
* closest(selectors): 返回第一个匹配selectors的inclusive ancestor元素

### 查找元素

* getElementsByTagName(qualifiedName): 根据qualifiedName匹配element的后代元素节点
* getElementsByTagName(namespace, localName): 根据namespace、localName匹配element的后代元素节点
* getElementsByClassName(classNames): 根据classNames匹配element的后代元素节点

### 相邻插入

* insertAdjacentElement(where, element): 根据where位置插入元素
 * beforebegin: 插入element前
 * afterbegin: 插入element的第一个节点前
 * beforeend: 插入element的最后一个节点后
 * afterend: 插入element后
* insertAdjacentText(where, data): 根据where插入文本节点

## NamedNodeMap接口

```javascript
interface NamedNodeMap {
  readonly attribute unsigned long length;

  getter Attr? item(unsigned long index);
  getter Attr? getNamedItem(DOMString qualifiedName);
  Attr? getNamedItemNS(DOMString? namespace, DOMString localName);
  Attr? setNamedItem(Attr attr);
  Attr? setNamedItemNS(Attr attr);
  Attr removeNamedItem(DOMString qualifiedName);
  Attr removeNamedItemNS(DOMString? namespace, DOMString localName)
}
```

### 内部属性

* element: 关联的element节点
* attribute list: 关联element的attribute list

### 属性元素

* [index]: index对应的属性节点
* [name]: name对应的属性节点
* length: attribute list的size

### 操作

* item(index): 同[index]
* getNamedItem(qualifiedName): 同element的getAttributeNode(qualifiedName)
* getNamedItemNS(namespace, localName): 同element的getAttributeNodeNS(namespace, localName)
* setNamedItem(attr): 同element的setAttributeNode(attr)
* setNamedItemNS(attr): 同element的setAttributeNodeNS(attr)
* removeNamedItem(qualifiedName): 同element的removeAttribute(qualifiedName)
* removeNamedItemNS(namespace, localName): 同element的removeAttributeNS(namespace, localName)

## Attr接口

```javascript
interface Attr: Node {
  readonly attribute DOMString? namespaceURI;
  readonly attribute DOMString? prefix;
  readonly attribute DOMString localName;
  readonly attribute DOMString name;
  attribute DOMString value;
  readonly attribute Element? ownerElement;
}
```

### 内部属性

* namespace: 命名空间，默认为null
* namespace prefix: 命名空间前缀，默认为null
* local name: 名称
* qualified name: 属性名，同element的qualified name
* value: 属性值，默认为空字符串
* element: 所属的element节点，默认为null

### 特征属性

* namespaceURI: attr的namespace
* prefix: attr的namespace prefix
* localName: attr的local name
* name: attr的qualified name
* value: attr的value, setter时相当于修改属性
* ownerElement: attr的element

## CharacterData接口

```javascript
interface CharacterData: Node {
  attribute DOMString data;
  readonly attribute unsigned long length;

  DOMString substringData(unsigned long offset, unsigned long count);
  void appendData(DOMString data);
  void insertData(unsigned long offset, DOMString data);
  void deleteData(unsigned long offset, unsigned long count);
  void replaceData(unsigned long offset, unsigned long count, DOMString data);
}
```

### 内部属性

* data: 字符数据

### 特征属性

* data: character data的data, setter时相当于replaceData
* length: character data的data的长度

### data操作

变更操作会触发characterData mutation, live range会发生相应的调整

* substringData(offset, count): 截取数据
* appendData(data): 追加数据
* insertData(offset, data): 插入数据
* deleteData(offset, count): 移除数据
* replaceData(offset, count, data): 替换数据

## Text接口

```javascript
interface Text: CharacterData {
  constructor(optional DOMString data = "");

  Text splitText(unsigned long offset);
  readonly attribute DOMString wholeText;
}
```

### 术语

* exclusive Text node: 纯Text节点，非CDATASection节点
* contiguous Text nodes: node节点及node连续相邻的文本节点
* descendant text content: 所有后代文本节点的data组合

### new Text([data = ""])

创建Text节点，初始化data, node document

### 属性

* wholeText: contiguous Text nodes的data组合

### 操作

* splitText(offset): 将一个文本节点拆分为两个文本节点

## CDATASection接口

```javascript
interface CDATASection: Text {
}
```

## ProcessingInstruction接口

```javascript
interface ProcessingInstruction: CharacterData {
  readonly attribute DOMString target;
}
```

### 内部属性

* target: 指令目标

### 特征属性

* target: ProcessingInstruction的target

## Comment接口

```javascript
interface Comment: CharacterData {
  constructor(optional DOMString data = "");
}
```

### new Comment([data = ""])

创建Comment节点，初始化data, node document
