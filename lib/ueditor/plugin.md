# UEditor插件

标签（空格分隔）： 前端知识体系

---

## Plugin接口

```javascript
interface Plugin {
  DOMString optionName;

  PluginExecCallback execFn;
  PluginAfterDisabledCallback afterDisabled;
}

callback PluginExecCallback = void ();
callback PluginAfterDisabledCallback = void ();
```

### 属性

* optionName：插件对应的选项名，默认为插件名

### 回调

* execFn：插件执行回调，this为editor实例
* afterDisabled：插件禁止回调，this为editor实例

## UE.plugins接口

记录旧版注册的插件，结构{pluginName: pluginExecCallback}

* pluginName：插件名
* pluginExecCallback：插件执行回调，this为editor实例
 * 执行时可以设置editor选项，添加命令、快捷键、输入输出规则，绑定事件


## UE.Plugin接口

新版插件

```javascript
interface UE.Plugin {
  void register(DOMString pluginName, PluginExecCallback fn, DOMString oldOptionName, PluginAfterDisabledCallback afterDisabled)
  void load(UE.Editor editor);
  void run(DOMString pluginName, UE.Editor editor);
}
```

### 内部属性

* _plugins：记录注册的插件，结构{pluginName: plugin}

### 插件操作

* register(pluginName, fn, oldOptionName, afterDisabled)：注册插件
* run(pluginName, editor)：执行插件

#### load(editor)

加载插件

```
1. 依次执行UE.Plugin中注册插件的执行回调
  a. 插件对应的选项值为false，执行禁止回调
  b. 插件对应的选项值不为false，根据执行回调的返回值设置editor选项，添加命令、快捷键、输入输出规则，绑定事件
2. 依次执行UE.Plugins中注册插件的执行回调
```
