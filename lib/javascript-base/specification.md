# 规范类型

标签（空格分隔）： 前端知识体系

---

只存在于规范里的抽象类型

## List类型

列表类型，用于描述列表

* 使用<<item1, item2,...>>表示
* 使用list[index]访问列表中的元素

## Set类型

组类型，用于描述去重的列表

* 添加元素、移除元素
* 取交集、并集、子集

## Record类型

记录类型，用于描述数据聚合

* 使用{[[Field1]]: value1, [[Field2]]: value2,...}表示
* 使用record.[[Field]]访问[[Field]]的值

## Reference类型

引用类型，用于描述引用的Record类型

| Component | Value | 描述 |
| --- | --- | --- |
| base value | either Undefined、Boolean、Number、BigInt、String、Symbol、Object or EnvironmentRecord | 基值 |
| referenced name | String or Symbol | 引用名称 |
| strict reference flag | Boolean | 严格引用标志 |
| thisValue | Object | super引用的this值 |

### 分类

* Unresolvable引用：base为Undefined类型，表示未声明的变量
* Property引用：base为Boolean、Number、BigInt、String、Symbol、Object类型，表示对象属性
 * 原始值引用：原始值属性
 * 对象引用：对象属性
 * super引用：super属性
* Environment引用：base为Environment Record类型，表示环境记录项中绑定的标识符

### 操作

* GetBase(V)：获取引用的基值
* GetReferencedName(V)：获取引用名称
* IsStrictReference(V)：是否是严格引用
* IsUnresolvableReference(V)：是否是Unresolvable引用
* IsPropertyReference(V)：是否是Property引用
* HasPrimitiveBase(V)：是否是原始值引用
* IsSuperReference(V)：是否是super引用
* GetThisValue(V)：获取Property引用的this值，super引用时为thisValue，否则为base
* InitializeReferencedBinding(V, W)：初始化Environment引用绑定的标识符的值

### GetValue(V)

获取引用的值

```
1. V不是Reference类型，直接返回V
2. V是Unresolvable引用，抛出ReferenceError
3. V是Property引用，获取对象属性
4. V是Environment引用，获取环境记录项中绑定的标识符的值
```

### PutValue(V, W)

设置引用的值

```
1. V不是Reference类型，抛出ReferenceError
2. V是Unresolvable引用
  a. 严格模式下，抛出ReferenceError
  b. 非严格模式下，设置全局属性
3. V是Property引用，设置对象属性
  a. 严格模式下不能设置对象属性时抛出ReferenceError
4. V是Environment引用，设置环境记录项中绑定的标识符的值
```

## Property Descriptor类型

属性描述符类型，用于描述属性的Record类型

| Field | Value | 描述 |
| --- | --- | --- |
| [[Value]] | Any | 属性值 |
| [[Writable]] | Boolean | 是否可写 |
| [[Get]] | Function or Undefined | 访问器 |
| [[Set]] | Function or Undefined | 设置器 |
| [[Enumerable]] | Boolean | 是否可枚举 |
| [[Configurable]] | Boolean | 是否可配置 |

### 分类

* 数据属性描述符：由[[Value]]、[[Writable]]、[[Enumerable]]、[[Configurable]]组成的描述符
* 访问器属性描述符：由[[Get]]、[[Set]]、[[Enumerable]]、[[Configurable]]组成的描述符
* 通用属性描述符：只由[[Enumerable]]或[[Configurable]]组成的描述符

### 操作

* IsDataDescriptor(Desc): 是否是数据属性描述符
* IsAccessorDescriptor(Desc): 是否是访问器属性描述符
* IsGenericDescriptor(Desc): 是否是通用属性描述符
* FromPropertyDescriptor(Desc): 属性描述符转换为对象
* ToPropertyDescriptor(Desc): 对象转换为属性描述符
* CompletePropertyDescriptor(Desc)：补充为完整的数据属性描述符或访问器属性描述符

## Property Identifier类型

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

## Completion类型

