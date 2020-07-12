# Encoding

标签（空格分隔）： 前端知识体系

---

非代理区码点流与字节流之间的转换

## 术语

* token: a piece of data, 如字节或非代理区码点
* stream: token流
* End-of-stream: 特殊的token，标志stream结束
* read: 从stream中读取token
* prepend: 将token追加到stream开头
* push: 将token追加到stream结尾
* index: 编号和码点组成的索引表
* index pointer: 索引表编号
* inde code point: 索引表编号对应的码点

## 构成

* name: Encoding名称，如UTF-8
* label: Encoding别名，如utf-8, utf8
* decoder: 解码器，将字节流转换为非代理区码点流
* encoder: 编码器，将非代理区码点流转换为字节流
* error mode: 错误模式
 * replacement: 解码器的默认错误模式，无法解码时解码为替换字符码点U+FFFD
 * fatal: 编码器的默认错误模式，无法编码/解码时返回错误
 * html: 无法编码时给input流prepend字符串`&#错误的code point十进制表示;`的码点

### 解码

将字节流转换为非代理区码点流

```
1. 从input流中读取token
2. 使用decoder将token转换为对应的码点
3. 转换成功时将码点push进output流，继续读取下一个token，直至End-of-stream
4. 转换失败时根据error mode进行处理
 a. error mode为replacement时，给output流push 替换字符码点U+FFFD，继续处理下一个token
 b. error mode为fatal时，返回error，不再往下处理
```

*BOM*: 考虑BOM时对input流进行BOM检测，有BOM时使用BOM指定的Encoding，从BOM后开始解码

### 编码

将非代理区码点流转换为字节流

```
1. 从input流中读取token
2. 使用encoder将token转换为对应的字节
3. 转换成功时将字节push进output流，继续读取下一个token，直至End-of-stream
4. 转换失败时根据error mode进行处理
 a. error mode为fatal时，返回error，不再往下处理
 b. error mode为html时，给input流prepend字符串`&#error的code point的十进制表示;`的码点，继续处理下一个token
```

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
* BOM seen: BOM是否已处理，默认为false
* error mode: 错误模式，默认为replacement

### 特征属性

* encoding: 小写的Encoding name
* fatal: error mode是否为fatal，默认为false
* ignoreBOM: ignore BOM值，默认为false

## TextDecoder接口

```javascript
interface TextDecoder includes TextDecoderCommon {
  constructor(optional DOMString label = "utf-8", optional TextDecoderOptions options = {})

  USVString decode(optional BufferSource input, optional TextDecodeOptions options = {})
}

directionary TextDecoderOptions {
  boolean fatal = false;
  boolean ignoreBOM = false;
}

direction TextDecodeOptions {
  boolean stream = false;
}
```

### 内部属性

* do not flush: 是否保留最后不完整的字节

### new TextDecoder(label, options)

```
1. 创建TextDecoder对象
2. 初始化encoding, error mode, ignore BOM属性
```

### decode(input, options)

将字节流解码为字符串

```
1. do not flush为false时，设置decoder、stream为新创建的decoder、 stream
2. 设置do not flush属性为options.stream，将input流copy进stream
3. 使用decoder对stream进行解码
4. 将解码得到的字节流转换为字符串返回
```

## TextEncoderCommon接口

```javascript
interface mixin TextEncoderCommon {
  readonly attribute DOMString encoding;
}
```

### 特征属性

* encoding: 返回utf-8

## TextEncoder接口

```javascript
interface TextEncoder includes TextEncoderCommon {
  constructor();

  Unit8Array encode(optional USVString input = "");
  TextEncoderEncodeIntoResult encodeInto(USVString source, Uint8Array destination);
}

