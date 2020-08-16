# 类型操作

标签（空格分隔）： 前端知识体系

---

## 类型转换

### ToPrimitive(input, PreferredType)

转换为原始值类型

```
1. input为原始值类型，直接返回
2. 根据PreferedType获取hint
  a. PreferedType不存在时，hint为default
  b. PreferedType为hint String时，hint为string
  c. PreferedType为hint Number时，hint为number
3. input有Symbol.toPrimitive方法时，执行该方法，参数为hint
  a. 返回值为原始值类型，直接返回
  b. 返回值不为原始值类型，抛出TypeError
4. hint为default时，置为number，根据hint获取执行列表
  a. hint为number时，执行列表为<<valueOf, toString>>
  b. hint为string时，执行列表为<<toString, valueOf>>
5. 依次执行执行列表中的方法
  a. 返回值为原始值类型时，直接返回
  b. 返回值都不为原始值类型时，抛出TypeError
```

Date对象和Symbol对象有Symbol.toPrimitive方法

### ToBoolean(input)

转换为Boolean类型

| input | result |
| --- | --- |
| Undefined | false |
| Null | false |
| Boolean | input |
| Number | NaN、+0、-0转换为false，其他转换为true |
| BigInt | 0n转换为false，其他转换为true |
| String | 空字符串转换为false，其他转换为true |
| Symbol | true |
| Object | true |

### ToNumber(input)

转换为Number类型

| input | result |
| --- | --- |
| Undefined | NaN |
| Null | +0 |
| Boolean | true转换为1，false转换为+0 |
| Number | input |
| BigInt | 抛出TypeError |
| String | 数字字符串转换为相应的数字，其他转换为NaN |
| Symbol | 抛出TypeError |
| Object | 先转换为原始值类型，偏向number，再转换为Number类型 |

数字字符串

```
StringNumberLiteral:::
    StrWhiteSpace(opt)
    StrWhiteSpace(opt) StrNumericLiteral StrWhiteSpace(opt)

StrWhiteSpace:::
    StrWhiteSpaceChar StrWhiteSpace(opt)

StrWhiteSpaceChar:::
    WhiteSpace
    LineTerminator

StrNumericLiteral:::
    StrDecimalLiteral
    NonDecimalIntegerLiteral

StrDecimalLiteral:::
    StrUnsignedDecimalLiteral
    + StrUnsignedDecimalLiteral
    - StrUnsignedDecimalLiteral

StrUnsignedDecimalLiteral:::
    Infinity
    DecimalDigits . DecimalDigits(opt) ExponentPart(opt)
    . DecimalDigits(opt) ExponentPart(opt)
    DecimalDigits ExponentPart(opt)
```

数字字符串转换为Number

```
1. 忽略前置、后置空白
2. 空字符串转换为+0
3. Infinity、十进制数字字符串、科学计数法转换为相应的十进制数字，忽略前导零
4. 非十进制整数字符串转换为相应的十进制整数
5. 转换的数字有超过20位有效数字时，将20位后的数字置为0, 20位加或不加一
```

### ToInteger(input)

转换为整数

```
1. input转换为Number类型
2. number为NaN、+0、-0时，返回+0
3. number为无穷大时，返回number
4. number为其他，转换为整数sign(number) * floor(abs(number))
  a. integer为-0，返回+0
  b. 其他返回integer
```

### ToUInt32(input)

转换为32位无符号整数，范围为[0, 2^32 - 1]

```
1. input转换为Number类型
2. number为NaN、+0、-0、无穷大时，返回+0
3. number为其他，转换为整数sign(number) * floor(abs(number))
4. int进行modulo 2^32运算，返回余数
```

### ToInt32(input)

转换为32位有符号整数，范围为[-2^31, 2^31-1]

```
1. input转换为Number类型
2. number为NaN、+0、-0、无穷大时，返回+0
3. number为其他，转换为整数sign(number) * floor(abs(number))
4. int进行modulo 2^32运算
  a. 余数小于2^31时，直接返回
  b. 余数大于等于2^31时，返回余数 - 2^32
```

### ToUInt16(input)

转换为16位无符号整数，范围为[0, 2^16 - 1]

```
1. input转换为Number类型
2. number为NaN、+0、-0、无穷大时，返回+0
3. number为其他，转换为整数sign(number) * floor(abs(number))
4. int进行modulo 2^16运算，返回余数
```

### ToInt16(input)

转换为16位有符号整数，范围为[-2^15, 2^15-1]

