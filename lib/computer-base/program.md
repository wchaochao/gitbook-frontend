# 编程基础

标签（空格分隔）： 前端知识体系

---

## Algorithms

* step: 表示算法的一个步骤

### Variables

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
Let example be then list<1, 2, 3, 4>
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

原始值类型

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

#### 属性

* length: 字节数

#### 操作

* byte-lowercase: 转换为小写
* byte-uppercase: 转换为大写
* a starts with b:
