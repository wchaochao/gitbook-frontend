# DOM标准

标签（空格分隔）： 前端知识体系

---

## 节点

### DOM树

XML被解析器解析为DOM树

```
Document节点
    指令节点
    文档类型节点
    注释节点
    CDATA节点
    根元素节点 -- 属性节点
        文本节点
        子元素节点
        注释节点
        CDATA节点
```

#### 关系

* 父子关系
* 祖先、后代关系
* 兄弟关系
* 先后关系

#### 操作

* 查询：查找某个节点下符合条件的后代节点
* 遍历：遍历某个节点及其所有后代节点
 * 先序：先处理节点再处理下一个节点
 * 后序：先处理下一个节点再处理本节点
 * 深度优先：先处理子节点，再处理兄弟节点
 * 广度优先：先处理兄弟节点，再处理子节点

### Shadow DOM

* shadow host: shadow dom的宿主元素
* shadow root: shadow dom的根元素

shadow host与shadow dom被影子边界分隔，shadow dom可以有自己的html、css、js

* html: 通过template和content引用host的后代元素
* css: 通过伪元素影响host样式，host也可以通过伪元素影响shadow dom
* js: shadow dom上的事件有些会被阻塞，有些会被重定向到host，content的事件会被重定向到host的后代元素

### Node接口

#### 节点属性

| 节点 | nodeType | nodeName | nodeValue | textContent |
| -- | -- | -- | -- | -- |
| Element | 1 | tagName属性 | null | 所有后代文本 |
| Attr | 2 | 属性名 | 属性值 | 属性值 |
| Text | 3 | `#text` | data属性 | data属性 |
| Comment | 8 | `#comment` | data属性 | data属性  |
| Document | 9 | `#document` | null | null |
| DocumentType | 10 | name属性 | null | null |
| DocumentFragment | 11 | `#document-fragment` | null | 所有后代文本 |

#### 关系属性

* parentNode: 父节点，只能是Element/Document/DocumentFragment
* parentElement: 父元素节点
* previousSibling: 前一个兄弟节点
* nextSibling: 后一个兄弟节点
* childNodes: 所有子节点
* firstChild: 第一个子节点
* lastChild: 最后一个子节点

#### document相关节点

* baseURI: document.URL
* ownerDocument: document节点，document.ownerDocument为null
* isConnected: 是否在DOM树上

#### 关系方法

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

#### 操作方法

* normalize(): 删除空文本节点，合并相邻的文本节点
* cloneNode(deep): 克隆节点（类型相同，特性属性相同）
* insertBefore(newChild, referenceChild): 插入到指定子节点前
* appendChild(newChild): 追加子节点
* removeChild(child): 删除子节点
* replaceNode(newChild, referenceChild): 删除子节点
* getRootNode(): 获取根节点

### NodeList接口

类数组对象，实时集合

#### 属性

* <index>: 索引
* length: 长度

#### 方法

* forEach(callback, thisArg): 遍历节点
* keys(): key迭代器
* values(): value迭代器
* entries(): [key, value]迭代器

#### 创建

* childNodes
* getElementsByName()
* querySelector(): 静态集合

#### HTMLCollection接口

## 节点监控

## 事件

## 事件中断

## 选区
