# HTTP报文

标签（空格分隔）： 前端知识体系

---

HTTP协议交互的信息，由多行数据构成的字符串文本，包括请求报文和响应报文

## 结构

* 报文首部：服务器端或客户端需处理的请求或响应的内容及属性
 * 请求行：请求方法、请求URI、HTTP版本
 * 响应行：HTTP版本、状态码、状态短语
 * 首部字段：通用首部、请求首部、响应首部、实体首部
* 空行：CR + LF
* 报文主体：应被发送的数据

## 编码

通过编码提升传输速率

### 内容编码

对实体进行压缩编码，由接收的客户端负责解码

* gzip：GNU zip
* compress：UNIX系统的标准压缩
* deflate：zlib
* identity：不进行编码

### 分块传输编码

将实体分块传输，由接收的客户端负责解码

* 每一块用十六进制标记块的大小
* 最后一块大小为0，用`0(CR+LF)`标记

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

## 范围请求

使用Range字段指定请求实体的范围

```
// 5001~10000字节
Range: bytes=5001-10000

// 5001之后全部
Range: bytes=5001-

// 0-3000、5000~7000字节的多重范围
Range: bytes=-3000, 5000-7000
```

示例

```
// 请求
GET /tip.jpg HTTP/1.1
Host: www.usagidesign.jp
Range: bytes=5001-10000

// 响应
HTTP/1.1 206 Partial Content
Content-Type: image/jpeg
Content-Length: 5000
Content-Range: bytes 5001-10000/10000
```

## 多部分对象集合

报文主体中含有多类型实体

* 使用Content-Type说明使用多部分对象集合
* 使用boundary字符串划分多部分对象集合的各类实体

### multipart/form-data

表单提交

```
Content-Type: multipart/form-data; boundary=aBoundaryString

--aBoundaryString
Content-Disposition: form-data; name="myField"

Joe Blow
--aBoundaryString
Content-Disposition: form-data; name="myFile"; filename="img.jpg"
Content-Type: image/jpeg

...img.jpg的数据...
--aBoundaryString--
```

### multipart/byteranges

206（部分内容）响应报文中含有多个范围的内容

```
HTTP/1.1 206 Partial Content
Accept-Ranges: bytes
Content-Type: multipart/byteranges; boundary=3d6b6a416f9b5

--3d6b6a416f9b5
Content-Type: text/html
Content-Range: bytes 100-200/1270

...（指定范围的数据）...
--3d6b6a416f9b5
Content-Type: text/html
Content-Range: bytes 300-400/1270

...（指定范围的数据）...
--3d6b6a416f9b5--
```

## 内容协商

客户端和服务器端就响应的资源内容进行协商，返回给客户端最为适合的资源

* Accept
* Accept-Charset
* Accept-Encoding
* Accept-Language
* Content-Language