```
1. input转换为Number类型
2. number为NaN、+0、-0、无穷大时，返回+0
3. number为其他，转换为整数sign(number) * floor(abs(number))
4. int进行modulo 2^16运算
  a. 余数小于2^15时，直接返回
  b. 余数大于等于2^15时，返回余数 - 2^16
```

### ToUInt8(input)

转换为8位无符号整数，范围为[0, 2^8 - 1]

```
1. input转换为Number类型
2. number为NaN、+0、-0、无穷大时，返回+0
3. number为其他，转换为整数sign(number) * floor(abs(number))
4. int进行modulo 2^8运算，返回余数
```

### ToInt8(input)

转换为8位有符号整数，范围为[-2^7, 2^7-1]

```
1. input转换为Number类型
2. number为NaN、+0、-0、无穷大时，返回+0
3. number为其他，转换为整数sign(number) * floor(abs(number))
4. int进行modulo 2^8运算
  a. 余数小于2^7时，直接返回
  b. 余数大于等于2^7时，返回余数 - 2^8
```

### ToUInt8Clamp(input)

转换为8位无符号整数，范围为[0, 2^8 - 1]，范围之外的值对应端点值

```
1. input转换为Number类型
2. number为NaN时，返回+0
3. number小于等于0时，返回0+
4. number大于等于255时，返回255
5. number四舍五入，正好为0.5时，奇入偶不入
```

### ToBigInt(input)

转换为BigInt类型

| input | result |
| --- | --- |
| Undefined | 抛出TypeError |
| Null | 抛出TypeError |
| Boolean | true转换为1n，false转换为0n |
| Number | 抛出TypeError |
| BigInt | input |
| String | BigInt字符串转换为相应的BigInt类型，其他抛出TypeError |
| Symbol | 抛出TypeError |
| Object | 先转换为原始值类型，偏向number，再转换为BigInt类型 |

BigInt字符串，同数字字符串，只有StrUnsignedDecimalLiteral不同

```
StrUnsignedDecimalLiteral:::
    DecimalDigits
```

### ToBigUint64(input)

转换为64位无符号整数，范围为[0, 2^64 - 1]

```
1. input转换为BigInt类型
2. bigInt进行modulo 2^64运算，返回余数
```

### ToBigInt64(input)

转换为64位有符号整数，范围为[-2^63, 2^63-1]

```
1. input转换为BigInt类型
2. bigInt进行modulo 2^64运算
  a. 余数小于2^63时，直接返回
  b. 余数大于等于2^63时，返回余数 - 2^64
```

### ToNumeric(input)

转换为Number或BigInt类型

```
1. 将input转换为原始值类型，偏向number
2. 原始值为BigInt类型时，直接返回
3. 原始值不为BigInt类型时，转换为Number类型返回
```

### ToString(input)

转换为String类型

| input | result |
| --- | --- |
| Undefined | `'undefined'` |
| Null | `'null'` |
| Boolean | true转换为`'true'`，false转换为`'false'` |
| Number | 返回数字操作Number::toString(input) |
| BigInt | 返回数字操作BigInt::toString(input) |
| String | input |
| Symbol | 抛出TypeError |
| Object | 先转换为原始值类型，偏向string，再转换为String类型 |

### CanonicalNumericIndexString(input)

将number转换成的字符串再转换回number

```
1. input为'-0'，返回-0
2. input为number转换的字符串时，返回number
3. 其他情况返回undefined
```

### ToObject(input)

转换为Object类型

| input | result |
| --- | --- |
| Undefined | 抛出TypeError |
| Null | 抛出TypeError |
| Boolean | 返回Boolean对象，[[BooleanData]]为input |
| Number | 返回Number对象，[[NumberData]]为input |
| BigInt | 返回BigInt对象，[[BigIntData]]为input |
| String | 返回String对象，[[StringData]]为input |
| Symbol | 返回Symbol对象，[[SymbolData]]为input |
| Object | input |

### ToPropertyKey(input)

转换为属性名，String或Symbol类型

```
1. 将input转换为原始值类型，偏向string
2. 原始值为Symbol类型时，直接返回
3. 原始值不为Symbol类型时，转换为String类型返回
```

### ToLength(input)

转换为长度，范围为[0, 2^53 - 1]

```
1. input转换为整数
2. len小于等于+0时，返回+0
3. len大于等于2^53 - 1，返回2^53 - 1
4. 其他情况返回len
```

### ToIndex(input)

转换为索引，整数类型，在length范围内

```
1. input转换为整数
2. index在length范围外，抛出RangeError
3. 其他情况返回index
```

