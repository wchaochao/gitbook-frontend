# UEditor UI层

标签（空格分隔）： 前端知识体系

---

## UE.ui接口

UI的命名空间

```javascript
typedef record<DOMString, BuiltInUI or RegisterUI>

interface RegisterUI {
  RegisterUIExecCallback execFn;
  DOMString id;
  unsigned long index;
}

callback RegisterUIExecCallback = UE.ui.uiBase? (UE.Editor editor, DOMString toolbarItem)
```

* BuiltInUI：内置UI，通过构造器创建UI对象
* RegisterUI：注册UI，通过回调函数execFn创建UI对象
 * execFn：UI对象执行函数
 * id：指定生成的UI对象所属的editor实例，不指定时所有editor实例都有该UI对象
 * index：指定生成的UI对象在最后一栏工具栏的位置，不指定时防止所在栏的最后

## UE.ui.uiBase接口

UI层基类

```javascript
interface UE.ui.uiBase {
  void initOptions(object options)
  void initUIBase();

  Element getDom();
  Element getDom(DOMString name);
}
```

### 初始化方法

* initOptions(options)：浅复制options对象到ui对象，并生成ui对象id
* initUIBase()：将ui对象挂载到`window.$EDITORUI`上，并使用_globalKey属性记录挂载的全局路径

### DOM方法

* getDom()：获取UI对象对应的DOM
* getDom(name)：获取UI对象DOM下name对应的DOM

## UE.ui.uiUtils接口

UI层工具函数

```javascript
interface UE.ui.uiUtils {
  unsigned long uid();
  unsigned long uid(object obj);

  string setGlobal(DOMString id, object obj);
  void deleteGlobal(DOMString id);

  boolean contains(Element elA, Element elB);
}
```

### uid方法

* uid()：给ui对象生成id
* uid(obj)：obj不存在`ID$EDITORUI`属性时生成`ID$EDITORUI`属性

### global方法

* setGlobal(id, obj)：将ui对象挂载到`window.$EDITORUI`上，并返回挂载的全局路径
* deleteGlobal(id)：从`window.$EDITORUI`中删除id对应的ui对象

### DOM方法

* contains(elA, elB)：elA是否包含elB

## EditorUI接口

editor的UI对象

```javascript
interface EditorUI: UE.ui.uiBase {
  constructor(EditorOptions options);

  UE.Editor editor;
  sequence<UE.ui.Toolbar> toolbars;
  record<DOMString, UE.ui.Dialog> _dialogs;
  unsigned long _actualFrameWidth;

  void initEditorUI();
  void _initToolbars();
}
```

### new EditorUI(options)

创建EditorUI对象

```
1. 初始化options，将editor实例的选项值挂载到EditorUI对象上
2. 初始化EditorUI对象
```

### initEditorUI()

初始化EditorUI对象

```
1. 将editorUI对象挂载到editor实例的ui字段上和window.$EDITORUI上
2. 初始化工具栏
3. 初始化底部栏
  a. options中允许元素路径时，设置元素路径的初始显示，监听光标变化
  b. options中允许字符统计时，设置字符统计的初始显示，监听字符数据的变化
```

### _initToolbars()

初始化EditorUI工具栏

```
1. 根据选项值中的toolbars初始化工具栏
2. 二维数组toolbars的每个一维数组元素对应一个UE.ui.Toolbar对象
3. 每个一维数组中的元素对应一个ui对象
  a. toobarItem对应的是BuiltInUI时，使用构造器实例化UI对象
  b. toobarItem对应的是RegisterUI时，调用execFn创建UI对象
```

### 内部属性

* editor：对应的editor实例
* toolbars：记录工具栏对象
* _dialogs：记录DiglogUI对象
* _actualFrameWidth：编辑器实时高度

## UE.ui.Toolbar接口

工具栏

```javascript
interface UE.ui.Toolbar: UE.ui.uiBase {
  constructor(object options);

  sequence<UE.ui.uiBase> items;

  void add(UE.ui.uiBase item);
  void add(UE.ui.uiBase item, unsigned long index);
}
```

### new UE.ui.Toolbar(options)

创建ToolBarUI对象

```
1. 初始化options
2. 将ToolBarUI对象挂载到window.$EDITORUI上
3. 初始化items属性
```

### 属性

* items：工具栏列表项数组

### 列表项操作

* add(item)：追加列表项
* add(item, index)：在index处插入列表项

## UE.ui.Seperate接口

分隔线

```javascript
interface UE.ui.Separate: UE.ui.uiBase {
  constructor(options);

  string getHtmlTpl();
}
```

## UE.ui.Breakline接口

工具栏换行

```javascript
interface UE.ui.Break: UE.ui.uiBase {
  constructor(options);

  string getHtmlTpl();
}
```

## UE.ui.Popup接口

工具栏弹框

```javascript
interface UE.ui.Popup: UE.ui.uiBase {
  constructor(options);

  boolean _hidden;

  static void closeAllPopup(Event event, Element el);
  static void postHide(Event event, Element el);

  boolean isHidden();
  boolean queryAutoHide(Element el);

  string getHtmlTpl();
}
```

### new UE.ui.Popup(options)

创建PopupUI对象

```
1. 初始化options
2. 将PopupUI对象挂载到window.$EDITORUI上
3. 将PopupUI对象记录在内部变量allPopups数组中
```

### 静态方法

* closeAllPopup(Event, el)：关闭所有弹框，触发afterhidepop事件
 * 编辑器内滚动、编辑器外滚动、Popup外点击时自动关闭所有弹框
* postHide(Event, el)：closeAllPopup的别名

### 内部属性

* _hidden：是否隐藏

### 显示隐藏操作

* isHidden()：弹框是否是隐藏的
* queryAutoHide(el)：是否要隐藏弹框
