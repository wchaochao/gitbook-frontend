# HTTP首部

标签（空格分隔）： 前端知识体系

---

提供额外的重要信息

## 结构

由首部字段名和字段值构成，中间用冒号分隔

```
首部字段名: 字段值
```

## 分类

* 端到端首部：会转发到最终接收目标
* 逐跳首部：只对单次转发有效

## 通用首部字段

请求报文和响应报文都会使用的首部

### Connection

* 控制不再转发的首部字段
* 管理持久连接：对于同一个源，大多数浏览器允许同时建立6个持久连接
 * keep-alive: 持久连接
 * close: 关闭连接

```
// 控制不再转发的首部字段
GET / HTTP/1.1
Upgrade: HTTP/1.1
Connection: Upgrade

// 管理持久连接
Connection: keep-alive
```

### Date

创建报文的时间

```
Date: Tue, 03 Jul 2012 04:40:59 GMT
```

### Cache-Control

缓存控制

* 每个指令具有可选参数
* 多个指令使用逗号分隔

```
Cache-Control: public, no-cache, max-age=86400
```

#### 缓存指令

* public: 公共缓存，可向任意方提供缓存
* private: 私有缓存，只能向特定用户提供缓存
* no-cache: 不缓存过期资源
 * 客户端：向源服务器请求，不接收缓存
 * 服务器端：使用缓存前必须向源服务器确认其有效性
* no-store: 不缓存
* max-age: 缓存的有效时间（单位：秒）
 * 客户端：缓存时间比指定时间小时接收缓存
 * 服务器端：在缓存的有效时间内直接使用缓存，，默认为(Date - LastModified) / 10
* s-maxage: 公共缓存的有效时间（单位：秒）
* min-fresh: 指定时间内缓存仍有效时接收缓存（单位：秒）
* max-stale: 在过期时间内仍接收缓存
* only-if-cached: 仅获得缓存的响应，无缓存时返回504
* must-revalidate: 缓存过期时向源服务器确认其有效性
* proxy-revalidate: 公共缓存过期时向源服务器确认其有效性
* no-transform: 缓存不能对实体的进行转换

### Pragma

HTTP/1.0的缓存控制，只用在客户端请求中

```
Cache-Control: no-cache
Pragma: no-cache // 在HTTP/1.0中相当于Cache-Control: no-cache
```

### Transfer-Encoding

实体的传输编码方式，HTTP/1.1仅支持分块传输编码

```
HTTP/1.1 200 OK
Content-Type: text/html
Transfer-Encoding: chunked

cf0 // 十六进制

...3312字节分块数据...

392

...914字节分块数据...

0
```

### Trailer

报文主体后附加的首部字段，用于分块传输编码中

```
HTTP/1.1 200 OK
Content-Type: text/html
Transfer-Encoding: chunked
Trailer: Expires

...（报文主体）...
0
Expires: Tue, 28 Sep 2004 23:59:59 GMT
```

### Via

客户端和服务器之间的经过的代理

* 报文经过代理或网关时，在Via字段中附加该服务器的信息

```
GET / HTTP/1.1
Via: 1.0 gw.hackr.jp(Squid/3.1), 1.1 a1.example.com(Squid/2.7)
```

### Upgrade

是否可使用更高的版本进行通信

```
GET /index.html HTTP/1.1
Upgrade: TLS/1.0
Connection: Upgrade

HTTP/1.1 101 Switching Protocols
Upgrade: TLS/1.0, HTTP/1.1
Connection: Upgrade
```

### Warning

告知用户一些与缓存相关的问题的警告

```
Warning: [警告码] [警告的主机:端口号] "[警告内容]" ([日期时间])
```

## 请求首部字段

请求报文使用的首部

### Host

资源所在的host

* 必须存在，用于处理同一个IP下部署多个域名的情况

```
Host: www.hackr.jp
```

### User-Agent

用户代理信息

```
User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4044.122 Safari/537.36
```

### Referer

请求是由哪个Web页面发起的，不包含查询参数

```
Referer: http://www.hackr.jp/index.html
```

### From

用户的电子邮件地址

```
From: info@hackr.jp
```

### Accept

客户端可接受的媒体类型及优先级

* 用q来表示权重，分号分隔，默认q=1.0

```
Accept: text/html, application/xhtml+xml, application/xml;q=0.9, */*;q=0.8
```

### Accept-Charset

客户端可接受的字符集及优先级

```
Accept-Charset: iso-8859-5, unicode-1-1;q=0.8
```

### Accept-Encoding

客户端可接受的内容编码及优先级

```
Accept-Encoding: gzip, compress;q=0.9
```

### Accept-Language

客户端可接受的自然语言集及优先级

```
Accept-Encoding: zh-cn, zh;q=0.7, en-us, en;q=0.3
```

### Range

请求范围

```
Range: bytes=5001-10000
```

### TE

客户端可接受的传输编码方式及优先级

```
TE: chunked
```

### If-Match

条件请求，条件为真时，执行请求

* ETag匹配时，执行请求
* ETag不匹配时，返回412 Precondition Failed

```
If-Match: "123456"
```

### If-None-Match

通过ETag确认缓存的有效性

* ETag不匹配时，资源已更新，执行请求
* ETag匹配时，资源未更新，返回304 Not Modified

```
If-None-Match: "123456"
```

### If-Modified-Since

通过Last-Modified确认缓存的有效性

* Last-Modified之后资源已更新，执行请求
* Last-Modified之后资源未更新，返回304 Not Modified

```
If-Modified-Since: Thu, 15 Apr 2004 00:00:00 GMT
```

### If-Unmodified-Since

以Last-Modified为条件

* Last-Modified之后资源未更新，执行请求
* Last-Modified之后资源已更新，返回412 Precondition Failed

