# HTTP缓存

标签（空格分隔）： 前端知识体系

---

代理服务器或客户端本地磁盘内保存的资源副本

## 分类

* 私有缓存：只能向特定用户提供缓存，如浏览器缓存
* 共享缓存：可向任意方提供缓存，如缓存服务器、CDN、负载均衡器

## 设置

### 缓存规则

* HTTP/1.1: Cache-Control首部
* HTTP/1.0: Pragma首部

### 缓存时间

* HTTP/1.1: Cache-Control首部的max-age指令
* HTTP/1.0: Expires首部

### 缓存标识

* HTTP/1.1: ETag首部
* HTTP/1.0: Last-Modified首部

### 缓存校验

* HTTP/1.1: If-None-Match首部
* HTTP/1.0: If-Modified-Since首部

## 策略

![缓存策略](https://raw.githubusercontent.com/wchaochao/images/master/gitbook-network-base/http-cache-decision-tree.png)
