# Node

标签（空格分隔）： 前端知识体系

---

## Node Tree

XML被解析器解析为Node Tree

### Document Tree

* 根节点为Document节点
* 可以有一个根元素节点DocumentElement

```
Document节点
    指令、注释、CDATA节点
    文档类型节点
    指令、注释、CDATA节点
    根元素节点 -- 属性节点
        文本节点
        子元素节点
        指令、注释、CDATA节点
    指令、注释、CDATA节点
```

### DocumentFragment Tree

* 根节点为DocumentFragement节点
* 对子节点进行操作

```
DocumentFragment节点
    文本节点
    子元素节点
    指令、注释、CDATA节点
```

### Shadow Tree

* 根节点为Shadow Root
* 通过host属性绑定到Light Tree上

```
Shadow Root节点    --host-->|<--shadowRoot--    Shadow host元素节点
    slot节点 --assignedNodes-->|<--assignedSlot slottables节点
```

#### Slots

插槽，引用Slottables

* name: 插槽名，默认为空字符串
 * 命名插槽：引用slot属性为name的Slottaables
 * 默认插槽：引用slot属性为空字符串的Slottables
* assignedNodes: 引用的Slottables

#### Slottables

宿主元素的子文本/元素节点

* slot: 对应的插槽名
* assignedSlot: 对应的插槽节点

## 节点

### 节点类型

* Element: 元素节点
* Attr: 属性节点
* Text: 文本节点
* CDATASection: CDATA节点
* ProcessingInstruction: 指令节点
* Comment：注释节点
* Document: 文档节点
* DocumentType: 文档类型节点
* DocumentFragment: 文档片段节点

### 节点关系

* 父子关系
* 祖先、后代关系
* 兄弟关系
* 先后关系

### 节点操作

* 查询：查找某个节点下符合条件的后代节点
* 遍历：遍历某个节点及其所有后代节点
 * 先序：先处理节点再处理下一个节点
 * 后序：先处理下一个节点再处理本节点
 * 深度优先：先处理子节点，再处理兄弟节点
 * 广度优先：先处理兄弟节点，再处理子节点

## Node接口

### 节点属性

| 节点 | nodeType | nodeName | nodeValue | textContent |
| -- | -- | -- | -- | -- |
| Element | 1 | tagName属性 | null | 所有后代文本 |
| Attr | 2 | 属性名 | 属性值 | 属性值 |
| Text | 3 | `#text` | data属性 | data属性 |
| CDATASection | 4 | `#cdata-section` | data属性 | data属性 |
| ProcessingInstruction | 7 | target属性 | data属性 | data属性 |
| Comment | 8 | `#comment` | data属性 | data属性  |
| Document | 9 | `#document` | null | null |
| DocumentType | 10 | name属性 | null | null |
| DocumentFragment | 11 | `#document-fragment` | null | 所有后代文本 |

### 关系属性

* parentNode: 父节点，只能是Element/Document/DocumentFragment
* parentElement: 父元素节点
* previousSibling: 前一个兄弟节点
* nextSibling: 后一个兄弟节点
* childNodes: 所有子节点
* firstChild: 第一个子节点
* lastChild: 最后一个子节点

遍历

* childNodes
* firstChild + nextSibling
* lastChild + previousSibling

### document相关节点

* baseURI: document.URL
* ownerDocument: document节点，document.ownerDocument为null
* isConnected: 是否在DOM树上

### 关系方法

* compareDocumentPosition(otherNode): otherNode相对于node的关系
 * 0 - 同一个节点
 * 1 - 不在同一文档
 * 2 - otherNode在node之前
 * 4 - otherNode在node之后
 * 8 - otherNode包含node
 * 16 - otherNode被node包含
 * 32 - 待定
* isEqual(otherNode): 两个节点是否类型相同，特定属性也相同
 * Element节点 - 标签、属性、子元素相同
 * Attr节点 - 属性名、属性值相同
 * Text节点 - data相同
 * Comment节点 - data相同
 * DocumentType节点 - name相同
 * DocumentFragment - 子元素相同
* contains(otherNode): otherNode为node节点或node的后代节点
* hasChildNodes(): 是否有后代节点

### 操作方法

* normalize(): 删除空文本节点，合并相邻的文本节点
* cloneNode(deep): 克隆节点（类型相同，特性属性相同）
* insertBefore(newChild, referenceChild): 插入到指定子节点前
* appendChild(newChild): 追加子节点
* removeChild(child): 删除子节点
* replaceNode(newChild, referenceChild): 替换子节点
* getRootNode(): 获取根节点

## NodeList接口

类数组对象，实时集合

### 属性

* `<index>`: 索引
* length: 长度

### 方法

* forEach(callback, thisArg): 遍历节点
* keys(): key迭代器
* values(): value迭代器
* entries(): [key, value]迭代器

### 创建

* childNodes
* getElementsByName()
* querySelector(): 静态集合

## HTMLCollection接口

类数组对象，实时集合

### 属性

* `<index>`: 索引
* `<id>`: id索引
* `<name>`: name索引
* length: 长度

### 创建

* children
* document集合属性
 * document.scripts
 * document.images
 * document.links
 * document.forms
* getElementsByTagName()
* getElementsByClassName()
