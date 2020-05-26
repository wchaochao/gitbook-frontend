# Web构建技术

标签（空格分隔）： 前端知识体系

---

## HTML

HyperText Markup Language, 超文本标记语言，网页的描述语言

* CSS：Cascading Style Sheets, 层叠样式表，网页的样式单
* JavaScript：通过调用浏览器提供的API实现网页交互
* DOM：Document Object Model，文档对象模型，操作HTML文档的API
* Web API：浏览器操作API，如网络请求

## HTTP协议

HyperText Transfer Protocol，超文本传输协议，Web的核心通信协议

* URI: Uniform Resource Identifier，统一资源标识符，用于标志网络资源
* 请求：客户端发送请求
* 响应：服务器返回响应

## TCP/IP协议蔟

互联网相关的各类协议的总称

* 应用层：提供应用服务，如HTTP、DNS、FTP
* 传输层：建立端口到端口的通信，实现程序之间的交流，如TCP、UDP
* 网络层：建立主机到主机的通信，如IP、APR
* 数据链路层：提供数据包，如以太网协议

### TCP/IP通信流

发送端从应用层往下走，接收端从数据链路层往上走

* 发送端在层与层传输数据时，每经过一层时加上该层的首部消息
* 接收端在层与层传输数据时，每经过一层时删去该层的首部消息

### IP协议

通过IP地址和MAC地址传递数据包

* 在同一子网内，广播给目标机器
* 不在同一子网内，广播给路由器，由路由器转发
 * 使用APR协议根据路由器的IP地址反查路由器MAC地址
 * 通过IP地址和MAC地址将数据包传递给路由器
 * 路由器进行路由选择，经过层层路由器后，到达目标机器

### TCP协议

通过ACK应答机制确保数据完整

* 三次握手：同步初始序号
* 数据传输：客户端、服务器双向传输
* 四次挥手：关闭连接

### DNS协议

域名解析

* 通过域名查找IP地址
* 通过IP地址反查域名

## Web应用

* 静态站点：纯静态页面，所有用户提供相同的页面
* 应用程序：动态页面，根据用户提供相应的页面
 * 后端动态生成页面
 * 前端通过请求局部刷新页面

## 数据交换格式

* JSON: Javascript Object Notation, Javascript对象表示法
* XML: Extensible Markup Language, 可扩展标记语言
