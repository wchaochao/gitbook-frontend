# 页面渲染

标签（空格分隔）： 前端知识体系

---

## 页面加载过程

* 域名解析
* TCP连接
* 发送HTTP请求
* 服务器处理请求并返回HTTP响应
* 浏览器解析渲染页面

![navigation Timing](http://wuduoyi.com/note/what-happen-when-browser-loading-the-page/timing-overview.png)

### 域名解析

按以下优先级将域名解析为IP

* 浏览器DNS缓存
* 操作系统DNS缓存、hosts文件
* 本地域名服务器
* 路由器DNS缓存
* 根域名服务器
* 各级域名服务器

**注意：**该域名使用浏览器代理时，由浏览器代理决定如何解析

![DNS解析](https://image-static.segmentfault.com/161/828/1618288278-57f00bf9444dd)

### TCP连接

* 三次握手
* 传输数据
* 四次挥手

![TCP连接](https://upload-images.jianshu.io/upload_images/1641067-cfbdc82ef9f5c5c0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/546/format/webp)

### 发送HTTP请求

* 请求行：`Method Request-URL HTTP-Version`
* 请求头：向服务器传递请求的附加信息和客户端自身的信息
* 请求体：向服务器传递数据

### 接收HTTP响应

* 响应行：响应状态
* 响应头：给浏览器的附加信息
* 响应体：返回的数据

## 渲染引擎

* HTML解释器：将HTML解析成DOM树
* CSS解释器：将CSS解析成CSSOM树，为DOM提供样式信息
* JavaScript引擎：解释执行JavaScript代码，通过DOM接口和CSSOM接口修改网页内容和样式，从而改变渲染结果
* 布局：将DOM树和CSSOM树结合成渲染树，计算各节点的大小位置等布局信息
* 绘图：使用图形库将布局计算后的各个网页节点绘制成图像结果

![渲染引擎](14)

### 主流程

![渲染过程](http://taligarsiel.com/Projects/webkitflow.png)

```
1. 解析HTML文档
2. 节点为script时
  a. 内联脚本：立即执行脚本，会阻碍文档解析
  b. 外部脚本：加载并执行脚本，加载和执行都会阻塞文档解析
  c. async脚本：异步加载脚本，加载完后立即执行脚本，加载不会阻塞文档解析，在文档解析完前执行时会阻碍文档解析
  d. defer脚本：异步加载脚本，文档解析完后按顺序执行脚本，加载和执行都不会阻塞文档解析
  e. 动态脚本：加载和执行都不阻塞文档解析
3. 节点为style时，
  a. 内部样式：解析CSS
  b. 外部样式：异步加载并解析CSS
  c. 解析CSS时不会阻塞HTML解析，但会阻塞JavaScript执行
4. 节点为img、video、iframe等依赖外部资源的节点时，异步加载资源，继续HTML解析
5. HTML解析完成后触发DOMContentLoaded事件
6. 所有外部资源加载完时触发onload事件
7. 根据DOM树和CSSOM树生成渲染树
10. 根据渲染树将节点树的每一个节点布局在屏幕上的正确位置
11. 遍历渲染树绘制所有节点，为每一个节点适用对应的样式
```

## 资源加载

### 资源类型

* HTML
* JavaScript
* CSS
* 图片
* SVG
* 音视频和字幕
* 字体文件
* XSL样式表

### 资源缓存

缓存资源，提高资源的使用效率

```
1. 建立一个资源的缓存池
2. 加载资源前，根据URL去资源池查找相应资源
3. 资源存在且有效，直接使用资源
4. 资源不存在或无效，发送请求加载资源，加载完成后放入缓存池中
```

缓存位置

* memory cache：内存缓存，读取快
* disk cache：磁盘缓存，时效长

### 资源加载器

* 特定资源加载器：加载某种特定资源
* 缓存资源加载器：特定资源加载器共享它来查找并获取缓存资源
* 通用资源加载器：特定资源加载器共享它来从网络或文件系统中获取资源

### 加载过程

```
1. 创建加载器异步加载资源
2. 当加载过程（如加载JavaScript）阻碍主线程的渲染时，启动另外一个线程进行预扫描和预加载，即遍历后面的HTML，收集需要加载的资源去加载
3. 由浏览器调度多个标签页的请求，同源时可以并发加载2~6个资源，高优先级的请求和同步请求优先加载
```

## 加载优化

### 减少连接

* 使用DNS预解析
* 使用持久链接

### 减少请求

* 使用HTTP缓存
* 减少重定向
* 减少错误的链接请求
* 减少不必要的请求

### 减少资源数

* 内嵌小型资源，如内嵌JavaScript、CSS、图片
* 合并资源，如JavaScript、CSS、图片合并

### 减少请求数据

* 减少不必要的cookie
* 减少不必要的首部数据

### 减少响应数据

* 代码压缩
* 传输压缩
* 使用合适格式的图片

### 优化加载顺序

* 核心部分优先加载，非核心部分异步加载
* 使用preload、prefetch调整资源优先级
* 多域名并行加载

## HTML解析

### HTML结构

* 树状结构：节点间的父子关系
* 框结构：iframe嵌套
* 层次结构：分层渲染

### HTML解释

```
1. 从网络或本地文件中获取HTML字节流
2. 使用解码器将字节流转换为字符流
3. 使用词法分析器将字符流转换为Token流
4. 使用XSSAuditor验证Token，过滤掉不安全的内容
5. 使用语法分析器生成节点，组成DOM树
  a. 节点为script时，会将JavaScript代码交给JavaScript引擎进行解析，阻碍HTML解析
  b. 节点为style时，会将CSS代码交给CSS解释器解析，阻碍JavaScript解析
```

## CSS解析

## JavaScript解析

## 布局

## 绘制
