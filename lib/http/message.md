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

在不丢失实体信息的前提下对实体进行压缩编码，由接收的客户端负责解码

* gzip：文件压缩程序GNU zip生成的编码格式
* compress：UNIX文件压缩程序compress生成的编码格式
* deflate：由deflate压缩算法生成的编码格式
* identity：不进行编码

```
// 请求
GET /style.css HTTP/1.1
Accept-Encoding: gzip, deflate

// 响应
HTTP/1.1 200 OK
Content-Encoding: gzip
```

### 分块传输编码

将实体分块传输，由接收的客户端负责解码

* 每一块用十六进制标记块的大小
* 最后一块大小为0，用`0(CR+LF)`标记

```
// 请求
GET /big.html HTTP/1.1
TE: chunked

// 响应
HTTP/1.1 200 OK
Content-Type: text/html
Transfer-Encoding: chunked
Trailer: Expires

cf0 // 十六进制

...3312字节分块数据...

392

...914字节分块数据...

0
Expires: Tue, 28 Sep 2004 23:59:59 GMT
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
Range: bytes=5001-10000
If-Range: "123456"

// 响应
HTTP/1.1 206 Partial Content
Accept-Ranges: bytes
Content-Type: image/jpeg
Content-Length: 5000
Content-Range: bytes 5001-10000/10000

...（指定范围的数据）...
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

```
GET /index.json HTTP/1.1
Accept: application/json, text/javascript, */*; q=0.01
Accept-Charset: utf-8
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,en-US;q=0.7
```
