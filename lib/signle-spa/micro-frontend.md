# 微前端

标签（空格分隔）： 前端知识体系

---

将一个大的单页应用拆分为多个小的单页应用，各服务独立开发、独立部署、不限技术栈

## 结构

![结构](https://martinfowler.com/articles/micro-frontends/deployment.png)

### 主应用

* 渲染公共部分
* 解决认证和导航等跨应用问题
* 注册、加载、挂载、卸载子应用

### 子应用

独立开发，提供加载资源

* Applications: 提供应用
* Parcels: 提供组件
* Utility: 提供方法

## 机制

![机制](https://pic2.zhimg.com/80/v2-d55549366b52cde19c93835cfa2a58c9_720w.jpg)

### 加载

加载子应用资源

#### 资源

* Config Entry
* HTML Entry

#### 方式

* 按需加载
* 预加载
* 公共依赖加载: 使用webpack的externals或dll
 * 公共依赖库
 * 公共样式、函数、组件库

### 挂载

根据各子应用的技术栈进行挂载

#### 隔离

* JS沙箱
* CSS隔离
 * BEM约定
 * CSS预处理器
 * CSS Modules
 * CSS in JS
 * Shadow DOM

#### 通信

* 路由
* 事件总线
* store

### 移除

清理工作

## 实现

### 服务器端模板组合

每次切换应用都刷新了页面

```html
<html lang="en" dir="ltr">
  <head>
    <meta charset="utf-8">
    <title>Feed me</title>
  </head>
  <body>
    <h1>🍽 Feed me</h1>
    <!--# include file="$PAGE.html" -->
  </body>
</html>
```

### 编译时集成

每个应用的发布都会导致整个应用的编译和发布

```json
{
  "name": "@feed-me/container",
  "version": "1.0.0",
  "description": "A food delivery web app",
  "dependencies": {
    "@feed-me/browse-restaurants": "^1.2.3",
    "@feed-me/order-food": "^4.5.6",
    "@feed-me/user-profile": "^7.8.9"
  }
}
```

### 运行时集成

#### iframe

* 加载自己的资源
* 隔离太深交互困难、不灵活

```html
<html>
  <head>
    <title>Feed me!</title>
  </head>
  <body>
    <h1>Welcome to Feed me!</h1>

    <iframe id="micro-frontend-container"></iframe>

    <script type="text/javascript">
      const microFrontendsByRoute = {
        '/': 'https://browse.example.com/index.html',
        '/order-food': 'https://order.example.com/index.html',
        '/user-profile': 'https://profile.example.com/index.html',
      };

      const iframe = document.getElementById('micro-frontend-container');
      iframe.src = microFrontendsByRoute[window.location.pathname];
    </script>
  </body>
</html>
```

### javascript

灵活

```html
<html>
  <head>
    <title>Feed me!</title>
  </head>
  <body>
    <h1>Welcome to Feed me!</h1>

    <!-- These scripts don't render anything immediately -->
    <!-- Instead they attach entry-point functions to `window` -->
    <script src="https://browse.example.com/bundle.js"></script>
    <script src="https://order.example.com/bundle.js"></script>
    <script src="https://profile.example.com/bundle.js"></script>

    <div id="micro-frontend-root"></div>

    <script type="text/javascript">
      // These global functions are attached to window by the above scripts
      const microFrontendsByRoute = {
        '/': window.renderBrowseRestaurants,
        '/order-food': window.renderOrderFood,
        '/user-profile': window.renderUserProfile,
      };
      const renderFunction = microFrontendsByRoute[window.location.pathname];

      // Having determined the entry-point function, we now call it,
      // giving it the ID of the element where it should render itself
      renderFunction('micro-frontend-root');
    </script>
  </body>
</html>
```

#### Web Component

使用Web Component渲染

```html
<html>
  <head>
    <title>Feed me!</title>
  </head>
  <body>
    <h1>Welcome to Feed me!</h1>

    <!-- These scripts don't render anything immediately -->
    <!-- Instead they each define a custom element type -->
    <script src="https://browse.example.com/bundle.js"></script>
    <script src="https://order.example.com/bundle.js"></script>
    <script src="https://profile.example.com/bundle.js"></script>

    <div id="micro-frontend-root"></div>

    <script type="text/javascript">
      // These element types are defined by the above scripts
      const webComponentsByRoute = {
        '/': 'micro-frontend-browse-restaurants',
        '/order-food': 'micro-frontend-order-food',
        '/user-profile': 'micro-frontend-user-profile',
      };
      const webComponentType = webComponentsByRoute[window.location.pathname];

      // Having determined the right web component custom element type,
      // we now create an instance of it and attach it to the document
      const root = document.getElementById('micro-frontend-root');
      const webComponent = document.createElement(webComponentType);
      root.appendChild(webComponent);
    </script>
  </body>
</html>
```

## 问题

* 应用重复打包
* 应用开发调试
* 应用代码管理、CI管理
