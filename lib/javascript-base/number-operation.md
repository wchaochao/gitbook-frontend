# 数字操作

标签（空格分隔）： 前端知识体系

---

## Number操作

### Number::unaryMinus(x)

取负

```
1. x为NaN，返回NaN
2. 其他情况返回-x
```

### Number::add(x, y)

加法

```
1. x、y有一个为NaN，返回NaN
2. x、y一个为正无穷，一个为负无穷，返回NaN
3. x、y有一个为无穷，返回这个操作数
4. x、y都为0
  a. 都为-0时，返回-0
  b. 其他情况返回+0
5. x、y为相反数，返回+0
6. 其他情况进行加法运算
```

### Number::subtract(x, y)

减法

```
1. 相当于x + (-y)
```

### Number::multiply(x, y)

乘法

```
1. x、y有一个为NaN，返回NaN
2. x、y一个为0，一个为无穷，返回NaN
3. x、y有一个为无穷，返回无穷，符号为两个操作数的符号之积
4. 其他情况进行乘法运算
```

### Number::divide(x, y)

除法

```
1. x、y有一个为NaN，返回NaN
2. x、y都为无穷，返回NaN
3. x为无穷，返回无穷，符号为两个操作数的符号之积
4. y为无穷，返回0，符号为两个操作数的符号之积
5. x、y都为0 ，返回NaN
6. x为0，返回0，符号为两个操作数的符号之积
7. y为0，返回无穷，符号为两个操作数的符号之积
8. 其他情况进行除法运算
```

### Number::remainder(x, y)

求余

```
1. x、y有一个为NaN，返回NaN
2. x为无穷或y为0，返回NaN
3. x为0或y为无穷，返回被除数
4. 其他情况进行绝对值求余运算，符号为被除数符号
```

### Number::lessThan(x, y)

小于

```
1. x、y有一个为NaN，返回undefined
2. x、y为相同的数，返回false
3. x、y一个为+0、一个为-0，返回false
4. x为正无穷，返回false
5. y为正无穷，返回true
6. x为负无穷，返回true
7. y为负无穷，返回false
8. x小于y，返回true
9. 其他情况返回false
```

### Number::equal(x, y)

相等

```
1. x、y有一个为NaN，返回false
2. x、y都为0，返回true
3. x、y为相同的数，返回true
4. 其他情况返回false
```

### Number::sameValue(x, y)

同一个数

```
1. x、y都为NaN时，返回true
2. x、y一个为+0，一个为-0，返回false
3. x、y为相同的数，返回true
4. 其他情况返回false
```

### Number::sameValueZero(x, y)

同一个数，包括+0、-0

```
1. x、y都为NaN时，返回true
2. x、y一个为+0，一个为-0，返回true
3. x、y为相同的数，返回true
4. 其他情况返回false
```

### Number::exponentiate(base, exponent)

指数

```
1. exponent为NaN时，返回NaN
2. exponent为0时，返回1
3. base为NaN时，返回NaN
4. base的绝对值大于1时
  a. exponent为正无穷时，返回正无穷
  b. exponent为负无穷时，返回+0
5. base的绝对值等于1时，exponent为无穷时，返回NaN
6. base的绝对值小于1时
  a. exponent为正无穷时，返回+0
  b. exponent为负无穷时，返回正无穷
7. base为正无穷时
  a. exponent大于0时，返回正无穷
  b. exponent小于0时，返回+0
8. base为负无穷时
  a. exponent大于0且为奇数时，返回负无穷
  b. exponent大于0且不为奇数时，返回正无穷
  c. exponent小于0且为奇数时，返回-0
  d. exponent小于0且不为奇数时，返回+0
9. base为+0时
  a. exponent大于0时，返回+0
  b. exponent小于0时，返回正无穷
8. base为-0时
  a. exponent大于0且为奇数时，返回-0
  b. exponent大于0且不为奇数时，返回+0
  c. exponent小于0且为奇数时，返回负无穷
  d. exponent小于0且不为奇数时，返回正无穷
10. base为负数且exponent不为整数时，返回NaN
11. 其他情况进行指数运算
```

### Number::bitwiseNOT(x)

取反

```
1. x转换为Int32类型
2. 按位取反，结果为Int32类型
```

### NumberBitwiseOp(op, x, y)

位操作

