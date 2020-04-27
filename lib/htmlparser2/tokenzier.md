# Tokenzier

标签（空格分隔）： 前端知识体系

---

## 词法解析器

将源代码字符流转换为Token流

* 源代码字符流：while循环遍历整个源代码文本
* 转换为Token流
 * 窥探：窥探当前字符所处的状态
 * 消费：状态转换时消费掉上一个状态的文本

```
_buffer: 记录源代码文本
_index: 记录解析位置
_state: 记录状态
_sectionStart: 记录状态开始的位置
_baseState: 前一个状态
```

## 文本处理

### State.Text

```
State.Text -- &字符且不在特殊标签内 --> State.BeforeEntity
    消费[_sectionStart, _index)之间的Text
    _sectionStart前进到_index
    _baseState为StateText

State.Text -- <字符 --> State.BeforeTag
    消费[_sectionStart, _index)之间的Text
    _sectionStart前进到_index
```

## 实体处理

### State.BeforeEntity

```
State.BeforeEntity -- #字符 --> State.BeforeNumericEntity

State.BeforeEntity -- 其他字符 --> State.InNamedEntity
    _index后退一步
```

### State.InNamedEntity

```
State.InNamedEntity -- ;字符 --> _baseState
    [_sectionStart, _index)之间的文本进行Entity匹配
    能严格匹配
        消费匹配的Entity
        _sectionStart前进到_index + 1
    能宽松匹配
        消费匹配的Entity
        _sectionStart前进到宽松匹配的位置 + 1
    无法匹配
        _sectionStart不动，继续解析

State.InNamedEntity -- 非字母数字字符 --> _baseState
    [_sectionStart, _index)之间的文本进行Entity匹配
    _baseState为State.Text进行宽松匹配，其他进行严格匹配
    能严格匹配
        消费匹配的Entity
        _sectionStart前进到_index
        _index后退一步
    能宽松匹配
        消费匹配的Entity
        _sectionStart前进到宽松匹配的位置 + 1
        _index后退一步
    无法匹配
        _sectionStart不动
        _index后退一步
```

### State.BeforeNumericEntity

```
State.BeforeNumericEntity -- x字符 --> State.InHexEntity

State.BeforeNumericEntity -- 其他字符 --> State.InNumericEntity
    _index后退一步
```

### State.InNumericEntity

```
State.InNamedEntity -- ;字符 --> _baseState
    [_sectionStart + 2, _index)之间的十进制数字进行codePoint匹配
    有数字匹配
        消费匹配的Entity
        _sectionStart前进到_index + 1

    无数字匹配
        _sectionStart不动，继续解析

State.InNamedEntity -- 非数字字符 --> _baseState
    [_sectionStart + 2, _index)之间的数字进行codePoint匹配
    有数字匹配
        消费匹配的Entity
        _sectionStart前进到_index
        _index后退一步

    无数字匹配
        _sectionStart不动
        _index后退一步
```

### State.InHexEntity

```
State.InHexEntity -- ;字符 --> _baseState
    [_sectionStart + 3, _index)之间的十六进制数字进行codePoint匹配
    有数字匹配
        消费匹配的Entity
        _sectionStart前进到_index + 1

    无数字匹配
        _sectionStart不动，继续解析

State.InHexEntity -- 非数字字符 --> _baseState
    [_sectionStart + 2, _index)之间的十六进制进行codePoint匹配
    有数字匹配
        消费匹配的Entity
        _sectionStart前进到_index
        _index后退一步

    无数字匹配
        _sectionStart不动
        _index后退一步
```

## 尖括号处理

### State.BeforeTagName

```
State.BeforeTagName -- /字符 --> State.BeforeClosingTagName

State.BeforeTagName -- 特殊标签内 --> State.Text

State.BeforeTagName -- ?字符 --> State.InProcessingInstruction
    _sectionStart前进到_index + 1

State.BeforeTagName -- !字符 --> State.BeforeDeclaration
    _sectionStart前进到_index + 1

State.BeforeTagName -- s字符 --> State.BeforeSpecial
    _sectionStart前进到_index

State.BeforeTagName -- 其他字符 --> State.InTagName
    _sectionStart前进到_index
```

