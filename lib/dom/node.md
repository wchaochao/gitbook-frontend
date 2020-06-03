# 节点基础

标签（空格分隔）： 前端知识体系

---

## 节点类型

* Document: 文档节点
* DocumentType: 文档类型节点
* DocumentFragment: 文档片段节点
 * ShadowRoot: ShadowTree根节点
* Element: 元素节点
* Attr: 属性节点
* CharacterData: 字符数据节点
 * Text: 文本节点
  * CDATASection: CDATA节点
 * ProcessingInstruction: 指令节点
 * Comment：注释节点

## 节点限制

* 父节点可以是Document、DocumentFragment、Element节点
* 子节点可以是DocumentType、DocumentFragment、Element、CharaterData节点
* 父节点为Document节点时
 * 子节点不能为Text节点
 * 只能有一个DocumentType节点和一个根元素节点，且DocumentType节点要在根元素节点前面
* 父节点为DocumentFragment、Element节点时
 * 子节点不能为DocumentType节点

## 节点关系

* 父子关系
* 祖先、后代关系
* 兄弟关系
* 先后关系
* 宿主元素、Shadow Tree关系

### 术语

* root: 根节点，从当前节点往上查找，直至parentNode为null的节点
* inclusive ancestor: 节点及其所有祖先节点
* inclusive descendant: 节点及其所有后代节点
* inclusive sibling: 节点及其所有兄弟
* shadow-including root: 根节点为ShadowRoot时，继续查找ShadowHost的根节点
* shadow-including ancestor: ShadowRoot为祖先节点时，继续查找ShadowHost及其祖先节点
* shadow-including descendant: ShadowHost为后代节点时，继续查找ShadowRoot及其后代节点

## 节点操作

### 插入前校验

* child是parent的子节点或null
* node不能是parent及parent的祖先节点

### 插入节点

在parent节点的一个子节点child前插入节点node

* node为DocumentFragment节点时，插入node的子节点
* live range在child后时执行相应调整
* slot、slottable发生变化时触发slot change
* 自定义元素插入时触发connectedCallback
* 插入节点后触发subList mutation

### 追加节点

在parent节点的最后一个节点后插入节点node

### 删除前校验

* child是parent节点的子节点

### 删除节点

删除parent节点的一个子节点child

* live range在child里或后时执行相应调整
* slot、slottable发生变化时触发slot change
* 自定义元素移除时触发disconnectedCallback
* 移除节点后触发subList mutation

### 替换节点

在parent节点的子节点child前插入节点node，并删除子节点child

### 替换所有

删除parent节点下所有子节点，并追加新的节点node

### 查找节点

查找某个节点下符合条件的后代节点

### 遍历节点

遍历某个节点及其所有后代节点

* 先序：先处理节点再处理下一个节点
* 后序：先处理下一个节点再处理本节点
* 深度优先：先处理子节点，再处理兄弟节点
* 广度优先：先处理兄弟节点，再处理子节点
