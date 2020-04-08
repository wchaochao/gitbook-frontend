# 节点接口

标签（空格分隔）： 前端知识体系

---

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

* parentNode: 父节点
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

* ownerDocument: 创建时使用的document节点，document.ownerDocument为null
* baseURI: ownerDocument的base URL
* isConnected: 根节点是否为document节点

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
 * Element节点 - 标签、属性、子节点相同
 * Attr节点 - 属性名、属性值相同
 * Text节点 - data属性相同
 * Comment节点 - data属性相同
 * DocumentType节点 - name相同
 * DocumentFragment - 子节点相同
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
