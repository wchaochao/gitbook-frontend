# 节点树

标签（空格分隔）： 前端知识体系

---

XML被解析器解析为Node Tree

## Document Tree

根节点为Document节点

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

## DocumentFragment Tree

根节点为DocumentFragement节点

```
DocumentFragment节点
    文本节点
    子元素节点
    指令、注释、CDATA节点
```

## Shadow Tree

根节点为Shadow Root

```
Shadow Root节点
    slot节点
    其他节点

Shadow host元素节点
    slottables节点
    其他节点
```

### Shadow host

Shadow Tree绑定的宿主元素节点

* Shadow Root的host属性指向Shadow host
* Shadow host的shadowRoot属性指向Shadow Root

### Slot

Shadow Tree的插槽，引用Slottables

* name: 插槽名，默认为空字符串
 * 命名插槽：引用slot属性为name的Slottaables
 * 默认插槽：引用slot属性为空字符串的Slottables
* assignedNodes: 引用的Slottables

### Slottables

宿主元素的子文本/元素节点

* slot: 对应的插槽名
* assignedSlot: 对应的插槽节点

### 匹配

* slottable匹配slot: 在Shadow Root的slots节点中根据插槽名匹配第一个slot
* slot匹配slottables: 该slot第一次出现时，在Shadow host的slottables中根据插槽名匹配所有slottables，无匹配时为slot的子文本/元素节点
* slot匹配flattened slottables: slot匹配的slottables中有slot节点的，继续递归匹配

### 变化

* slottables发生变化时，对应的slot要变化
* slot变化时，slot所在树对应的slottables要变化

#### Slot change

当slot引用的slottables发生变化时会触发slot change事件

1. 将slot放入signal slots队列中
2. 生成一个mutation observer微任务，放入微任务队列中（已经有mutation observer微任务时不用再生成）
3. mutation observer微任务执行时从signal slots队列中取出所有slot，触发它的slot change事件
