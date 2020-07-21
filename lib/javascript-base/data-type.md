# 数据类型

标签（空格分隔）： 前端知识体系

---

## 分类

* 原始值类型：存储值，包括Undefined、Null、Boolean、Number、BigInt、String、Symbol类型
* 引用类型：存储引用，包括Object类型

## Undefined类型

值未定义

* 未赋值的变量
* 不存在的对象属性
* 未传入实参的形参
* 没有return的返回值

## Null类型

值为空

* 空对象

## Boolean类型

布尔值

* 逻辑判断

## Number类型

数字

### 存储

64位双精度浮点数存储

1. 选择64位双精度浮点数离散值中最接近的一个值
2. 同样接近时选择位数M为偶数的

### 组成

1位数符S + 11位阶码E（偏移量1023） + 52位尾数M

```
E > 0 && E < 2047，表示值 (-1)^S * 2^(E-1023) * 1.M，可用s * m * 2^e表示，s为+1或-1，2^52 <= m < 2^53，-1074 <= e <= 971
  Number.MAX_VALUE：最大正数，1 * (2^53 - 1) * 971
  Number.MAX_SAFE_INTEGER：最大安全整数，1 * (2^53 - 1)
  Number.MIN_SAFE_INTEGER：最小安全整数，-1 * (2^53 - 1)
  Number.EPSILON：1与大于1的最小浮点数的差值，2^-52

E = 0，表示值 (-1)^S * 2^(-1022) * 0.M，可用s * m * 2^e表示，s为+1或-1，m < 2^52，e为-1074
  +0：1 * 0 * 2^-1074
  -0：-1 * 0 * 2^-1074
  Number.MIN_VALUE：最小正数，1 * 1 * 2^-1074

E = 2047，表示无限数
  Infinity：S为0，M全为0
  -Infinity：S为1，M全为0
  NaN：M不全为0
```

### 分类

可以表示 2^64 - 2^53 + 3 个数

* 有限数：2^64 - 2^53个
 * 规格数：2^64 - 2^54个
 * 非规格数：2^53个，包括+0、-0
* 无限数：3个
 * 无穷大：正无穷大、父无穷大
 * 非数字：2^53 - 2个表示方式

## BigInt类型

任意长度的整数值

### 创建

```javascript
const bigint = 1234567890123456789012345678901234567890n

const sameBigint = BigInt('1234567890123456789012345678901234567890')

const bigintFromNumber = BigInt(10)
```

## String类型

字符串

### 存储

UTF-16编码的Code Point

1. 字符串转换为对应的Code Point
2. Code Point使用UTF-16编码
 * 基本平面字符使用两个字节表示
 * 辅助平面字符使用代理对表示，高位两字节、低位两字节

### 组成

一系列的16位无符号整数

* index: 索引，每两个字节一个索引
* length: 长度，最大的索引加1

## Symbol类型

唯一的标识符，可以被用作属性名

### 创建

使用Symbol()创建

```javascript
let id1 = Symbol('id')
let id2 = Symbol('id')

id1 === id2 // false
```

### API

* description: symbol的描述
* toString(): 转换为字符串，symbol不会自动转换为字符串

```javascript
let id = Symbol('id')
console.log(id) // TypeError: Cannot convert a Symbol value to a string

console.log(id.description) // id
console.log(id.toString()) // Symbol(id)
```

### 全局Symbol

* Symbol.for(key): key不在全局symbol空间中，创建，在,返回
* Symbol.keyFor(symbol): 返回全局symbol对应的key

```javascript
let id = Symbol.for('id')
let idAgain = Symbol.for('id')

id === idAgain // true

let localSymbol = Symbol('name')
Symbol.keyFor(id) // id
Symbol.keyFor(localSymbol) // undefined
```

### 内置Symbol

| Symbol | 描述 |
| --- | --- |
| Symbol.toPrimitive | 转换为原始值类型的方法，用在ToPrimitive操作中 |
| Symbol.toStringTag | 获取对象字符串描述的方法，用在Object.prototype.toString中 |
| Symbol.hasInstance | 判断是否是实例的方法，用在instanceof中 |
| Symbol.isConcatSpreadable | 在数组拼接时是否要展开，用在Array.prototype.concat中 |
| Symbol.iterator | 获取对象默认迭代器的方法，用在for-of中 |
| Symbol.asyncIterator | 获取对象默认异步迭代器的方法，用在for-await-of中 |
| Symbol.species | 用于创建衍生对象的构造器 |
| Symbol.match | 字符串匹配方法，用在String.prototype.match中 |
| Symbol.matchAll | 字符串匹配所有方法，用在String.prototype.matchAll中 |
| Symbol.replace | 字符串替换方法，用在String.prototype.replace中 |
| Symbol.search | 字符串查询方法，用在String.prototype.search中 |
| Symbol.split | 字符串分隔方法，用在String.prototype.split中 |
| Symbol.unscopables | 不会被with对象访问的属性配置 |

