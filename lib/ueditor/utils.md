# UEditor辅助接口

标签（空格分隔）： 前端知识体系

---

## UE.browser接口

浏览器检测

```javascript
dictionary UE.browser {
  DOMString chrome;
  DOMString safari;
  DOMString opera;

  boolean ie;
  boolean ie9above;
  boolean ie9below;
  boolean ie11above;
  boolean ie11below;

  boolean ie11Compat;
  boolean ie9Compat;
  boolean ie8;
  boolean ie8Compat;
  boolean ie7Compat;
  boolean ie6Compat;

  boolean webkit;
  boolean gecko;

  boolean mac;
  boolean quirks;
  boolean isCompatible;
}
```

### 浏览器版本检测

* chrome：浏览器为Chrome时的版本
* safari：浏览器为Safari时的版本
* opera：浏览器为Opera时的版本

### IE版本检测

* ie：是否为IE
* ie9above：是否为IE9及以上版本
* ie9below：是否为IE9及以下版本
* ie11above：是否为IE11及以上版本
* ie11below：是否为IE11及以下版本

### IE兼容模式检测

* ie11Compat：是否为IE11兼容模式
* ie9Compat：是否为IE9兼容模式
* ie8：是否为IE8
* ie8Compat：是否为IE8兼容模式
* ie7Compat：是否为IE7兼容模式
* ie6Compat：是否为IE6兼容模式

### 浏览器内核检测

* webkit：是否是webkit内核
* gecko：是否是gecko内核

### 其他检测

* mac：是否是Mac平台
* quirks：是否是怪异模式
* isCompatible：是否支持UEditor

## UE.utils接口

工具函数

```javascript
interface UE.utils {
  void each((object or sequence) obj, IteratorCallback iterator, object context);

  object extend(object target, object source, boolean isKeepTarget)
  object clone(object source, object target);

  object makeInstance((object or null) obj)
  class inhreits(class subClass, class superClass)

  unsigned long indexOf(sequence array, any item, unsigned long start)
  void removeItem(sequence array, any item)

  DOMString trim(DOMString str);
  DOMString unhtml(DOMString str);
  DOMString html(DOMString str);

  boolean isNumber(any vlaue);
  boolean isString(any vlaue);
  boolean isObject(any vlaue);
  boolean isArry(any vlaue);
  boolean isFunction(any vlaue);
  boolean isDate(any vlaue);
  boolean isRegExp(any vlaue);

  void loadFile(Document doc, LoadFileOptions options, LoadFileSuccessCallback fn);

  void domReady(DOMReadyHandler onready);
}

callback IteratorCallback = boolean (any value, DOMString key, (object or sequence) obj)

dictionary LoadFileOptions {
  LoadFileTag tag;
  DOMString type;
  USVString src;
  USVString href;
  boolean async;
  boolean defer;
}

enum LoadFileTag {
  "script",
  "link"
}

callback LoadFileSuccessCallback = void ()
```

### 遍历方法

* each(obj, iterator, context): 遍历数组或对象
 * iterator回调的this为context
 * iterator回调返回false时停止遍历

### 复制方法

* extend(target, source, isKeepTarget)：浅复制source对象到target对象，isKeepTarget为true时不覆盖
* clone(source, target)：深复制source对象到target对象

### 继承方法

* makeInstance(obj)：相当于Object.create(obj)
* inhreits(subClass, superClass)：子类继承父类，子类原型原有的属性保留

### 数组方法

* indexOf(array, item, start)：从start开始查找item元素位置
* removeItem(array, item)：移除所有值为item的元素

### 字符串方法

* trim(str)：去除字符串的首尾字符
* unhtml(str)：对字符串中单独的`<>'"&`字符转义为html实体
* html(str)：将字符串中的`<>'"&`html实体转换回来

### 类型判断方法

* isNumber(value)：是否是number类型或Number对象
* isString(value)：是否是string类型或String对象
* isObject(value)：是否是Object对象
* isArray(value)：是否是数组
* isFunction(value)：是否是函数
* isDate(value)：是否是Date对象
* isRegExp(value)：是否是正则

### 文件加载方法

* loadFile(doc, options, fn)：加载js或css文件（使用缓存，不重复加载），options为标签属性，fn为加载成功的回调

### DOM事件方法

* domReady(onready)：DOM ready之后执行回调

## UE.ajax接口

Ajax封装

```javascript
interface UE.ajax {
  void request(USVString url, AjaxOptions options)
}

dictionary AjaxOptions {
  AjaxDataType dataType;
  USVString url;
  AjaxMethod method = "POST";
  boolean async = true;
  object data = {};
  AjaxSuccessCallback onsuccess = function () {};
  AjaxErrorCallback onerror = function () {};
  JSONPCompleteCallback oncomplete;
  unsigned long timeout = 500;
}

enum AjaxDataType {
  "jsonp"
}

enum AjaxMethod {
  "GET",
  "POST"
}

callback AjaxSuccessCallback = void (XMLHttpRequest xhr)
callback AjaxErrorCallback = void (XMLHttpRequest xhr)
callback JSONPCompleteCallback = void ()
```

### 请求参数

* dataType：请求类型，为jsonp时发出jsonp请求
* url：请求URL，url参数未传时使用options中的url
* method：请求方法
* async：是否是异步操作
* data：请求参数
 * GET请求时加到url的查询参数中
 * POST请求时Content-Type默认为application/x-www-form-urlencoded
* onsuccess：成功回调
* onerror：失败回调
* complete：JSONP的完成回调
* timeout：超时时间

### 请求操作

* request(url, options)：发出Ajax或JSONP请求

## UE.dtd

UEditor的文档类型定义

```javascript
dictionary UE.dtd {
  record<DOMString, unsigned short> $empty;
  record<DOMString, unsigned short> $isNotEmpty;
  record<DOMString, unsigned short> $cdata;
}
```

### 元素分类

* `$empty`：自闭合元素
* `$isNotEmpty`：没有内容也不能认为是空的元素
* `$cdata`：内部的标签会当作文本的元素