```
1. x、y转换为Int32类型
2. op为&时，x、y进行按位与操作
3. op为|时，x、y进行按位或操作
4. op为^时，x、y进行按位异或操作
5. 结果为Int32类型
```

### Number::bitwiseAND(x, y)

按位与

```
1. 相当于NumberBitwiseOp(&, x, y)
```

### Number::bitwiseOR(x, y)

按位或

```
1. 相当于NumberBitwiseOp(|, x, y)
```

### Number::bitwiseXOR(x, y)

按位异或

```
1. 相当于NumberBitwiseOp(^, x, y)
```

### Number::leftShift(x, y)

左移

```
1. x转换为Int32类型
2. y转换为Uint32类型，取最后5位为ShiftCount
3. x左移ShiftCount位，右补0，结果为Int32类型
```

### Number::signedRightShift(x, y)

有符号右移

```
1. x转换为Int32类型
2. y转换为Uint32类型，取最后5位为ShiftCount
3. x右移ShiftCount位，左补符号位，结果为Int32类型
```

### Number::unsignedRightShift(x, y)

无符号右移

```
1. x转换为Uint32类型
2. y转换为Uint32类型，取最后5位为ShiftCount
3. x右移ShiftCount位，左补0，结果为Uint32类型
```

### Number::toString(x)

字符串表示

```
1. x为NaN，返回'NaN'
2. x为+0或-0，返回'0'
3. x小于0，返回'-'拼接Number::toString(-x)
4. x为正无穷，返回'Infinity'
5. x小数点前的数字超过21位或小数点后紧跟的0超过5位，返回科学计数法字符串
6. 其他情况，返回数字字符串
```

## BigInt操作

### BigInt::unaryMinus(x)

整数取负

```
1. x为0n，返回0n
2. 其他情况返回-x
```

### BigInt::add(x, y)

整数加法

```
1. 进行加法运算
```

### BigInt::subtract(x, y)

整数减法

```
1. 进行减法运算
```

### BigInt::multiply(x, y)

整数乘法

```
1. 进行乘法运算
```

### BigInt::divide(x, y)

整数除法

```
1. y为0n，抛出RangeError
2. 其他情况进行除法运算，向0取整
```

### BigInt::remainder(x, y)

整数求余

```
1. y为0n，抛出RangeError
2. x为0n，返回0n
3. 其他情况进行绝对值求余运算，符号为被除数符号
```

### BigInt::lessThan(x, y)

整数小于

```
1. x小于y，返回true
2. 其他情况返回false
```

### BigInt::equal(x, y)

整数相等

```
1. x、y为相同的数，返回true
2. 其他情况返回false
```

### BigInt::sameValue(x, y)

同一个整数

```
1. 相当于BigInt::equal(x, y)
```

### BigInt::sameValueZero(x, y)

同一个整数，包括0

```
1. 相当于BigInt::equal(x, y)
```

### BigInt::exponentiate(base, exponent)

整数指数

```
1. exponent小于0n, 抛出RangeError
2. base为0n且exponent为0n，返回1n
3. 其他情况进行指数运算
```

### BigInt::bitwiseNOT(x)

整数取反

```
1. 按位取反，返回-x-1
```

### BigIntBitwiseOp(op, x, y)

整数位操作

```
1. op为&时，x、y进行按位与操作
2. op为|时，x、y进行按位或操作
3. op为^时，x、y进行按位异或操作
```

### BigInt::bitwiseAND(x, y)

整数按位与

```
1. 相当于BigIntBitwiseOp(&, x, y)
```

### BigInt::bitwiseOR(x, y)

整数按位或

```
1. 相当于BigIntBitwiseOp(|, x, y)
```

### BigInt::bitwiseXOR(x, y)

整数按位异或

```
1. 相当于BigIntBitwiseOp(^, x, y)
```

### BigInt::leftShift(x, y)

整数左移

```
1. y小于0n, 返回x / 2^y, 向最近整数近似
2. 其他情况返回x * 2^y
```

### BigInt::signedRightShift(x, y)

整数有符号右移

```
1. 相当于BigInt::leftShift(x, -y)
```

### BigInt::unsignedRightShift(x, y)

整数无符号右移

```
1. 抛出TypeError
```

### BigInt::toString(x)

整数字符串表示

```
1. x小于0，返回'-'拼接BigInt::toString(-x)
2. 其他情况，返回BigInt字符串
```
