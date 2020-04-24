# 词法

标签（空格分隔）： 前端知识体系

---

## 非Token类

### 空白符

#### 空白字符

分隔Token

* 制表符
* 垂直制表符
* 换页符
* 各类空格符

#### 换行符

影响分号自动插入

* 回车
* 换行
* 行分隔符
* 段分隔符

### 注释

* 单行注释：`// 非行终结符`
* 多行注释：`/* 非*/字符 */`

## Token类

### 标识符名

* 字母、数字、$、_组成
* 数字不开头
* 大小写不同
* 部分属性名

#### 保留字

有语法意义的标识符名

* 关键字
* 未来保留字
* Null字面量
* Boolean字面量

#### 标识符

不为保留字的标识符名

* 变量名
* 函数名
* 形参名

### 符号

* 语法符号：`{} ;`等
* 运算符号：`+ - * /`等

### 字面量

#### Null字面量

* null

#### Boolean字面量

* true
* false

#### Number字面量

* 十进制整数
* 十进制小数
* 十进制科学计数法
* 十六进制整数

#### String字面量

"字符"或'字符'

* 普通字符
* 转义字符
* \ + 换行符

转义字符

* \0: NUL字符
* \t: 水平制表符
* \v: 垂直制表符
* \f: 换页符
* \b: 退格符
* \r: 回车符
* \n: 换行符
* \': 单引号
* \": 双引号
* \\: 反斜杠
* \x两位十六进制: 对应的ASCII码
* \u四位十六进制: 对应的Unicode字符
* \其他: 忽略\

#### 正则字面量

/pattern/flag，需要与除法表达式区分