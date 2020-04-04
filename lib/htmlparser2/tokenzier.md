# Tokenzier

标签（空格分隔）： 前端知识体系

---

## 介绍

词法解析器，将源代码字符流转换为Token流

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

## State.Text

```
State.Text -- &字符 --> State.BeforeEntity
    消费[_sectionStart, _index)之间的Text
    _sectionStart前进到_index
    _baseState为StateText

State.Text -- <字符 --> State.BeforeTag
    消费[_sectionStart, _index)之间的Text
    _sectionStart前进到_index

State.Text -- 其他字符 --> State.Text
    继续解析
```

## State.BeforeEntity

```
State.BeforeEntity -- #字符 --> State.BeforeNumericEntity

State.BeforeEntity -- 其他字符 --> State.InNamedEntity
    _index后退一步
```

## State.InNamedEntity

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
        _sectionStart不动，_index后退一步，继续解析
```

## State.BeforeNumericEntity

```
State.BeforeNumericEntity -- x字符 --> State.InHexEntity

State.BeforeNumericEntity -- 其他字符 --> State.InNumericEntity
    _index后退一步
```

## State.InNumericEntity

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
        _sectionStart不动，_index后退一步，继续解析
```

## State.InHexEntity

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
        _sectionStart不动，_index后退一步，继续解析
```