## Object类型

属性的集合

### 属性

* 属性名：String类型或Symbol类型
 * String类型：标识符名、String字面量、整数索引
 * Symbol类型：Symbol值
* 属性值：任意类型，为函数时称为方法

### 数据属性

属性值由`[[value]]`决定

| 属性描述 | 类型 | 默认值 | 描述 |
| --- | --- | --- | --- |
| [[Value]] | any | undefined | 属性值 |
| [[Writable]] | Boolean | false | 属性值是否可设置，为false时不可设置属性值 |
| [[Enumerable]] | Boolean | false | 属性是否可枚举，为true时可在`for/in, Object.keys, JSON.stringify`中遍历 |
| [[Configurable]] | Boolean | false | 属性是否可配置，为false时属性不可删除、不可改为访问器属性、属性描述不可修改（[[Writable]]为true时，[[Writable]]和[[Value]]可修改） |

### 访问器属性

属性值由`get/set`函数决定

| 属性描述 | 类型 | 默认值 | 描述 |
| --- | --- | --- | --- |
| [[Get]] | Function &#x7c; Undefined | undefined | 获取属性时执行的函数，参数为空，返回值为属性值 |
| [[Set]] | Function &#x7c; Undefined | undefined | 设置属性时执行的函数，参数为设置的值 |
| [[Enumerable]] | Boolean | false | 属性是否可枚举，为true时可在`for/in, Object.keys, JSON.stringify`中遍历 |
| [[Configurable]] | Boolean | false | 属性是否可配置，为false时属性不可删除、不可改为数据属性、属性描述不可修改 |

### 内部方法

通用方法

