# UEditor适配层

标签（空格分隔）： 前端知识体系

---

封装下层API，提供给上层用户使用

## UE.ui.Editor接口

对UE.Editor的封装

```javascript
interface UE.ui.Editor {
  constructor(EditorOptions options);

  void render(DOMString id)
}
```

### new UE.ui.Editor(options)

实例化UE.ui.Editor

```
1. 创建editor实例，记录到editor.options.editor中
2. 加载ueditor.css文件
3. 重写editor实例的render函数
4. 返回修改后的editor实例
```

### render(id)

渲染id对应的编辑器实例

```
1. id为字符串时，设置editor.key为id
2. editor.options.labelMap未设置时，设置为国际化中的labelMap字段
3. 为editor实例创建EditorUI对象
4. 编辑器容器为script时
  a. script中有内容时设置为initialContent
  b. 创建一个div，复制DOM的class、style、id属性，替换DOM
5. 编辑器容器为textarea时
  a. textarea中有内容时设置为initialContent
  b. 创建一个div，复制DOM的class、style、id
  c. editor.options.textarea设置为textarea，隐藏textarea
6.
```

### UE.getEditor(id, opt)

获取编辑器实例

```
1. id对应的容器有编辑器时，直接返回编辑器实例
2. id对应的容器没有编辑器时，实例化UE.ui.Editor，执行render方法，返回编辑器实例
```

### UE.delEditor(id)

删除编辑器实例

```
1. 调用editor实例的destory方法
2. 从缓存中删除id对应的editor实例
```

### UE.registerUI(uiName, fn, index, editorId)

注册UI，结构为{execFn: fn, id: editorId, index: index}