## 指令处理

### State.InProcessingInstruction

```
State.InProcessingInstruction -- >字符 --> State.Text
    消费[_sectionStart, _index)之间的指令文本
    _sectionStart前进到_index + 1
```

## 声明处理

### State.BeforeDeclaration

```
State.BeforeDeclaration -- -字符 --> State.BeforeComment

State.BeforeDeclaration -- [字符 --> State.BeforeCdata1

State.BeforeDeclaration -- 其他字符 --> State.InDeclaration
```

### State.InDeclaration

```
State.InDeclaration -- >字符 --> State.Text
    消费[_sectionStart, _index)之间的声明
    _sectionStart前进到_index + 1
```

## 注释处理

### State.BeforeComment

```
State.BeforeComment -- -字符 --> State.InComment
    _sectionStart前进到_index + 1

State.BeforeComment -- 其他字符 --> State.InDeclaration
```

### State.InComment

```
State.InComment -- -字符 --> State.AfterComment1
```

### State.AfterComment1

```
State.AfterComment1 -- -字符 --> State.AfterComment2

State.AfterComment1 -- 其他字符 --> State.InComment
    _index后退一步
```

### State.AfterComment2

```
State.AfterComment2 -- >字符 --> State.Text
    消费[_sectionStart, _index - 2)之间的注释
    _sectionStart前进到_index + 1

State.AfterComment2 -- -字符 --> State.AfterComment2

State.AfterComment2 -- 其他字符 --> State.InComment
```

## 字符数据处理

### State.BeforeData1

```
State.BeforeData1 -- C字符 --> State.BeforeData2

State.BeforeData1 -- 其他字符 --> State.InDeclaration
    _index后退一步
```

### State.BeforeData2

```
State.BeforeData2 -- D字符 --> State.BeforeData3

State.BeforeData2 -- 其他字符 --> State.InDeclaration
    _index后退一步
```

### State.BeforeData3

```
State.BeforeData3 -- A字符 --> State.BeforeData4

State.BeforeData3 -- 其他字符 --> State.InDeclaration
    _index后退一步
```

### State.BeforeData4

```
State.BeforeData4 -- T字符 --> State.BeforeData5

State.BeforeData4 -- 其他字符 --> State.InDeclaration
    _index后退一步
```

### State.BeforeData5

```
State.BeforeData5 -- A字符 --> State.BeforeData6

State.BeforeData5 -- 其他字符 --> State.InDeclaration
    _index后退一步
```

### State.BeforeData6

```
State.BeforeData6 -- [字符 --> State.InCdata
    _sectionStart前进到_index + 1

State.BeforeData6 -- 其他字符 --> State.InDeclaration
    _index后退一步
```

### State.InCdata

```
State.InCdata -- ]字符 --> State.AfterCdata1
```

### State.AfterCdata1

```
State.AfterCdata1 -- ]字符 --> State.AfterCdata2

State.AfterCdata1 -- 其他字符 --> State.InCdata
```

### State.AfterCdata2

```
State.AfterCdata2 -- >字符 --> State.Text
    消费[_sectionStart, _index - 2)之间的cdata
    _sectionStart前进到_index + 1

State.AfterCdata2 -- ]字符 --> State.AfterCdata2

State.AfterCdata2 -- 其他字符 --> State.InCdata
```

## 开始标签处理

### State.InTagName

```
State.InTagName -- / 或 > 或 空白字符 --> State.BeforeAttributeName
    消费[_sectionStart, _index)之间的标签名
    _sectionStart置为-1
    _index后退一步
```

### State.InSelfClosingTag

```
State.InSelfClosingTag -- >字符 --> State.Text
    _sectionStart前进到_index + 1

State.InSelfClosingTag -- 空白字符 --> State.InSelfClosingTag

State.InSelfClosingTag -- 其他字符 --> State.BeforeAttributeName
    _index后退一步
```

## 开始特殊标签处理

