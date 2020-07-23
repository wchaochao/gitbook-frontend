# UEditor核心接口

标签（空格分隔）： 前端知识体系

---

UEditor核心层，挂载在UE命名空间下

## UE.EventBase接口

事件基类

```javascript
interface UE.EventBase {
  constructor();

  readonly attribute record<DOMString, sequence<EventListener>> __allListeners;

  void addListener(DOMString types, EventListener listener);
  void removeListener(DOMString types, EventListener listener);
  void fireEvent(DOMString types, any... args)

  void on(DOMString types, EventListener listener);
  void off(DOMString types, EventListener listener);
  void trigger(DOMString types, any... args)
}

callback EventListener = any (DOMString type, any... args)
```

### 内部属性

* __allListeners： 记录注册的事件监听器，结构为{type: listener[]}
 * type：事件类型
 * listener：事件监听器，参数为触发时传递的参数

### 事件监听器

* addListener(types, listener): 注册事件监听器，types为空白符分隔的type时同时注册多个
* removeListener(types, listener): 移除事件监听器，types为空白符分隔的type时同时移除多个
* fireEvent(types): 触发事件，types为空白符分隔的type时同时触发多个
* on(types, listener): addListener的别名
* off(types, listener): removeListener的别名
* trigger(types): fireEvent的别名

### 触发事件

```
1. 获取事件对应的监听器数组
2. 按倒序触发监听器，返回true时停止触发
3. 触发'on' + type
```

## Command接口

编辑器命令

```javascript
interface Command {
  boolean notNeedUndo;
  boolean ignoreContentChange;

  QueryCommandStateCallback queryCommandState;
  QueryCommandValueCallback queryCommandValue;
  ExecCommandCallback execCommand;
}

QueryCommandStateCallback queryCommandState = unsigned short (DOMString commandName)
QueryCommandValueCallback queryCommandValue = any (DOMString commandName)
ExecCommandCallback execCommand = void (DOMString commandName, any... args)
```

### 属性

* notNeedUndo：命令是否不需要记录在操作历史中
* ignoreContentChange：是否忽略内容变化

### 回调操作

this为editor实例

* queryCommandState(commandName)：查询命令状态
 * 1：已使用
 * 0：可用
 * -1：不可用
* queryCommandValue(commandName)：查询命令的值
* execCommand(commandName, ...args)：执行命令

## UE.Editor接口

核心编辑器类

```javascript
interface UE.Editor: UE.EventBase {
  readonly unsigned long uid;

  readonly record<DOMString, any> options;

  any getOpt(DOMString key);
  any setOpt(DOMString key, any value);
  any setOpt(record<DOMString, any> options);

  readonly record<DOMString, Command> commands;

  void registerCommand(DOMString cmdName, Command command);
  any _callCmdFn(DOMString fnName, sequence<any> args);
  unsigned short queryCommandState(DOMString commandName);
  any queryCommandValue(DOMString commandName);
  void execCommand(DOMString commandName)

  readonly record<DOMString, DOMString> shortcutKeys;

  void addshortcutKey(DOMString commandName, DOMString key);
  void addshortcutKey(record<DOMString, DOMString> cmdToKeys);
  void _bindshortcutKeys();

  readonly sequence<uNodeFilter> inputRules;

  void addInputRule(uNodeFilter filter);
  void filterInputRule(UE.uNode root);

  readonly sequence<uNodeFilter> outputRules;

  void addOutputRule(uNodeFilter filter);
  void filterOutputRule(UE.uNode root);

  readonly EditorUI ui;
  readonly Element container;
  readonly Window window;
  readonly Document document;
  readonly HTMLBodyElement body;

  UE.ui.Dialog getDialog(DOMString name);

  string getLang(DOMString path);

  void ready(ReadyCallback fn);
}
callback uNodeFilter = void (uNode root)
```

### uid

editor实例的标志符

* 使用UE.instants的`ueditorInstant` + uid字段记录editor实例
* 编辑区域iframe的id为`ueditor_` + uid

### options

选项，三层结构，优先级由高到低