## 类型检测

### IsInteger(input)

是否是整数

```
1. input为Number类型且有限且floor(abs(input)) === abs(input)，返回true
2. 其他返回false
```

### IsNonNegativeInteger(input)

是否是非负整数

```
1. input为整数且input大于等于0，返回true
2. 其他返回false
```

### IsStringPrefix(p, q)

p是否是q的字符串前缀

```
1. q可以由q和另一字符串r拼接成，返回true
2. 其他情况返回false
```

### RequireObjectCoercible(input)

检测能否转换为Object类型

| input | result |
| --- | --- |
| Undefined | 抛出TypeError |
| Null | 抛出TypeError |
| Boolean | input |
| Number | input |
| BigInt | input |
| String | input |
| Symbol | input |
| Object | input |

### IsExtensible(O)

对象是否是可扩展的

```
1. 返回O.[[IsExtensible]]()
```

### IsPropertyKey(input)

是否是属性名

```
1. input为String类型或Symbol类型，返回true
2. 其他返回false
```

### IsArray(input)

是否是数组

```
1. input是Array exotic object或Array exotic object的代理对象，返回true
2. 其他返回false
```

### IsCallable(input)

是否可调用

```
1. input为Object类型且有[[Call]]方法，返回true
2. 其他返回false
```

### IsConstructor(input)

是否可实例化

```
1. input为Object类型且有[[Construct]]方法，返回true
2. 其他返回false
```

### IsRegExp(input)

是否是正则

```
1. input为Object类型且有Symbol.match方法或[[RegExpMatcher]]方法，返回true
2. 其他返回false
```

### SameValueNonNumeric(x, y)

非数字类型的同类型值是否是同一个值

```
1. 都是Undefined类型，返回true
2. 都是Null类型，返回true
3. 都是Boolean类型，为同一个布尔值时返回true，否则返回false
4. 都是String类型，是相同的字符串系列时返回true，否则返回false
5. 都是Symbol类型，是同一个Symbol值时返回true，否则返回false
6. 都是Object类型，是同一个对象时返回true，否则返回false
```

### SameValue(x, y)

是否是同一个值

```
1. x、y类型不同，返回false
2. x、y为Number类型，返回数字操作Number::sameValue(x, y)
3. x、y为BigInt类型，返回数字操作BigInt::sameValue(x, y)
4. x、y为非数字类型，返回SameValueNonNumeric(x, y)
```

### SameValueZero(x, y)

是否是同一个值，+0、-0当作同一个值

```
1. x、y类型不同，返回false
2. x、y为Number类型，返回数字操作Number::sameValueZero(x, y)
3. x、y为BigInt类型，返回数字操作BigInt::sameValueZero(x, y)
4. x、y为非数字类型，返回SameValueNonNumeric(x, y)
```

### Strict Equality Comparison(x, y)

全等比较

```
1. x、y类型不同，返回false
2. x、y为Number类型，返回数字操作Number::equal(x, y)
3. x、y为BigInt类型，返回数字操作BigInt::equal(x, y)
4. x、y为非数字类型，返回SameValueNonNumeric(x, y)
```

### Abstract Equality Comparison(x, y)

相等比较

```
1. x、y类型相同，返回Strict Equality Comparison(x, y)
2. x、y一个为Undefined类型、一个为Null类型，返回true
3. x、y一个为Number类型、一个为BigInt类型，比较数字大小
4. x、y一个为数字类型、一个为String类型，String转换为相应的数字类型进行比较
5. x、y有一个为Boolean类型，转换为Number类型进行比较
6. x、y有一个为Object类型，转换为原始值类型进行比较，无偏向
```

### Abstract Relational Comparison(x, y, LeftFirst)

x是否小于y，LeftFirst指定是否x先执行

```
1. LeftFirst为true时，依次将x、y转换为原始值类型px、py，偏向number
2. LeftFirst为false时，依次将y、x转换为原始值类型py、px，偏向number
3. px、py都是String类型时，依次比较字符的Unicode码
4. px、py一个是BigInt类型，一个是String类型时
  a. 字符串为BigInt字符串时，转换为BigInt类型，比较BigInt大小
  b. 字符串不为BigInt字符串时，返回undefined
5. px、py为其他类型，转换为数字类型
  a. 转换的数字类型都是BigInt类型，比较BigInt大小
  b. 转换的数字类型都是Number类型，比较Number大小
  c. 转换的数字类型一个为BigInt类型，一个为Number类型
    i. 有NaN时，返回undefined
    ii. 无NaN时，比较值大小
```
