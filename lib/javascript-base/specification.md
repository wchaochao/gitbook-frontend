# 规范类型

标签（空格分隔）： 前端知识体系

---

只存在于规范里的抽象类型

## Lexical Environment类型

词法环境类型，记录执行上下文的词法环境

```javascript
interface lexical Environment {
  attribute Environment Record record;
  attribute Lexical Enviroment outer;
}

interface Lexical Environment Operator {
  Lexical Environment NewDeclartiveEnvironment(Lexical Environment E)
  Lexical Environment NewObjectEnvironment(Object O, Lexical Environment E)
  Reference GetIdentifierReference(Lexical Environment lex, String name, Boolean strict)
}
```

### 属性

* record: 词法环境的环境记录项
* outer: 外部词法环境，形成作用域链

### 操作

* NewDeclartiveEnvironment(E): 创建声明式词法环境，环境记录项为声明式环境记录项，外部词法环境为E
* NewObjectEnvironment(O, E): 创建对象式词法环境，环境记录项为对象式环境记录项，绑定对象为O，外部词法环境为E
* GetIdentifierReference(lex, name, strict): 获取词法环境中的标识符引用
 * 标识符在词法环境里，返回引用
 * 标识符不在词法环境里，沿着作用链查找，返回相应的引用

## Environment Record类型

环境记录类型，记录标识符及其绑定的值

* 声明式环境记录项：将标识符与值绑定，如函数上下文、catch语句
* 对象式环境记录项：将标识符与对象属性绑定，如全局上下文，with语句

### Declarative Environment Record

```javascript
interface Environment Record {
  void CreateMutableBinding(String N, Boolean D)
  void CreateImmutableBinding(String N)
  Boolean DeleteBinding(String N)
  void SetMutableBinding(String N, Any V, Boolean S)
  void InitalizeImmutableBinding(String N, Any V)
  Boolean HasBinding(String N)
  Any GetBindingValue(String N, Boolean S)
  Undefined ImplicitThisValue()
}

interface Binding {
  attribute Any value;
  readonly attribute Boolean mutable;
  readonly attribute Boolean deleted;
}
```

* CreateMutableBinding(N, D): 创建可变绑定，D指定是否可删除
* CreateImmutableBinding(N): 创建不可变绑定，不可删除
* DeleteBinding(N): 删除可删除的绑定
* SetMutableBinding(N, V, S): 设置可变绑定的值，当设置不可变绑定时S指定是否要报错
* InitalizeImmutableBinding(N, S): 初始化不可变绑定的值
* HasBinding(N): 是否绑定了标识符
* GetBindingValue(N, S): 获取绑定的值，当获取未初始化的不可变绑定的值时S指定是否报错
* ImplicitThisValue(): 绑定的函数执行时的this值

### Object Environment Record

```javascript
interface Object Environment Record {
  void CreateImmutableBinding(String N)
  Boolean DeleteBinding(String N)
  void SetMutableBinding(String N, Any V, Boolean S)
  Boolean HasBinding(String N)
  Any GetBindingValue(String N, Boolean S)
  Undefined or Object ImplicitThisValue()
}
```

* CreateMutableBinding(N, D): 绑定对象添加属性，D指定属性是否可配置
* DeleteBinding(N): 绑定对象删除属性
* SetMutableBinding(N, V, S): 绑定对象设置属性，当设置属性出错时S指定是否抛出错误
* HasBinding(N): 绑定对象是否有属性
* GetBindingValue(N, S): 绑定对象获取属性值，当属性值不存在时S指定是否报错
* ImplicitThisValue(): 绑定的函数执行时的this值

## Reference类型

引用类型，用于描述引用

```javascript
interface Reference {
  readonly attribute Undefined or Number or String or Boolean or Object or Environment Record base;
  readonly attribute String name;
  readonly attribute Boolean strict;
}

interface Reference Operator {
  Undefined or Number or String or Boolean or Object or Environment Record GetBase(Reference V);
  String GetReferenceName(Reference V);
  Boolean IsStrictReference(Reference V);

  Boolean IsUnresolvableReference(Reference V);
  Boolean IsPropertyReference(Reference V);
  Boolean HasPrimitiveBase(Reference V);

  Any GetValue(Reference V);
  void PutValue(Reference V);
}
```

### 属性

* base: 基值
* name: 引用名
* strict: 是否为严格模式

### 分类

* Unresolvable引用：base为Undefined类型，表示未声明的变量
* Property引用：base为Number、String、Boolean、Object类型，表示对象属性
* Environment引用：base为Environment Record类型，表示环境记录项中绑定的标识符

### 操作

#### GetValue(V)

获取引用的值

1. V不是Reference类型，直接返回V
2. V是Unresolvable引用，抛出ReferenceError
3. V是Property引用，获取对象属性
4. V是Environment引用，获取环境记录项中绑定的标识符的值

### PutValue(V)

设置引用的值

1. V不是Reference类型，抛出ReferenceError
2. V是Unresolvable引用
 a. 严格模式下，抛出ReferenceError
 b. 非严格模式下，设置全局属性
3. V是Property引用，设置对象属性
 a. 原始值类型不能设置属性时严格模式下抛出ReferenceError
4. V是Environment引用，设置环境记录项中绑定的标识符的值

## Property Identifier

属性标识符类型，关联属性和属性描述符

```javascript
interface Property Identifier {
  attribute String name;
  attribute Property Descriptor descriptor;
}
```

### 属性

* name: 属性名
* descriptor: 属性描述符

## Property Descriptor类型

属性描述符类型，用于描述属性

```javascript
interface Property Descriptor {
  attribute Any? [[Value]];
  attribute Boolean? [[Writable]];
  attribute (Function or Undefined)? [[Get]];
  attribute (Function or Undefined)? [[Set]];
  attribute Boolean [[Enumerable]];
  attribute Boolean [[Configurable]];
}
```

### 分类

* 数据属性描述符：有[[Value]]、[[Writable]]、[[Enumerable]]、[[Configurable]]属性的描述符
* 访问器属性描述符：有[[Get]]、[[Set]]、[[Enumerable]]、[[Configurable]]属性的描述符
* 通用属性描述符：只有[[Enumerable]]或[[Configurable]]属性的描述符

### 描述符属性

* [[Value]]: 属性值
* [[Writable]]：属性值是否可写
* [[Get]]: 属性访问器
* [[Set]]: 属性设置器
* [[Enumerable]]: 属性是否可枚举
* [[Configurable]]: 属性是否可配置

### 属性描述符判断

* IsDataDescriptor(Desc): 是否是数据属性描述符
* IsAccessorDescriptor(Desc): 是否是访问器属性描述符
* IsGenericDescriptor(Desc): 是否是通用属性描述符

### 描述符转换

* FromPropertyDescriptor(Desc): 属性描述符转换为对象
* ToPropertyDescriptor(Desc): 对象转换为属性描述符

## Completion类型

完结类型，用于描述语句的执行情况

```javascript
interface Completion {
  readonly attribute normal or break or continue or return or throw type;
  readonly attribute Any or empty value;
  readonly attribute Identifier or empty target;
}
```

### 属性

* type: 执行类型
 * normal: 顺序执行
 * break: 中断循环
 * continue: 继续下一个循环
 * return: 返回值
 * throw: 抛出错误
* value: normal、return返回的值或throw抛出的错误
* target: break或continue的目标

## List类型

列表类型，用于描述列表

* 形参列表
* 实参列表