```
If-Unmodified-Since: Thu, 15 Apr 2004 00:00:00 GMT
```

### If-Range

ETag或Last-Modified一致时，作为范围请求处理

* 资源未更新时，返回部分内容
* 资源已更新时，返回全部资源

```
GET /index.html HTTP/1.1
If-Range: "123456"
Range: bytes=5001-10000
```

### Authorization

源服务器要求的客户端认证信息

```
Authorization: Basic dWVub3N1bjpwYXNzd29yZA==
```

### Proxy-Authorization

代理服务器要求的客户端认证信息

```
Proxy-Authorization: Basic dWVub3N1bjpwYXNzd29yZA==
```

### Max-Forwards

最大转发数，用于调查传输路径

* 每转发一次，数值减一
* 数值为0时，返回响应

```
Max-Forwards: 10
```

### Expect

期望的服务器行为，HTTP/1.1仅定义了100-continue

```
Expect: 100-continue
```

## 响应首部字段

响应报文使用的首部

### Server

HTTP服务器应用程序信息

```
Server: Apache/2.2.6 (Unix) PHP/5.2.5
```

### Age

响应创建后经过的时间（单位：秒）

* 源服务器多久前创建了响应
* 缓存代理多久前确认了响应

```
Age: 600
```

### Accept-Ranges

是否接受范围请求

* bytes: 接受
* none: 不接受

```
// 接受范围请求
Accept-Ranges: bytes

// 不接受范围请求
Accept-Ranges: none
```

### ETag

实体的唯一性标识，资源更新时，ETag随之更新

* 强ETag: 无论实体发生多么细微的变化都会改变其值
* 弱ETag: 实体发送根本变化，产生差异时改变其值

```
// 强ETag
ETag: "usagi-1234"

// 弱ETag
ETag: W/"5ec30f8d-19bb9"
```

### Vary

按指定字段对代理缓存进行区分

```
Vary: Accept-Language
```

### Location

重定向地址，浏览器会强制性地尝试对Location的资源进行访问，配合3xx使用

```
// 请求
GET /sample.html HTTP/1.1

// 响应
HTTP/1.1 302 Found
Location: http://www.usagidesign.jp/sample.html
```

### Retry-After

多久之后再次发送请求，配合503或3xx使用

```
// 秒数
Retry-After: 120

// 具体的时间
Retry-After: Web, 04 Jul 2012 06:34:24 GMT
```

### WWW-Authenticate

源服务器要求客户端提供认证信息，配合401使用

```
WWW-Authenticate: Basic realm="Usagidesign Auth"
```

### Proxy-Authenticate

代理服务器要求客户端提供认证信息，配合401使用

```
Proxy-Authenticate: Basic realm="Usagidesign Auth"
```

## 实体首部字段

针对实体使用的首部

### Content-Type

实体类型和字符集

```
Content-Type: text/html; charset-UTF-8
```

### Content-Encoding

实体的内容编码方式

```
Content-Encoding: gzip
```

### Content-Language

实体的自然语言集

```
Content-Language: zh-CN
```

### Content-Range

返回的实体范围

```
HTTP/1.1 206 partial Content
Content-Type: image/jpeg
Content-Range: bytes 5001-10000/10000
Content-Length: 5000
```

### Content-Length

实体的大小（单位：字节）

* 进行内容编码时，不使用Content-Length

```
Content-Length: 15000
```

### Content-Location

实体的URI

```
// 请求
GET / HTTP/1.1
host: http://www.hackr.jp
Accept-Language: ja

// 响应
HTTP/1.1 200 OK
Content-Location: http://www.hackr.jp/index-ja.html
```

### Content-MD5

实体经过MD5算法后用base64表示的值，用于检测实体是否完整

* 客户端会对实体再执行一次相同的MD5算法，再用base64编码，将得到的值与Content-MD5比较

```
Content-MD5: OGFKZDUwNGVhNGY3N2MxMDIwZmQ4NTBmY2IyTY==
```

### Expires

缓存过期时间

```
Expires: Web, 04 Jul 2012 08:26:05 GMT
```

### Last-Modified

实体的最后修改时间

```
Last-Modified: Web, 23 May 2012 09:59:55 GMT
```

### Allow

实体允许的请求方法

* 当服务器接收到不支持的请求方法时，返回405

```
Allow: GET, HEAD
```

## Cookie首部字段

### Set-Cookie

服务器往客户端写入Cookie信息

* NAME=VALUE: Cookie的名称和值
* expires=DATE: Cookie的有效期，默认为当前会话
* domain=DOMAIN: Cookie所属的域名，默认为服务器域名
* path=PATH: Cookie所属的路径，默认为文档所在的文件目录
* Secure: 仅在HTTPS协议下发送Cookie
* HttpOnly: 只能使用在请求中，JavaScript不能通过document.cookie访问该Cookie

```
Set-Cookie: status=enable; expires=Tue, 05 Jul 2011 07:26:31 GMT; path=/; domain=.hackr.jp; secure; HttpOnly
```

### Cookie

客户端发送Cookie信息给服务器

```
Cookie: status=enable; name=abc;
```

## 其他首部字段

扩展的非标准首部字段

### X-Frame-Options

响应首部，是否可以被iframe嵌入，用于防止点击劫持

* DENY: 拒绝
* SAMEORIGIN: 同源可嵌入

```
X-Frame-Options: DENY
```

### X-XSS-Protection

响应首部，XSS防护机制的开关

* 0: 关
* 1: 开

```
X-XSS-Protection: 1
```

### DNT

请求首部，Do Not Track, 拒绝追踪

* 0: 同意被追踪
* 1: 拒绝被追踪

```
DNT: 1
```
