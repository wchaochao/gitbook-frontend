# HTTP增强

标签（空格分隔）： 前端知识体系

---

## HTTP瓶颈

* 一条连接上多个请求需要排队
* 首部未经压缩就发送
* 每次互相发送相同的首部
* 数据非强制压缩发送
* 请求只能从客户端开始

## Ajax

Asynchronous JavaScript and XML, 异步JavaScript与XML

* 局部更新页面，减少响应的数据量
* 可能会导致大量请求产生

## Comet

通过延迟应答，模拟实现服务器端向客户端推送的功能

* 服务器接收到请求时，暂时挂起，等内容有更新时，再返回响应
* 维持连接会消耗跟多的资源

## SPDY

介于SSL和HTTP之间，以会话层的形式加入，控制数据的流动，HTTP/2.0的继承

* 多路复用：单一的TCP连接可以处理无限个HTTP请求
* 分配优先级：可以给请求逐个分配优先级
* 压缩首部：压缩请求和响应的首部
* 服务器推送：服务器可以主动向客户端推送数据
* 服务器提示：服务器可以提示客户端请求所需的资源

## WebSocket

Web浏览器与Web服务器之间的全双工通信标准

* 服务器推送：服务器可以主动向客户端推送数据
* 减少通信量：一直保持WebSocket连接，而且WebSocket的首部信息很小

### 握手

* 客户端发送升级协议为WebSocket的请求
* 服务器返回转换协议的响应
* 接下来使用WebSocket协议通信

```
GET /chat HTTP/1.1
Host: server.example.com
Upgrade: websocket
Connection: Upgrade
Origin: http://example.com
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
Sec-WebSocket-Protocol: chat, superchat
Sec-WebSocket-Version: 13

HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbKcxOo=
Sec-WebSocket-Protocol: chat
```

## HTTP2.0

改善用户使用Web时的速度体验

* 二进制协议：头信息和数据体都是二进制，称为头信息帧和数据帧
* 多工：客户端和浏览器可以同时发送多个请求或响应，且可以不按顺序发送
* 数据流：每个请求或响应的所有数据包，称为一个数据流，可以指定数据流的优先级
* 首部压缩：对头信息进行压缩，另外客户端和服务器会同时维护一张首部表，以后只发送索引号
* 服务器推送：服务器可以主动向客户端推送资源
