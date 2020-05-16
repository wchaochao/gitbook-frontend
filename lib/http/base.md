# HTTP基础

标签（空格分隔）： 前端知识体系

---

## 消息模型

客户端发出请求，服务器返回响应

### HTTP请求

* 请求行：由请求方法、请求URI、HTTP版本组成
* 请求头：每行一个头信息
* 空行
* 请求体：向服务器发送的内容

```
POST /form/entry HTTP/1.1
Host: hackr.jp
Connection: keep-alive
Content-Type: application/x-www-form-urlencoded
Content-Length: 16

name=ueno&age=37
```

### HTTP响应

* 响应行：由HTTP版本、状态码，状态短语组成
* 响应头：每行一个头信息
* 空行
* 响应体：服务器返回的内容

```
HTTP/1.1 200 OK
Date: Tue, 10 Jul 2012 06:50:15 GMT
Content-Length: 362

<html>
...
```

## 资源定位

HTTP使用URI定位资源

```
// 完整URI
GET http://hackr.jp/index.html HTTP/1.1

// 绝对URI
GET /index.html HTTP/1.1
Host: hackr.jp

// 访问服务器本身
OPTIONS * HTTP/1.1
```

## 资源方法

### GET

获取资源

```
// 请求
GET /index.html HTTP/1.1
Host: hackr.jp
// 响应
返回index.html的页面资源

// 请求
GET /index.html HTTP/1.1
Host: hackr.jp
If-Modified-Since: Wed, 13 May 2020 05:35:51 GMT
// 响应
返回自指定时间之后更新过的index.html资源，未更新时返回304
```

### POST

发送数据

```
// 请求
POST /api/content/add HTTP/1.1
Host: hackr.jp
Content-Type: application/json;charset=UTF-8
Content-Length: 51

{"title":"bbb","tag":"aaa","markdown":"","html":""}
// 响应
返回程序处理后的结果
```

### PUT

更新资源

```
// 请求
PUT /api/content/456 HTTP/1.1
Host: hackr.jp
Content-Type: application/json;charset=UTF-8
Content-Length: 51

{"title":"bbb","tag":"aaa","markdown":"","html":""}
// 响应
返回程序处理后的结果
```

### DELETE

删除资源

```
// 请求
DELETE /api/content/456 HTTP/1.1
Host: hackr.jp
// 响应
返回程序处理后的结果
```

### HEAD

获取资源的相关信息，同GET方法，只是不返回响应内容

```
// 请求
HEAD /index.html HTTP/1.1
Host: hackr.jp
// 响应
返回index.html有关的响应首部
```

### OPTIONS

询问资源支持的方法

```
// 请求
OPTIONS * HTTP/1.1
Host: hackr.jp
// 响应
HTTP/1.1 200 OK
Allow: GET, POST, HEAD, OPTIONS
```

### TRACE

追踪路径，用于诊断请求是否被篡改了

```
// 请求
TRACE / HTTP/1.1
Host: hackr.jp
Max-Forwards: 2
// 响应
HTTP/1.1 200 OK
Content-type: message/http
Content-Length: 1024

TRACE / HTTP/1.1
Host: hackr.jp
Max-Forwards: 2
```

### CONNECT

用隧道协议连接代理，用于使用隧道传输加密信息

```
// 请求
CONNECT proxy.hackr.jp HTTP/1.1
Host: proxy.hackr.jp
// 响应
HTTP/1.1 200 OK （之后进入网络隧道）
```

## 持久连接

只要任意一端没有明确提出断开连接，则保持TCP连接状态

* 建立一次TCP连接后进行多次请求响应的交互，减少重复连接的开销

### 管线化

不用等待响应返回亦可直接发送下一个请求

* 并行发送多个请求

## 状态管理

HTTP是无状态协议，不保存请求和响应之间的通信状态

* 通过Cookie、Token实现保存状态的功能

### Cookie

响应设置cookie，请求自动带上cookie

```
// 请求
GET /render/ HTTP/1.1
Host: hackr.jp
// 响应
HTTP/1.1 200 OK
Set-Cookie: sid=100236

// 下一个请求自动带上cookie
GET /image/ HTTP/1.1
Host: hackr.jp
Cookie: sid=100236
```
