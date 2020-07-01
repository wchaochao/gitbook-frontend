# Infra

标签（空格分隔）： 前端知识体系

---

## Algorithm

算法

* step: 表示算法的一个步骤

### Variable

变量

* let: 声明变量
* set: 设置变量

```
1. Let value be null
2. If input is a string, then set value to input
```

### Control flow

控制流

* return: 返回值
* throw: 抛出错误

```
If input is then empty string, then return null
```

### Conditional abort

条件中断

* abort when: 在某种条件下中断subsequent step的执行
* if aborted: 在中断后执行

```
1. Let result be an empty list
2. Run these steps, but abort when the user clicks then "Cancel" button
    1. Compute the first million digits of π, and append the result to result
    2. Compute the first million digits of e, and append the result to result
    3. Compute the first million digits of φ, and append the result to result
3. If aborted, append "Didn't finish!" to result
```

### Iteration

迭代

* For each: 遍历list和map
* While: 条件为真时重复执行

迭代控制

* continue: 进入下一次循环
* break: 退出循环

```
Let example be then list<<1, 2, 3, 4>>
1. For each item of example
    1. Perform operation on item
2. For each item of example
    1. If item is 3, then continue
    2. Perform operation on item
3. For each item of example
    1. If item is 3, then break
    2. Perform operation on item

While condition is met:
    1. ...
```

### Assertions

断言

* Assert: 断言语句为真

```
1. Let x be "Aperturn Science"
2. Assert: x is a string
```

## Primitive data types

原始数据类型

### Null

空值

```
Let element be null
```

### Boolean

布尔值, true or false

```
Let elementSeen be false
```

### Byte

字节，八位二进制，使用0x00 ~ 0xFF表示

```
0x40 is a byte whose value is 64
```

### Byte sequence

字节系列，使用空格分隔的Byte表示

```
0x48 0x49 can also be represented as `HI`
```

#### 概念

* length: 字节数

#### 操作

* start with: 以指定字节序列开头
* byte less than: 按字节比较小于指定字节序列
* byte-lowercase: 转换为小写
* byte-uppercase: 转换为大写

### Code Point

Unicode码点，使用U+0000 ~ U+10FFFF表示

```
The code point rendered as 🤔 is represented as U+1F914.
```

#### 概念

* ASCII whitespace: ASCII空白字符码点，U+000D CR, U+000A LF, U+0009 TAB, U+000C FF, or U+0020 SPACE
* ASCII digit: ASCII数字码点，U+0030 (0) ~ U+0039 (9)
* ASCII upper hex digit: ASCII大写十六进制码点，ASCII digit加上U+0041 (A) ~ U+0046 (F)
* ASCII lower hex digit: ASCII小写十六进制码点，ASCII digit加上U+0061 (a) ~ U+0066 (f)
* ASCII hex digit: ASCII十六进制码点，ASCII upper hex digit加上ASCII lower hex digit
* ASCII upper alpha: ASCII大写字母码点，U+0041 (A) ~ U+005A (Z)
* ASCII lower alpha: ASCII小写字母码点，U+0061 (a) ~ U+007A (z)
* ASCII alpha: ASCII字母码点，ASCII upper alpha加上ASCII lower alpha
* ASCII alphanumeric: ASCII字母加数字
* ASCII code point: ASCII字符码点，U+0000 ~ U+007F
* surrogate: 代理区码点，U+D800 ~ U+DFFF
* scalar value: 非代理区码点

#### 操作

* Code point与Byte sequence通过UTF编码进行转换

### String

字符串，UTF-16编码的Code Point，使用"字符串"表示

```
"Hello, world!" is a string
```

#### 概念

* length: 双字节码元个数
* code point length: 字符个数

#### 操作

* is: 相同的码元系列
* start with: 以指定字符串开头
* code unit less than: 按码元比较小于指定字符串
* ASCII lowercase: 转换为ASCII小写
* ASCII upper: 转换为ASCII大写
* ASCII encode: 将ASCII字符串转换为ASCII byte
* ASCII decode: 将ASCII byte转换为ASCII字符串
* strip newlines: 移除CR、LF
* normalize newlinews: CRLF、CR替换为LF
* strip leading and trailing ASCII whitespace: 去除首尾ASCII空白字符
* strip and collapse ASCII whitespace: 去除首尾ASCII空白字符，并将中间连续的空白字符替换为一个空格
* collect: 收集符合条件的子字符串
* split: 以指定分隔符将字符串分隔为字符串列表
* concatenate：以指定分隔符将字符串列表连接为字符串

## Data structures

数据结构

### List

列表，有限的列表项系列，使用<<列表项1, 列表项2>>表示

```
Let example be the list <<"a", "b", "c", "a">>. Then example[1] is the string "b"
```

#### 概念

* [index]: 索引对应的列表项，从0开始
* size: 列表项个数

#### 操作

* insert: 插入列表项
* append: 往后追加列表项
* prepend: 往前追加列表项
* extend: 拼接列表
* remove: 移除符合条件的列表项
* empty: 清空列表
* replace: 替换符合条件的列表项
* contains: 是否包含列表项
* iterate: 遍历列表项
* clone: 克隆列表
* sort: 列表排序

### Stack

栈，先进后出的列表

#### 操作

* push: 进栈
* pop: 出栈
* 使用while和pop遍历

### Queue

队列，先进先出的列表

#### 操作

* enqueue: 进队列
* dequeue: 出队列
* 使用while和dequeue遍历

### Set

套、组，去重的列表

#### 概念

* subset: 子集
* superset: 超集
* range: [n, m]的Set

```
For each n of the range 1 to 4, inclusive
```

#### 操作

* append: 不存在时往后追加列表项
* prepend: 不存在时往前追加列表项
* replace: item或replacement存在时，将第一个替换为replacement，其他移除
* insersection: 交集
* union: 并集

```
Replacing "a" with "c" within the ordered set <<"a", "b", "c">> gives <<"c", "b">>. Within <<"c", "b", "a">> it gives <<"c", "b">> as well
```

### Map

映射，有限的key/value对（entry），key不重复，使用<<[key1 -> value1, key2 -> value2]>>表示

```
Let example be the ordered map <<["a" -> `x`, "b" -> `y`]>>. Then example["a"] is the byte sequence `x`
```

#### 概念

* [key]: key对应的value值
* size: entry的个数

#### 操作

* get: 获取key对应的value, 不存在时为null
* set: 设置key对应的value, 不存在时追加key -> value
* remove: 移除符合条件的entry
* contains: 是否含有key
* keys: 所有key组成的ordered set
* values: 所有value组成的ordered set
* iterate: 遍历entry
* clone: 克隆map
* sort: 排序map

### Struct

元组，一组有限的items, 每个item有个唯一的名字，使用(item1, item2)表示

```
A status is an example tuple consisting of a code and text.
Let statusInstance be the status (200, `OK`)
```