完结类型，用于描述语句执行情况的Record类型

| Field | Value | 描述 |
| --- | --- | --- |
| [[Type]] | One of normal, break, continue, return, or throw | 语句类型 |
| [[Value]] | Any or empty | 语句产生的值 |
| [[Target]] | String or empty | 语句跳转的目标 |

## Abstract Closure类型

抽象闭包类型，用于描述函数的Record类型

```
1. Let addend be 41.
2. Let closure be a new Abstract Closure with parameters (x) that captures addend and performs the following steps when called:
  Return x + addend.
3. Let val be closure(1).
4. Assert: val is 42.
```

## Environment Record类型

环境记录类型，描述标识符解析的Record类型

### 分类

* 声明式环境记录项：将标识符与值绑定，如函数上下文、catch语句
* 对象式环境记录项：将标识符与对象属性绑定，如全局上下文，with语句

### Declarative Environment Record

声明式环境记录项

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

#### 方法

* CreateMutableBinding(N, D): 创建可变绑定，D指定是否可删除
* CreateImmutableBinding(N): 创建不可变绑定，不可删除
* DeleteBinding(N): 删除可删除的绑定
* SetMutableBinding(N, V, S): 设置可变绑定的值，当设置不可变绑定时S指定是否要报错
* InitalizeImmutableBinding(N, S): 初始化不可变绑定的值
* HasBinding(N): 是否绑定了标识符
* GetBindingValue(N, S): 获取绑定的值，当获取未初始化的不可变绑定的值时S指定是否报错
* ImplicitThisValue(): 绑定的函数执行时的this值

### Object Environment Record

对象式环境记录项

```javascript
interface Object Environment Record {
  void CreateMutableBinding(String N, Boolean D)
  Boolean DeleteBinding(String N)
  void SetMutableBinding(String N, Any V, Boolean S)
  Boolean HasBinding(String N)
  Any GetBindingValue(String N, Boolean S)
  Undefined or Object ImplicitThisValue()
}
```

#### 方法

* CreateMutableBinding(N, D): 绑定对象添加属性，D指定属性是否可配置
* DeleteBinding(N): 绑定对象删除属性
* SetMutableBinding(N, V, S): 绑定对象设置属性，当设置属性出错时S指定是否抛出错误
* HasBinding(N): 绑定对象是否有属性
* GetBindingValue(N, S): 绑定对象获取属性值，当属性值不存在时S指定是否报错
* ImplicitThisValue(): 绑定的函数执行时的this值

## Lexical Environment类型

词法环境类型，记录执行上下文的词法环境

```javascript
interface lexical Environment {
  attribute Environment Record record;
  attribute Lexical Enviroment outer;
}
```

### 属性

* record: 词法环境的环境记录项
* outer: 外部词法环境，形成作用域链

## Lexical Environment操作

```
interface Lexical Environment Operator {
  Lexical Environment NewDeclartiveEnvironment(Lexical Environment E)
  Lexical Environment NewObjectEnvironment(Object O, Lexical Environment E)
  Reference GetIdentifierReference(Lexical Environment lex, String name, Boolean strict)
}
```

### NewDeclartiveEnvironment(E)

创建声明式词法环境

1. 创建词法环境对象
2. 环境记录项为声明式环境记录项
3. 外部词法环境为E

### NewObjectEnvironment(O, E)

创建对象式词法环境

1. 创建词法环境对象
2. 环境记录项为对象式环境记录项，绑定对象为O
3. 外部词法环境为E

### GetIdentifierReference(lex, name, strict)

获取词法环境中的标识符引用

1. 标识符在词法环境里，返回引用
2. 标识符不在词法环境里，沿着作用链查找，返回相应的引用

## Data Block类型

数据块类型，用于描述字节序列

### 操作

* createByteDataBlock(size)：创建数据块，每个字节默认为0
* createSharedByteDateBlock(size)：创建进程间共享的数据块
* copyDataBlockBytes(toBlock, toIndex, fromBlock, fromIndex, count)：复制数据块
