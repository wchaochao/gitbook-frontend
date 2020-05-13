# URI

标签（空格分隔）： 前端知识体系

---

Uniform Resource Identifier，统一资源标识符，用于唯一的标志资源

## 组成

由ASCII码可打印字符组成

* URI普通字符：字母、数字、标记（`. - _ ! ~ * ' ( )`）
* URI保留字符：`: / @ ? = & ; + $ ,`
* URI编码字符：`% HexDigit HexDigit`

## URL

Uniform Resource Location，统一资源定位符，通过位置标志资源

### 组成

![URL组成](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-network-base/url-structure.png)

* protocol: 协议
* username: 用户名，用于认证，可选
* password: 密码，用于认证，可选
* host: 主机，由主机名和端口号组成
* hostname: 主机名，使用IP或域名指定具体的服务器
* port: 端口号，对应服务器程序，省略时为协议默认的端口
* origin: 源，由协议和主机组成，用于同源判断
* pathname: 资源路径，由目录 + 文件名组成，文件名有时可省略
* search: 查询参数，可选
* hash: hash片段，可选

### protocol

| 协议 | 说明 | 默认端口 |
| --- | --- | --- |
| http | 超文本传输 | 80 |
| https | 安全的超文本传输 | 443 |
| ws | WebSocket连接 | 80 |
| wss | 安全的WebSocket连接 | 443 |
| ftp | 文件传输 | 21 |
| ssh | 安全Shell | 22 |
| telnet | 远程登录 | 23 |
| smtp | 发送邮件 | 25 |
| dns | DNS查询 | 53 |
| mailto | 电子邮件地址 | 无 |
| file | 本地文件 | 无 |
| data | 嵌入小文件 | 无 |
| view-source | 网页源代码 | 无 |