* 特殊标签内实体不解析
* 特殊标签内标签不解析

### State.BeforeSpecial

```
State.BeforeSpecial -- C字符 --> State.BeforeScript1

State.BeforeSpecial -- T字符 --> State.BeforeStyle1

State.BeforeSpecial -- 其他字符 --> State.InTagName
    _index后退一步
```

### State.BeforeScript1

```
State.BeforeScript1 -- R字符 --> State.BeforeScript2


State.BeforeScript1 -- 其他字符 --> State.InTagName
    _index后退一步
```

### State.BeforeScript2

```
State.BeforeScript2 -- I字符 --> State.BeforeScript3


State.BeforeScript2-- 其他字符 --> State.InTagName
    _index后退一步
```

### State.BeforeScript3

```
State.BeforeScript3 -- P字符 --> State.BeforeScript4


State.BeforeScript3 -- 其他字符 --> State.InTagName
    _index后退一步
```

### State.BeforeScript4

```
State.BeforeScript4 -- T字符 --> State.BeforeScript5


State.BeforeScript4 -- 其他字符 --> State.InTagName
    _index后退一步
```

### State.BeforeScript5

```
State.BeforeScript5 -- > 或 / 或 空白字符 --> _special为Special.Script


State.BeforeScript5 -- 任意字符 --> State.InTagName
    _index后退一步
```

### State.BeforeStyle1

```
State.BeforeStyle1 -- Y字符 --> State.BeforeStyle2


State.BeforeStyle1 -- 其他字符 --> State.InTagName
    _index后退一步
```

### State.BeforeStyle2

```
State.BeforeStyle2 -- L字符 --> State.BeforeStyle3


State.BeforeStyle2-- 其他字符 --> State.InTagName
    _index后退一步
```

### State.BeforeStyle3

```
State.BeforeStyle3 -- E字符 --> State.BeforeStyle4


State.BeforeStyle3 -- 其他字符 --> State.InTagName
    _index后退一步
```

### State.BeforeStyle4

```
State.BeforeStyle4 -- > 或 / 或 空白字符 --> _special为Special.Style


State.BeforeStyle4 -- 任意字符 --> State.InTagName
    _index后退一步
```

## 结束标签处理

### State.BeforeClosingTagName

```
State.BeforeClosingTagName -- 空白字符 --> State.BeforeClosingTagName

State.BeforeClosingTagName -- _special为特殊且为s字符 --> State.BeforeSpecialEnd

State.BeforeClosingTagName -- _special为特殊且不为s字符 --> State.Text
    _index后退一步

State.InSelfClosingTag -- 其他字符 --> State.InClosingTagName
    _sectionStart前进到_index
```

### State.InClosingTagName

```
State.InClosingTagName -- > 或 空白字符 --> State.AfterClosingTagName
    消费[_sectionStart, _index)之间的结束标签名
    _sectionStart置为-1
    _index后退一步
```

### State.AfterClosingTagName

```
State.AfterClosingTagName -- >字符 --> State.Text
    _sectionStart前进到_index + 1
```

## 结束特殊标签处理

### State.BeforeSpecialEnd

```
State.BeforeSpecialEnd -- _special为Special.Script且为c字符 --> State.AfterScript1

State.BeforeSpecialEnd -- _special为Special.Style且为t字符 --> State.AfterStyle1

State.InSelfClosingTag -- 其他字符 --> State.Text
    _index后退一步
```

### State.AfterScript1

```
State.AfterScript1 -- R字符 --> State.AfterScript2


State.AfterScript1 -- 其他字符 --> State.Text
    _index后退一步
```

### State.AfterScript2

```
State.AfterScript2 -- I字符 --> State.AfterScript3


State.AfterScript2-- 其他字符 --> State.Text
    _index后退一步
```

### State.AfterScript3

```
State.AfterScript3 -- P字符 --> State.AfterScript4


State.AfterScript3 -- 其他字符 --> State.Text
    _index后退一步
```

### State.AfterScript4