| 内部方法 | 类型 | 描述 |
| --- | --- | --- |
| [[GetPrototypeOf]] | () -> Object &#x7c; Null | 获取对象原型 |
| [[SetPrototypeOf]] | (Object &#x7c; Null) -> Boolean | 设置对象原型 |
| [[IsExtensible]] | () -> Boolean | 对象是否可扩展，不可扩展时不能添加属性，不能设置原型 |
| [[PreventExtensions]] | () -> Boolean | 阻止对象扩展 |
| [[GetOwnProperty]] | (propertyKey) -> Undefined &#x7c; Property Descriptor | 获取对象自身属性的属性描述符 |
| [[DefineOwnProperty]] | (propertyKey, Property Descriptor) -> Boolean | 设置对象自身属性的属性描述符，不存在时创建 |
| [[HasProperty]] | (propertyKey) -> Boolean | 是否是对象自身或继承的属性 |
| [[Get]] | (propertyKey, Receiver) -> any | 获取对象自身或继承属性的属性值 |
| [[Set]] | (propertyKey, value, Receiver) -> Boolean | 设置属性值 |
| [[Delete]] | (propertyKey) -> Boolean | 设置对象自身属性 |
| [[OwnPropertyKeys]] | () -> List of PropertyKey | 获取对象自身属性的属性名List |

函数专用方法

| 内部方法 | 类型 | 描述 |
| --- | --- | --- |
| [[Call]] | (any, a List of any) -> any | 执行函数 |
| [[Construct]] | (a List of any, Object) -> Object | 创建实例对象 |

## 内置对象

### Object对象

键值对

### Array对象

* 按顺序排列的一组元素
* 长度和元素动态关联

### 类数组对象

有length属性的对象，可以调用数组的一些方法

* String对象
* Arguments对象
* DOM元素集合

### Function对象

函数也是一个对象

### Arguments对象

传入的实参对象，与形参一一对应

### 基本包装对象

Number、String、Boolean类型的包装对象

* Number, String, Boolean类型当作对象使用时，会临时创建对应的包装对象，执行完后再销毁

### Date对象

用距1970年1月1日00:00:00(UTC时间)的毫秒数表示时间

* 本地时间 = 毫秒数 + 时区
* UTC时间的时区为0

### RegExp对象

用于正则匹配

### Error对象

用于错误处理

### 单体内置对象

* Global对象：提供全局属性和全局方法
* Math对象：用于数学运算
* JSON对象：用于转换和解析JSON

## 类型判断

* typeof: 区分原始值类型和引用类型
* instanceof: 区分实例
* Object.prototype.toString: 区分对象

### isUndefined()

```javascript
function isUndefined (value) {
  return value === undefined
}
```

### isNull()

```javascript
function isNull (value) {
  return value === null
}
```

### isBoolean()

```javascript
function isBoolean (value) {
  return typeof value === 'boolean' || (isObjectLike(value) && getTag(value) === '[object Boolean]')
}

function getTag (value) {
  return Object.prototype.toString.call(value)
}
```

### isNumber()

```javascript
function isNumber (value) {
  return typeof value === 'number' || (isObjectLike(value) && getTag(value) == = '[object Number]')
}
```

### isString()

```javascript
function isString (value) {
  return typeof value === 'string' || (isObjectLike(value) && getTag(value) === '[object String]')
}
```

### isObjectLike()

```javascript
function isObjectLike (value) {
  return typeof value === 'object' && value !== null
}
```

### isObject()

```javascript
function isObject (value) {
  return isObjectLike(value) || typeof value === 'function'
}
```

### isPlainObject()

```javascript
function isPlainObject (value) {
  return isObjectLike(value) && getTag(value) === '[object Object]'
}
```

### isArray()

```javascript
function isArray (value) {
  return Array.isArray(value)
}
```

### isArrayLike()

```javascript
function isArrayLike(value) {
  return value != null && typeof value !== 'function' && isLength(value.length)
}

function isLength (value) {
  return typeof value === 'number' && value > -1 && value % 1 === 0 && value <= Math.MAX_SAFE_INTEGER
}
```

### isFunction()

```javascript
function isFunction (value) {
  return typeof value === 'function'
}
```

### isArguments()

```javascript
function isArguments (value) {
  return isObjectLike(value) && getTag(value) === '[object Arguments]'
}
```

### isDate()

```javascript
function isDate (value) {
  return isObjectLike(value) && getTag(value) === '[object Date]'
}
```

### isRegExp()

```javascript
function isRegExp (value) {
  return isObjectLike(value) && getTag(value) === '[object RegExp]'
}
```

### isError()

```javascript
function isError (value) {
  return isObjectLike(value) && getTag(value) === '[object Error]'
}
```

## 类型转换

### ToPrimitive(input, PreferredType)

引用类型转换为原始值类型

```
PreferredType为'String'
    优先使用input.toString()方法返回的原始值类型
    次要使用input.valueOf()方法返回的原始值类型
    都不返回原始值类型时报错

PreferredType为'Number'
    优先使用input.valueOf()方法返回的原始值类型
    次要使用input.toString()方法返回的原始值类型
    都不返回原始值类型时报错

PreferedType默认为'String'（Date对象），'Number'（其他对象）
```

### ToBoolean(input)

转换为Boolean类型

| 输入类型 | 结果 |
| --- | --- |
| Undefined | false |
| Null | false |
| Number | NaN、+0、-0转换为false，其他转换为true |
| String | 空字符串转换为false，其他转换为true |
| Object | true |

### ToNumber(input)

转换为Number类型

| 输入类型 | 结果 |
| --- | --- |
| Undefined | NaN |
| Null | +0 |
| Boolean | true转换为1，false转换为+0 |
| String | 忽略前置、后置空白<br/>空字符串转换为0<br/>十进制数字字符串、十六进制整数字符串转换为相应的十进制数字<br/>Infinity、-Infinity转换为相应的无穷大<br/>其他转换为NaN |
| Object | 先转换为原始值类型（PreferredType为'Number'），再转换为Number类型 |

### ToInteger(input)

转换为整数，先转换为Number类型

| Number | Integer |
| --- | --- |
| NaN | +0 |
| 0或无穷大 | number |
| 其他 | sign(number) * floor(abs(number)) |

### ToUInt32(input)

转换为32位无符号整数([0, 2^32 - 1])，先转换为Number类型

| Number | Integer |
| --- | --- |
| NaN、0、无穷大 | +0 |
| 其他 | 先转换为整数，再执行modulo 2^32 |

### ToInt32(input)

转换为32位有符号整数([-2^31, 2^31-1])

| Number | Integer |
| --- | --- |
| NaN、0、无穷大 | +0 |
| 其他 | 先转换为整数，再执行modulo 2^32，结果大于2^31时再减去2^32 |

### ToUInt16(input)

转换为16位无符号整数([0, 2^16 - 1])，先转换为Number类型

| Number | Integer |
| --- | --- |
| NaN、0、无穷大 | +0 |
| 其他 | 先转换为整数，再执行modulo 2^16 |

### ToString(input)

转换为String类型

| 输入类型 | 结果 |
| --- | --- |
| Undefined | `'undefined'` |
| Null | `'null'` |
| Boolean | true转换为`'true'`，false转换为`'false'` |
| Number | 小数点前的数字超过20位或小数点后紧跟的0超过5位，为科学计数法字符串，其他直接为数字字符串|
| Object | 先转换为原始值类型（PreferredType为'String'），再转换为Number类型 |

### ToObject(input)

转换为Object类型

| 输入类型 | 结果 |
| --- | --- |
| Undefined、Null | 抛出TypeError，不能转换为Object类型 |
| Number、String、Boolean | 用相应的包装器包装 |
