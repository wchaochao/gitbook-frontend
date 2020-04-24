# 数据类型

标签（空格分隔）： 前端知识体系

---

## 原始值类型

存储值

### Undefined类型

值未定义

* 未赋值的变量
* 不存在的对象属性
* 未传入实参的形参
* 没有return的返回值

### Null类型

值为空

* 空对象

### Number类型

数字

* 64位双精度浮点数存储
* 计算会存在误差

```
1位数符S + 11位阶码E（偏移量1023） + 52位尾数M

E > 0 && E < 2047，表示值 (-1)^S * 2^(E-1023) * 1.M
  Number.MAX_VALUE：S为0，E为2046，M全为1
  Number.MAX_SAFE_INTEGER：S为0，E为1023 + 52，M全为1
  Number.MIN_SAFE_INTEGER：S为-1，E为1023 + 52，M全为1
  Number.EPSILON：S为0，E为1023，M为1

E = 0，表示值 (-1)^S * 2^(-1023) * 0.M
  +0：S为0，M全为0
  -0：S为1，M全为0
  Number.MIN_VALUE：S为0，M为1

E = 2047
  Infinity：S为0，M全为0
  -Infinity：S为1，M全为0
  NaN：M不全为0
```

### String类型

字符串，使用UTF-16编码，每两个字节算一个字符

* 单字节字符：两个字节表示
* 多字节字符：高位两个字节、低位两个字节，会影响length和index属性

### Boolean类型

布尔值

* 条件判断

## 引用类型

存储引用

### Object类型

各类对象

* 内置对象
* 原生对象
* 宿主对象：浏览器环境API、NodeJS环境API

#### Object对象

键值对

#### Array对象

* 按顺序排列的一组元素
* 长度和元素动态关联

#### 类数组对象

有length属性的对象，可以调用数组的一些方法

* String对象
* Arguments对象
* DOM元素集合

#### Function对象

函数也是一个对象

#### Arguments对象

传入的实参对象，与形参一一对应

#### 基本包装对象

Number、String、Boolean类型的包装对象

* Number, String, Boolean类型当作对象使用时，会临时创建对应的包装对象，执行完后再销毁

#### Date对象

用距1970年1月1日00:00:00(UTC时间)的毫秒数表示时间

* 本地时间 = 毫秒数 + 时区
* UTC时间的时区为0

#### RegExp对象

用于正则匹配

#### Error对象

用于错误处理

#### 单体内置对象

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

### isNumber()

```javascript
function isNumber (value) {
  return typeof value === 'number' || (isObjectLike(value) && getTag(value) == = '[object Number]')
}

function getTag (value) {
  return Object.prototype.toString.call(value)
}
```

### isString()

```javascript
function isString (value) {
  return typeof value === 'string' || (isObjectLike(value) && getTag(value) === '[object String]')
}
```

### isBoolean()

```javascript
function isBoolean (value) {
  return typeof value === 'boolean' || (isObjectLike(value) && getTag(value) === '[object Boolean]')
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
