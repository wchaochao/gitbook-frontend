# HTTP响应

标签（空格分隔）： 前端知识体系

---

## 结构

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

## 状态码

描述返回的请求结果，由三位数字组成，第一位说明响应类别

| 状态码 | 类别 | 原因短语 |
| --- | --- | --- |
| 1xx | Informational（信息性状态码） | 接收的请求正在处理 |
| 2xx | Success（成功状态码） | 请求正常处理完毕 |
| 3xx | Redirection（重定向状态码） | 需要进行附加操作以完成请求 |
| 4xx | Client Error（客户端错误状态码） | 服务器无法处理请求 |
| 5xx | Server Error（服务器端错误状态码） | 服务器处理请求出错 |

### 1xx

请求正在处理

| 状态码 | 状态短语 | 描述 |
| --- | --- | --- |
| 100 | Continue | 已收到请求头，客户端应继续发送请求体 |
| 101 | Switching Protocols | 转换协议 |

### 2xx

请求被正常处理

| 状态码 | 状态短语 | 描述 |
| --- | --- | --- |
| 200 | OK | 请求成功 |
| 204 | No Content | 无内容返回 |
| 206 | Partial Content | 返回部分内容 |

### 3xx

浏览器需要执行某些特殊的处理以正确处理请求

| 状态码 | 状态短语 | 描述 |
| --- | --- | --- |
| 301 | Moved Permanently | 永久重定向 |
| 302 | Found | 临时重定向 |
| 303 | See Other | 资源存在另一个URI，应使用GET方法获取 |
| 304 | Not Modified | 资源未更新 |
| 307 | Temporary Redirect | 临时重定向 |
| 308 | Permanent Redirect | 永久重定向 |

**注意**：301、302的重定向方法会被客户端改为GET方法，307、308不会

### 4xx

客户端是发送错误的原因所在

| 状态码 | 状态短语 | 描述 |
| --- | --- | --- |
| 400 | Bad Request | 坏请求 |
| 401 | Unauthorized | 未认证 |
| 403 | Forbidden | 禁止访问 |
| 404 | Not Found | 资源不存在 |
| 405 | Method Not Allowed | 请求方法不支持 |
| 407 | Proxy Authentication Required | 需要代理的认证信息 |
| 408 | Request Timeout | 请求超时 |

### 5xx

服务器本身发生错误

| 状态码 | 状态短语 | 描述 |
| --- | --- | --- |
| 500 | Internal Server Error | 服务器出现错误 |
| 502 | Bad Gatway | 网关错误 |
| 503 | Service Unavailable | 服务不可用 |
| 504 | Gatway Timeout | 网关超时 |