dictionary TextEncoderEncodeIntoResult {
  unsigned long long read;
  unsigned long long written;
}
```

### new TextEncoder()

创建TextEncodcoder对象

### encode(input)

将字符串转换为Uint8Array字节系列

```
1. 将input字符串转换为码点流，从流中读取token
2. 使用UTF-8 encoder将token转换为对应的字节
3. 转换成功时将字节push进output流，继续读取下一个token，直至End-of-stream
4. 将output流转换为Uint8Array返回
```

### encodeInto(source, destination)

读取source中的字符，编码后写入destination中

```
1. 将source字符串转换为码点流，从流中读取token
2. 使用UTF-8 encoder将token转换为对应的字节
3. 转换成功时将字节写入destination中，记录读取的字符码元数和写入字节数
4. 读取完或写入完后，返回读取的字符码元数和写入字节数
```

## GenericTransformStream接口

```javascript
interface mixin GenericTransformStream {
  readonly attribute ReadableStream readable;
  readonly attribute WritableStream writable;
}
```

### 内部属性

* transform: 转换流

### 特征属性

* readable: transform.[[readable]]
* writable: transform.[[writable]]

## TextDecoderStream接口

```javascript
interface TextDecoderStream includes TextDecoderCommon, GenericTransformStream {
  constructor(optional DOMString label = "utf-8", optional TextDecoderOptions options = {})
}
```

### new TextDecoderStream(label, options)

```
1. 创建TextDecoderStream对象
2. 初始化encoding, error mode, ignore BOM属性
3. 设置transform负责将字节chunk转换为字符串chunk
```

## TextEncoderStream接口

```javascript
interface TextEncoderStream includes TextEncoderCommon, GenericTransformStream {
  constructor()
}
```

### 内部属性

* encoder: UTF-8 encoder
* pending high surrogate: 正在处理的高位代理区，默认为null

### new TextEncoderStream()

```
1. 创建TextEncoderStream对象
2. 设置transform负责将字符串chunk转换为字节chunk
```

## UTF-8 decoder

UTF-8编码方式的解码器

### 内部属性

* UTF-8 code point: 码点，默认为0
* UTF-8 bytes needed: 需要几个`10xxxxxx`字节，默认为0
* UTF-8 bytes seen: 已处理几个`10xxxxxx`字节，默认为0
* UTF-8 lower boundary: `10xxxxxx`字节的下边界，默认为0x80
* UTF-8 upper boundary: `10xxxxxx`字节的上边界，默认为0xBF

### handler(stream, byte)

解码处理

```
1. UTF-8 bytes needed为0时
  a. byte为`0xxxxxxx`, 返回byte对应的code point
  b. byte为`110xxxxx`, UTF-8 bytes needed为1，UTF-8 code point为`xxxxx`代表的值，继续处理下一个byte
  c. byte为`1110xxxx`, UTF-8 bytes needed为2，UTF-8 code point为`xxxx`代表的值，继续处理下一个byte
  d. byte为`11110xxx`, UTF-8 bytes needed为3，UTF-8 code point为`xxx`代表的值，继续处理下一个byte
2. UTF-8 bytes needed不为0时，byte为`10xxxxxx`字节，需要在[UTF-8 lower boundary, UTF-8 upper boundary]之间
  a. UTF-8 code point为原UTF-8 code point值左移6位加上byte`xxxxxx`代表的值
  b. UTF-8 bytes seen加1
  c. UTF-8 bytes seen不等于UTF-8 bytes needed时，继续处理下一个byte
  d. UTF-8 bytes seen等于UTF-8 bytes needed时，返回UTF-8 code point，内部属性设置为初始值
```

## UTF-8 encoder

UTF-8编码方式的编码器

### handler(stream, code point)

编码处理

```
1. code point在[U+0000, U+007F]之间，返回code point对应的byte
2. code point在[U+0080, U+07FF]之间，count为1，offset为`11000000`
3. code point在[U+0800, U+FFFF]之间，count为2，offset为`11100000`
4. code point在[U+10000, U+10FFFF]之间，count为3，offset为`11110000`
5. 返回字节系列
  a. 第一个字节为code point右移6 * count位加上offset
  b. 后count个字节为code point右移6 * (count - k)位后取后六位加上`10000000`，k为后面第几个字节（不算第一个）
```

## single-byte decoder

单字节编码方式的解码器，如ASCII、ISO 8859等

### handler(stream, byte)

```
1. byte为ASCII byte，返回byte对应的code point
2. byte不为ASCII byte，返回单字节索引表中byte - 0x80编号对应的code point
```

## single-byte encoder

单字节编码方式的编码器

### handler(stream, code point)

```
1. code point为ASCII code point，返回code point对应的byte
2. code point不为SCII code point，pointer为单字节索引表中code point对应的编号，返回pointer + 0x80
```

## GBK decoder

简体中文GBK编码方式的解码器

### 内部属性

* GBK lead: 双字节编码的第一个字节，默认为0x00

### handler(stream, byte)

```
1. GBK lead为0x00时
  a. byte为ASCII byte，返回byte对应的code point
  b. byte为0x80，返回U+20AC
  c. byte在[0x81, 0xFE]之间，GBK lead为byte，继续处理下一个字节
2. GBK lead不为0x00时
  a. byte在[0x40, 0x7E]之间，offset为0x40
  b. byte在[0x80, 0xFE]之间，offset为0x41
  c. pointer为(GBK lead - 0x81) * 190 + byte - offset
  d. 返回GBK索引表中pointer对应的code point，内部属性置为初始值
```

## GBK encoder

简体中文GBK编码方式的编码器

### handler(stream, code point)

```
1. code point为ASCII code point，返回code point对应的byte
2. code point为U+20AC，返回0x80
3. pointer为GBK索引表中code point对应的编号，返回双字节
  a. 第一个字节为pointer / 190 + 0x81
  b. trail为pointer % 190
  c. trail小于0x3F时，offset为0x40，否则为0x41
  d. 第二个字节为trail + offset
