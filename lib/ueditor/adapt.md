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

创建UE.ui.Editor对象

```
1. 创建editor实例，记录到editor.options.editor中
2. 加载ueditor.css文件
3. 重写editor实例的render函数
4. 返回修改后的editor实例
```

### render(id)

渲染id对应的编辑器实例

```
1. 将字符串id记录到editor实例的key属性中
2. 设置labelMap选项值为国际化中的labelMap字段
3. 为editor实例创建EditorUI对象
4. id对应的DOM为script时，创建一个div，复制DOM的class、style、id属性，替换DOM
5. id对应的DOM为textarea时，创建一个div，复制DOM的class、style、id，隐藏DOM，记录DOM到editor实例的textarea字段中
```

### UE.getEditor(id, opt)

获取编辑器实例

```
1. id对应的编辑器不存在时，创建UE.ui.Editor对象，存在时直接使用
2. 调用editor实例的render方法，渲染编辑器
3. 返回编辑器实例
```

### UE.delEditor(id)

删除编辑器实例

```
1. 调用editor实例的destory方法
2. 从缓存中删除id对应的editor实例
```

### UE.registerUI(uiName, fn, index, editorId)

注册UI，结构为{execFn: fn, id: editorId, index: index}
