# MIME Sniffing

标签（空格分隔）： 前端知识体系

---

## MIME type

媒体类型

### 组成

* type：主类型
* subtype：子类型
* parameters：参数map

MIME type 字符串

```
// essence
text/html

// essence + parameters
text/html; charset=utf-8
```

MEME type record

```javascript
{
  type: text,
  subtype: html,
  parameters: {
    charset: 'utf-8'
  }
}
```

### parse(input)

将MIME type字符串解析为MIME type record

```
1. 去除input前后空白符
2. 从input开头开始收集不为/的字符，设为type，检测是否由HTTP token code points组成
3. 从/后一个字符开始收集不为;的字符，设为subtype，移除尾空白符，检测是否由HTTP token code points组成
4. 创建MIME type record，type为小写的type，subtype为小写的subtype
5. 解析参数
  a. 跳过;和空白字符
  b. 收集不为;和=的字符，设为parameterName，转换为小写
    i. 当前字符为;时，继续解析下一个参数
    ii. 当前字符为=时，跳过=
  c. 当前字符为"时，收集引号内的字符，设为parameterValue，继续消费字符至;
  d. 当前字符不为"时，收集不为;的字符，设为parameterValue，移除尾空白符
  e. paramaterName由HTTP token code points组成、parameterValue由HTTP quoted-string token code points组成且paramaterName不在parameters中时，设置parameters[parameterName]为parameterValue
  f. 继续解析下一个参数
6. 返回MIME type record
```

### serialize(mimeType)

将MIME type record解析为MIME type字符串

```
1. 设置serialization为mimeType.type + '/' + mimeType.subtype
2. 遍历mimeType.parameters，对每个参数项(name, value)，往serialization后依次追加;、name、=、value，value不为HTTP token code points时，使用引号括起来，对value里的引号和\进行\转义
3. 返回serialization
```

### group

| group | 描述 | 类型 |
| --- | --- | --- |
| HTML MIME type | HTML类型 | text/html |
| JavaScript MIME type | JavaScript类型 | application/javascript<br/>text/javascript |
| XML MIME type | XML类型 | subtype以+xml结尾<br/>text/xml<br/>application/xml |
| JSON MIME type | JSON类型 | subtype以+json结尾<br/>text/json<br/>application/json |
| image MIME type | 图片类型 | type为image |
| audio or video MIME type | 音视频类型 | type为audio或video<br/>application/ogg |
| font MIME type | 字体类型 | type为font<br/>application/font-ttf<br/>application/font-woff |
| archive MIME type | 压缩类型 | application/x-rar-compressed<br/>application/zip<br/>application/x-gzip |

## Resource

资源

### 元数据

* supplied MIME type：提供的媒体类型
* check-for-apache-bug flag：是否要检查apache bug
* no-sniff flag：是否不对资源内容进行媒体类型嗅探
* computed MIME type：对资源内容嗅探出的媒体类型

### supplied MIME type detection

提供的媒体类型检测

```
1. resource由HTTP返回时
  a. supplied type为最后一个Content-Type响应头
  b. supplied type为text/plain、text/plain; charset=ISO-8859-1、text/plain charset=iso-8859-1、text/plain; charset=UTF-8时需要检查apache bug
2. resource由文件系统返回时，supplied type由文件系统提供
3. resource由其他协议返回时，supplied type由协议决定
```

## MIME type match

### byte sequence match

字节系列匹配

```
1. 跳过byte sequence开头需要忽略的字节
2. 遍历byte sequence，每个字节和pattern mask对应的字节做且运算，再与byte pattern中对应的字节匹配
```

### image type match

| Match | Image MIME Type |
| --- | --- |
| 前四个字节为00 00 01 00，windows icon signature | image/x-icon |
