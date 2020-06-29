# Encoding

标签（空格分隔）： 前端知识体系

---

非代理区字符流与字节流之间的转换

## 术语

* token: a piece of data, 如字符或字节
* stream: token流
* End-of-stream: 特殊的token，标志stream结束
* read: 从stream中读取token
* prepend: 将token追加到stream开头
* push: 将token追加到stream结尾
* index: 编码和码点组成的索引表
* index pointer: 索引编号
* inde code point: 索引编码对应的code point

## 构成

* name: Encoding名称，如UTF-8
* label: Encoding别名，如utf-8, utf8
* encoder: 编码器，将非代理区字符流转换为字节流
* decoder: 解码器，将字节流转换为非代理去字符流
* error mode: 错误模式
 * replacement: 解码器的默认错误模式，无法解码时解码为替换字符U+FFFD
 * fatal: 编码器的默认错误模式，无法编码/解码时返回错误
 * html: 无法编码时给input流prepend字符串`&#错误的code point十进制表示;`

### 编码

将非代理区字符流转换为字节流

1. 从input流中读取token
2. 使用encoder将token转换为对应的字节
3. 转换成功时将字节push进output流，继续读取下一个token，直至End-of-stream
4. 转换失败时根据error mode进行处理
 a. error mode为fatal时，返回error，不再往下处理
 b. error mode为html时，给input流prepend `&#错误的code point十进制表示;`，继续处理下一个token

### 解码

将字节流转换为非代理去字符流

1. 从input流中读取token
2. 使用decoder将token转换为对应的字符
3. 转换成功时将字符push进output流，继续读取下一个token，直至End-of-stream
4. 转换失败时根据error mode进行处理
 a. error mode为fatal时，返回error，不再往下处理
 b. error mode为replacement时，给output流push 替换字符U+FFFD，继续处理下一个token

*BOM*: 考虑BOM时对input流进行BOM检测，有BOM时使用BOM指定的Encoding，从BOM后开始解码

## TextDecoderCommon接口

```javascript
interface mixin TextDecoderCommon {
  readonly attribute DOMString encoding;
  readonly attribute boolean fatal;
  readonly attribute boolean ignoreBOM;
}
```

### 内部属性

* encoding: 编码方式
* decoder: 解码器
* stream: 输入字节流
* ignore BOM: 是否忽略BOM，默认为false
* BOM seen: BOM是否存在，默认为false
* error mode: 错误模式，默认为replacement

### 特征属性

* encoding: decoder的小写的encoding name
* fatal: decoder的error mode是否为fatal
* ignoreBOM: decoder的ignore BOM

## TextDecoder接口

```javascript
interface TextDecoder includes TextDecoderCommon {
  constructor(optional DOMString label = "utf-8", optional TextDecoderOptions options = {})

  USVString decode(optional BufferSource input, optional TextDecodeOptions options = {})
}

directionary TextDecoderOptions {
  boolean fatal = false;
  boolean ignoreBOM = falst;
}

direction TextDecodeOptions {
  boolean stream = false;
}
```

### 内部属性

* do not flush: 是否保留最后不完整的字节

### new TextDecoder(label, options)

创建TextDecoder对象，初始化encoding, error mode, ignore BOM属性

### decode(input, options)

将字节流解码为字符串

1. do not flush为false时，设置decoder, stream为新创建的decoder, stream
2. 设置do not flush属性为options.stream，将input流push进stream
3. 使用decoder对stream进行解码
4. 对解码得到的字符流进行serialize，返回字符串