```

## Big5 decoder

繁体中文Big5编码方式的解码器

### 内部属性

* Big5 lead: 双字节编码的第一个字节，默认为0x00

### handler(stream, byte)

```
1. Big5 lead为0x00时
  a. byte为ASCII byte，返回byte对应的code point
  b. byte在[0x81, 0xFE]之间，Big5 lead为byte，继续处理下一个字节
2. Big5 lead不为0x00时
  a. byte在[0x40, 0x7E]之间，offset为0x40
  b. byte在[0xA1, 0xFE]之间，offset为0x62
  c. pointer为(Big5 lead - 0x81) * 157 + byte - offset
  d. 返回GBK索引表中pointer对应的code point，内部属性置为初始值
```

## Big5 encoder

繁体中文Big5编码方式的编码器

### handler(stream, code point)

```
1. code point为ASCII code point，返回code point对应的byte
2. pointer为Big5索引表中code point对应的编号，返回双字节
  a. 第一个字节为pointer / 157 + 0x81
  b. trail为pointer % 157
  c. trail小于0x3F时，offset为0x40，否则为0x62
  d. 第二个字节为trail + offset
```

## Shift_JIS decoder

日文Shift_JIS编码方式的解码器

### 内部属性

* Shift_JIS lead: 双字节编码的第一个字节，默认为0x00

### handler(stream, byte)

```
1. Shift_JIS lead为0x00时
  a. byte为ASCII byte或0x80，返回byte对应的code point
  b. byte在[0xA1, 0xDF]之间，返回0xFF61 + byte - 0xA1
  c. byte在[0x81, 0x9F]或[0xE0, 0xFC]之间，Shift_JIS lead为byte，继续处理下一个字节
2. Shift_JIS lead不为0x00时
  a. byte在[0x40, 0x7E]之间，offset为0x40
  b. byte在[0x80, 0xFC]之间，offset为0x41
  c. Shift_JIS lead在[0x81, 0x9F]之间，lead offset为0x81
  d. Shift_JIS lead在[0xE0, 0xFC]之间，lead offset为0xC1
  e. pointer为(Shift_JIS lead - lead offset) * 188 + byte - offset
  f. 返回Shift_JIS索引表中pointer对应的code point，内部属性置为初始值
```

## Shift_JIS encoder

日文Shift_JIS编码方式的编码器

### handler(stream, code point)

```
1. code point为ASCII code point或0x80，返回code point对应的byte
2. code point在[U+FF61, U+FF9F]之间，返回code point - 0xFF61 + 0xA1
3. pointer为Shift_JIS索引表中code point对应的编号，返回双字节
  a. lead为pointer / 188
  b. lead小于0x1F时，lead offset为0x81，否则为0xC1
  c. 第一个字节为lead + lead offset
  b. trail为pointer % 188
  c. trail小于0x3F时，offset为0x40，否则为0x41
  d. 第二个字节为trail + offset
```

## EUC-KR decoder

韩文EUC-KR编码方式的解码器

### 内部属性

* EUC-KR lead: 双字节编码的第一个字节，默认为0x00

### handler(stream, byte)

```
1. EUC-KR lead为0x00时
  a. byte为ASCII byte，返回byte对应的code point
  b. byte在[0x81, 0xFE]之间，EUC-KR lead为byte，继续处理下一个字节
2. EUC-KR lead不为0x00时
  a. byte在[0x41, 0xFE]之间，offset为0x41
  b. pointer为(EUC-KR lead - 0x81) * 190 + byte - offset
  d. 返回EUC-KR索引表中pointer对应的code point，内部属性置为初始值
```

## EUC-KR encoder

韩文EUC-KR编码方式的编码器

### handler(stream, code point)

```
1. code point为ASCII code point，返回code point对应的byte
2. pointer为EUC-KR索引表中code point对应的编号，返回双字节
  a. 第一个字节为pointer / 190 + 0x81
  d. 第二个字节为pointer % 190 + 0x41
```

## UTF-16 decoder

UTF-16编码方式的解码器

### 内部属性

* UTF-16 lead byte: 双字节编码的第一个字节，默认为null
* UTF-16 lead surrogate: 高位代理区，默认为null
* is UTF-16BE decoder: 是否为大端序，默认为false

### handler(stream, byte)

```
1. UTF-16 lead byte为null时，设置UTF-16 lead byte为byte，继续处理下一个byte
2. UTF-16 lead byte不为null时
  a. is UTF-16BE decoder为true，code unit为UTF-16 lead byte左移8位加上byte
  b. is UTF-16BE decoder为false，code unit为byte左移8位加上UTF-16 lead byte
3. UTF-16 lead surrogate为null时
  a. code unit在[0xD800, 0xDBFF]之间，设置UTF-16 lead surrogate为code unit，继续处理下一个code unit
  b. code unit不在[0xD800, 0xDBFF]之间，返回code unit
4. UTF-16 lead surrogate为不null时
  a. code unit在[0xDC00, 0xDFFF]之间，返回((UTF-16 lead surrogate - 0xD800) * 0x400) + (code unit - 0xDC00) + 0x10000
```
