# registerApplication

标签（空格分隔）： 前端知识体系

---

注册子应用信息

## 参数

### arguments参数

```javascript
registerApplication(
  name: string
  appOrLoadApp: Application | () => resolve(Application)
  activeWhen: (location) => boolean
  customProps?: object
)
```

### object参数

```javascript
registerApplication({
  name: string
  appOrLoadApp: Application | () => resolve(Application)
  activeWhen: string | (location) => boolean | Array<string | (location) => boolean>
  customProps?: object
})
```

## Applications

```javascript
Array<{
  name: string
  appOrLoadApp: Application | () => resolve(Application)
  activeWhen: string | (location) => boolean | Array<string | (location) => boolean>
  customProps?: object,
  loadErrorTime: null,
  status: NOT_LOADED,
  parcels: {},
  devtools: {
    overlays: {
      options: {},
      selectors: []
    }
  }
}>
```

### 操作

* getAppNames(): 获取已注册的应用名称
* getMountedNames(): 获取已挂载的应用名称
* getRawAppData(): 获取已注册的应用信息
* getAppStatus(appName): 获取指定应用的状态