* 传入的options
* config.js中的options
* Editor.defaultoptions.js中的options

属性

* editor：editor实例
* textarea：文本域DOM
* lang：语言类型
* labelMap：工具栏的国际化
* toolbars：工具栏按钮名称
* elementPathEnabled：是否启用元素路径
* wordCount：是否启用字数统计
* initialFrameHeight：默认的编辑区域高度
* autoClearinitialContent：聚焦时是否自动清空初始化时的内容

操作

* getOptions(key)：获取选项值
* setOptions(key, vlaue)：设置单个选项值，不覆盖
* setOptions(options)：设置多个选项值，不覆盖

### commands

命令，结构为{commandName: command}

* commandName：命令名
* command：命令对象

操作

* registerCommand(commandName, command)：注册命令
* _callCmdFn(fnName, args)：执行注册在commands属性或UE.commands中的命令对象的某个回调
 * fnName：回调名称
 * args：回调的参数，第一个参数为命令名
* queryCommandState(commandName)：调用命令的queryCommandState回调，没有时返回0
* queryCommandValue(commandName)：调用命令的queryCommandValue回调

#### execCommand(commandName)

执行编辑器命令

```
1. 获取命令名对应的命令对象
2. 命令对象需要记录在操作历史时
  a. 调用编辑器实例的queryCommandState方法，结果不为-1时执行以下步骤
  b. 触发saveScene、beforeexeccommand事件
  c. 执行命令的execCommand回调
  d. 触发afterexeccommand、saveScene事件
3. 命令对象不需要记录在操作历史时
  a. 执行命令的execCommand回调
  b. 命令不忽略内容变化时触发contentchange事件
4. 命令不忽略内容变化时执行实例的_selectionChange方法
```

### shortcutkeys

命令的快捷键，结构为{commandName: shortcutKeyStr}

* commandName：命令名
* shortcurKeyStr：快捷键字符串

```javascript
{
  cmd1: '65', // A
  cmd2: 'Ctrl+65', // Ctrl+A
  cmd3: 'Ctrl+Shift+65', // Ctrl+Shift+A
  cmd4: 'Ctrl+Shift+65:param', // queryCommandState、execCommand回调的参数为cmd4, param
  cmd5: '65,Ctrl+65' // A 或 Ctrl+A
}
```

操作

* addshortcutKey(commandName, key)：添加一个快捷键，不覆盖
* addshortcutKey(cmdToKeys)：添加多个快捷键，不覆盖
* _bindshortcutKeys()：添加keydown事件监听快捷键，按住快捷键且命令状态为可用时执行命令

### inputRules

输入过滤规则，结构为uNodeFilter[]

* uNodeFilter：uNode节点过滤器，this为editor实例

操作

* addInputRule(filter)：添加输入过滤规则
* filterInputRule(root)：对root节点执行所有的输入过滤规则

### outputRules

输出过滤规则，结构为uNodeFilter[]

* uNodeFilter：uNode节点过滤器，this为editor实例

操作

* addOutputRule(filter)：添加输出过滤规则
* filterOutputRule(root)：对root节点执行所有的输出过滤规则

### ui

editor实例的EditorUI对象

* container：EditorUI对象对应的DOM
* window：编辑区域的window对象
* documnt：编辑区域的document对象
* body: 编辑区域的body对象

操作

* getDialog(name)：获取dialog

### 国际化

* getLang(path)：获取path对应的国际化字符串

### 事件周期

* ready(fn)：editor ready后执行fn

### 内容操作

* hasContent()：编辑器是否有内容（editor.body不为空元素）

#### getContent()

获取编辑器内容

```
1. 没有内容时直接返回空字符串
2. 触发beforegetcontent事件
3. 将editor.body.innerHTML解析为uNode，执行所有的输出过滤规则
4. 触发aftergetcontent事件
5. 将过滤后的uNode转换为html返回
```

## UE.instants接口

记录editor实例，结构为{editorId: editorInstance}

* editorId：由`ueditorInstant`和uid属性拼装成的字符串
* editorInstance：editor实例
