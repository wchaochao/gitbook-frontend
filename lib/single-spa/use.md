# single-spa使用

标签（空格分隔）： 前端知识体系

---

## 主应用

### 提供html

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <title>Your application</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <meta name="importmap-type" content="systemjs-importmap">
    <script type="systemjs-importmap">
      {
        "imports": {
          "single-spa": "https://cdn.jsdelivr.net/npm/single-spa@5.1.1/lib/system/single-spa.min.js"
        }
      }
    </script>
    <link rel="preload" href="https://cdn.jsdelivr.net/npm/single-spa@5.1.1/lib/system/single-spa.min.js" as="script" crossorigin="anonymous" />
    <script src="https://cdn.jsdelivr.net/npm/import-map-overrides@1.12.0/dist/import-map-overrides.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/systemjs@6.2.5/dist/system.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/systemjs@6.2.5/dist/extras/amd.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/systemjs@6.2.5/dist/extras/named-exports.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/systemjs@6.2.5/dist/extras/named-register.min.js"></script>
  </head>
  <body>
    <script>
      System.import('single-spa').then(function (singleSpa) {
        singleSpa.start();
      })
    </script>
    <!-- See https://github.com/joeldenning/import-map-overrides#user-interface  -->
    <import-map-overrides-full show-when-local-storage="overrides-ui"></import-map-overrides-full>
  </body>
</html>
```

### 管理子应用

```javascript
import * as singleSpa from 'single-spa'

singleSpa.registerApplication(
  'app1',
  () => import('../app1/app1.js'),
  pathPrefix('/app1')
)

singleSpa.registerApplication(
  'app2',
  () => import('../app2/app2.js'),
  pathPrefix('/app2')
)

singleSpa.start()

function pathPrefix (prefix) {
  return function (location) {
    return location.pathname.startsWith(`${prefix}`)
  }
}
```

## 子应用

提供加载资源

```javascript
import singleSpaReact from 'single-spa-react'
import React from 'react'
import ReactDOM from 'react-dom'
import Root from './root.component.jsx'

const reactLifecycles = singleSpaReact({
  React,
  ReactDOM,
  rootComponent: Root,
  domElementGetter
})

export function bootstrap (props) {
  return reactLifecycles.bootstrap(props)
}

export function mount (props) {
  return reactLifecycles.mount(props)
}

export function unmount (props) {
  return reactLifecycles.unmount(props)
}

function domElementGetter () {
  let el = document.getElementById('app1')
  if (!el) {
    el = document.createElement('div')
    el.id = 'app1'
    document.body.appendChild(el)
  }
  return el
}
```
