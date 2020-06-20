# 数字操作

标签（空格分隔）： 前端知识体系

---

## Number操作

### Number::unaryMinus(x)

取负

1. x为NaN，返回NaN
2. 其他情况返回-x

### Number::bitwiseNOT(x)

取反

1. x转换为Int32类型
2. 按位取反，结果为Int32类型

### Number::exponentiate(base, exponent)

指数

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
 a. exponent小于0且为奇数时，返回-0
 b. exponent小于0且不为奇数时，返回+0
9. base为+0时
 a. exponent大于0时，返回+0
 b. exponent小于0时，返回正无穷
8. base为-0时
 a. exponent大于0且为奇数时，返回-0
 b. exponent大于0且不为奇数时，返回+0
 a. exponent小于0且为奇数时，返回负无穷
 b. exponent小于0且不为奇数时，返回正无穷
10. base为负数且exponent不为整数时，返回NaN
11. 其他情况进行指数运算

### Number::multiply(x, y)

乘法

1. x、y有一个为NaN，返回NaN
2. x、y一个为0，一个为无穷，返回NaN
3. x、y有一个为无穷，返回无穷，符号为两个操作数的符号之积
4. 其他情况进行乘法运算

### Number::divide(x, y)

除法

1. x、y有一个为NaN，返回NaN
2. x、y都为无穷，返回NaN
3. x为无穷，返回无穷，符号为两个操作数的符号之积
4. y为无穷，返回0，符号为两个操作数的符号之积
5. x、y都为0 ，返回NaN
6. x为0，返回0，符号为两个操作数的符号之积
7. y为0，返回无穷，符号为两个操作数的符号之积
8. 其他情况进行除法运算

### Number::remainder(n, d)

求余

1. n、d有一个为NaN，返回NaN
2. n为无穷或d为0，返回NaN
3. d为无穷或n为0，返回被除数
4. 其他情况进行绝对值求余运算，符号为被除数符号

### Number::add(x, y)

加法

1. x、y有一个为NaN，返回NaN
2. x、y一个为正无穷，一个为负无穷，返回NaN
3. x、y有一个为无穷，返回这个操作数
4. x、y都为0
 a. 都为-0时，返回-0
 b. 其他情况返回+0
5. x、y为相反数，返回+0
6. 其他情况进行加法运算

### Number::subtract(x, y)

减法

1. 相当于x + (-y)

### Number::leftShift(x, y)

左移

1. x转换为Int32类型
2. y转换为Uint32类型，取最后5位为ShiftCount
3. x左移ShiftCount位，右补0，结果为Int32类型

### Number::signedRightShift(x, y)

有符号右移

1. x转换为Int32类型
2. y转换为Uint32类型，取最后5位为ShiftCount
3. x右移ShiftCount位，左补符号位，结果为Int32类型

### Number::unsignedRightShift(x, y)

无符号右移

1. x转换为Uint32类型
2. y转换为Uint32类型，取最后5位为ShiftCount
3. x右移ShiftCount位，左补0，结果为Uint32类型

### Number::lessThan(x, y)

小于

1. x、y有一个为NaN，返回undefined
2. x、y为相同的数，返回false
2. x、y一个为+0、一个为-0，返回false
3. x为正无穷，返回false
4. y为正无穷，返回true
5. y为负无穷，返回false
6. x为负无穷，返回true
7. x大于y，返回true
8. 其他情况返回false

### Number::equal(x, y)

相等

1. x、y有一个为NaN，返回false
2. x、y都为0，返回true
3. x、y为相同的数，返回true
4. 其他情况返回false

### Number::sameValue(x, y)

同一个数

1. x、y都为NaN时，返回true
2. x、y一个为+0，一个为-0，返回false
3. x、y为相同的数，返回true
4. 其他情况返回false

### Number::sameValueZero(x, y)

同一个数，包括+0、-0

1. x、y都为NaN时，返回true
2. x、y一个为+0，一个为-0，返回true
3. x、y为相同的数，返回true
4. 其他情况返回false

### NumberBitwiseOp(op, x, y)

位操作

1. x、y转换为Int32类型
2. op为&时，x、y进行按位与操作
3. op为|时，x、y进行按位或操作
4. op为^时，x、y进行按位异或操作
5. 结果为Int32类型

### Number::bitwiseAND(x, y)

按位与

1. 相当于NumberBitwiseOp(&, x, y)

### Number::bitwiseOR(x, y)

按位或

1. 相当于NumberBitwiseOp(|, x, y)

### Number::bitwiseXOR(x, y)

按位异或

1. 相当于NumberBitwiseOp(^, x, y)

### Number::toString(x)

1. x为NaN，返回'NaN'
2. x为+0或-0，返回'0'
3. x小于0，返回'-'拼接Number::toString(-x)
4. x为正无穷，返回'Infinity'
5. x小数点前的数字超过21位或小数点后紧跟的0超过5位，返回科学计数法字符串
6. 其他情况，返回数字字符串