```
State.AfterScript4 -- T字符 --> State.AfterScript5


State.AfterScript4 -- 其他字符 --> State.Text
    _index后退一步
```

### State.AfterScript5

```
State.AfterScript5 -- > 或 空白字符 --> State.InClosingTagName
    _special为Special.None
    _sectionStart后退到_index - 6
    _index后退一步

State.AfterScript5 -- 任意字符 --> State.Text
    _index后退一步
```

### State.AfterStyle1

```
State.AfterStyle1 -- Y字符 --> State.AfterStyle2


State.AfterStyle1 -- 其他字符 --> State.Text
    _index后退一步
```

### State.AfterStyle2

```
State.AfterStyle2 -- L字符 --> State.AfterStyle3


State.AfterStyle2-- 其他字符 --> State.Text
    _index后退一步
```

### State.AfterStyle3

```
State.AfterStyle3 -- E字符 --> State.AfterStyle4


State.AfterStyle3 -- 其他字符 --> State.Text
    _index后退一步
```

### State.AfterStyle4

```
State.AfterStyle4 -- > 或 空白字符 --> State.InClosingTagName
    _sectionStart后退到_index - 5
    _index后退一步
    _special为Special.None


State.AfterStyle4 -- 其他字符 --> State.Text
    _index后退一步
```

## 属性名处理

### State.BeforeAttributeName

```
State.BeforeAttributeName -- >字符 --> State.Text
    _sectionStart前进到_index + 1

State.BeforeAttributeName -- /字符 --> State.InSelfClosingTag

State.BeforeAttributeName -- 空白字符 --> State.BeforeAttributeName

State.BeforeAttributeName -- 其他字符 --> State.InAttributeName
    _sectionStart前进到_index
```

### State.InAttributeName

```
State.InTagName -- = 或 / 或 > 或 空白字符 --> State.AfterAttributeName
    消费[_sectionStart, _index)之间的标签名
    _sectionStart置为-1
    _index后退一步
```

### State.AfterAttributeName

```
State.AfterAttributeName -- / 或 > --> State.BeforeAttributeName
    _index后退一步

State.AfterAttributeName -- =字符 --> State.BeforeAttributeValue

State.AfterAttributeName -- 空白字符 --> State.BeforeAttributeValue

State.AfterAttributeName -- 其他字符 --> State.InAttributeName
    _sectionStart前进到_index
```

## 属性值处理

### State.BeforeAttributeValue

```
State.BeforeAttributeValue -- "字符 --> State.InAttributeValueDq
    _sectionStart前进到_index + 1

State.BeforeAttributeValue -- '字符 --> State.InAttributeValueSq
    _sectionStart前进到_index + 1

State.BeforeAttributeValue -- 空白字符 --> State.BeforeAttributeValue

State.BeforeAttributeValue -- 其他字符 --> State.InAttributeNq
    _sectionStart前进到_index
    _index后退一步
```

### State.InAttributeValueDq

```
State.InAttributeValueDq -- "字符 --> State.BeforeAttributeName
    消费[_sectionStart, _index)之间的属性值
    _sectionStart置为-1

State.InAttributeValueDq -- &字符 --> State.BeforeEntity
    消费[_sectionStart, _index)之间的属性值
    _sectionStart前进到_index
    _baseState为InAttributeValueDq
```

### State.InAttributeValueSq

```
State.InAttributeValueSq -- '字符 --> State.BeforeAttributeName
    消费[_sectionStart, _index)之间的属性值
    _sectionStart置为-1

State.InAttributeValueSq -- &字符 --> State.BeforeEntity
    消费[_sectionStart, _index)之间的属性值
    _sectionStart前进到_index
    _baseState为InAttributeValueSq
```

### State.InAttributeNq

```
State.InAttributeNq -- > 或 空白字符 --> State.BeforeAttributeName
    消费[_sectionStart, _index)之间的属性值
    _index后退一步

State.InAttributeNq -- &字符 --> State.BeforeEntity
    消费[_sectionStart, _index)之间的属性值
    _sectionStart前进到_index
    _baseState为InAttributeNq
```
