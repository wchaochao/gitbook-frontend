# 节点

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

#### 匹配

* slottable匹配slot: 在Shadow Root的slots节点中根据插槽名匹配第一个slot
* slot匹配slottables: 该slot第一次出现时，在Shadow host的slottables中根据插槽名匹配所有slottables，无匹配时为slot的子文本/元素节点
* slot匹配flattened slottables: slot匹配的slottables中有slot节点的，继续递归匹配

#### Slot change

当slot引用的slottables发生变化时会生成一个mutation observer微任务，触发slot change

* slottables发生变化时，对应的slot要变化
* slot变化时，slot所在树对应的slottables要变化

## 节点类型

* Document: 文档节点
* DocumentType: 文档类型节点
* DocumentFragment: 文档片段节点
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

## 节点操作

### 插入节点

在parent节点的一个子节点child前插入节点node

#### 验证

* child是parent的子节点或null
* node不能是parent及parent的祖先节点

#### 插入

* node为DocumentFragment节点时，插入node的子节点
* 插入后相关的选区、slot、slottable发生相应的变化
* 触发相应的slot change、customElement connected callback

### 追加节点

在parent节点的最后一个节点后插入节点node

### 删除节点

删除parent节点的一个子节点child

* child是parent节点的子节点
* 删除后相关的选区、slot、slottable、NodeIterator发生相应的变化
* 触发相应的slot change、customElement disconnected callback

### 替换节点

在parent节点的子节点child前插入节点node，并删除子节点child

### 查找节点

查找某个节点下符合条件的后代节点

### 遍历节点

遍历某个节点及其所有后代节点

* 先序：先处理节点再处理下一个节点
* 后序：先处理下一个节点再处理本节点
* 深度优先：先处理子节点，再处理兄弟节点
* 广度优先：先处理兄弟节点，再处理子节点
