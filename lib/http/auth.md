# HTTP认证

标签（空格分隔）： 前端知识体系

---

## 介绍

认证使用者身份，核对登录者本人才知道的信息

* 密码
* 动态令牌
* 数字证书

## Basic认证

基本认证

* 客户端发送请求
* 服务器返回401，WWW-Authenticate首部指定认证方式为Basic及安全域字符串realm
* 客户端要求用户输入用户名和密码，并将Base64(userId:password)写入首部字段Authorization中发送给服务器
* 服务器对凭证信息进行验证

缺点

* 明文发送，容易被窃听
* 很难进行认证注销操作

```
GET /basic/ HTTP/1.1

HTTP/1.1 401 Authorization Required
WWW-Authenticate: Basic realm="Input Your ID and Password"

GET /basic/ HTTP/1.1
Authorization: Basic Z3Vlc3Q6Z3Vlc3Q=

HTTP/1.1 200 OK
```

## Digest认证

摘要认证

* 客户端发送请求
* 服务器返回401，WWW-Authenticate首部指定认证方式为Digest及安全域字符串realm和临时质询码nonce
* 客户端要求用户输入用户名和密码，并向首部字段Authorization写入username、realm、nonce、uri、response发送给服务器
* 服务器对凭证信息进行验证

Digest参数

* username: 用户名
* password: 密码
* realm: 操作域
* method: 请求方法
* uri: 请求路径
* nonce: 质询码，随机字符串
* nc: nonce count, 请求次数，防止重放攻击
* cnonce: clientNonce, 客户端发送给服务器的随机字符串
* qop: 保护质量参数，auth或auth-int，会影响摘要算法
* response: 根据算法算出的摘要值，response=MD5(HA1:nonce:HA2), HA1=MD5(username:realm:password), HA2=MD5(method:uri)

```
GET /digest/ HTTP/1.1

HTTP/1.1 401 Authorization Required
WWW-Authenticate: Digest realm="DIGEST", nonce="MOSQZ0itBAA=44abb6784cc9cbfc605a5b0893d36f23de95fcff", algorithm=MD5, qop="auth"

GET /digest/ HTTP/1.1
AUthorization: Digest username="guest" realm="DIGEST", nonce="MOSQZ0itBAA=44abb6784cc9cbfc605a5b0893d36f23de95fcff", uri="/digest", algorithm=MD5, response="df56389ba3f7c52e9d7551115d67472f", qop="auth", nc=00000001, cnonce="082c875dcb2ca740"

HTTP/1.1 200 OK
```

## FormBase认证

基于表单认证

* 用户在表单中填入登录信息
* 客户端将登录信息发送给服务器
* 服务器对登录信息进行认证

### Session管理

通过Session和Cookie保存认证信息

* 客户端发送登录信息
* 服务器认证后发放用以识别用户的Session ID，写入Set-Cookie首部字段中
* 客户端将Session ID保存到Cookie中，下次发请求时自动带上Cookie
