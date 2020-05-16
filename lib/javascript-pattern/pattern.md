# 设计模式

标签（空格分隔）： 前端知识体系

---

## 单体模式

将代码组织成一个逻辑单元，只能实例化一次

### 基本结构

对象字面量

* 将关联的属性和方法组织在一起
* 划分命名空间，避免命名冲突

```javascript
const Singleton = {
  attribute1: true,
  attribute2: 10,

  method1 () {
    // ...
  },

  method2 () {
    // ...
  },

  init () {
    // ...
  }
}
```

### 私有成员

划分好公有成员和私有成员，重构私有成员时不必担心会殃及其他代码

* 使用下划线表示
* 使用闭包创建

```javascript
MyNamespace.singleton = (function () {
  let privateAttribute1 = false
  let privateAttribute2 = [1, 2, 3]

  function privateMethod1 () {
    // ...
  }
  function privateMethod2 () {
    // ...
  }

  return {
    publicAttribute1: true,
    publicAttribute2: 10,

    publicMethod1 () {
      // ...
    },
    publicMethod2 () {
      // ...
    }
  }
})()
```

### 惰性加载

将实例化推迟到需要使用的时候，常用于必须加载大量数据的单体

```javascript
MyNamespace.singleton = (function () {
  let uniqueInstance

  function constructor () {
    // ...
  }

  return {
    getInstance () {
      if (!uniqueInstance) {
        uniqueInstance = constructor()
      }
      return uniqueInstance
    }
  }
})()
```

### 分支

屏蔽选择细节，提供统一接口

```javascript
MyNamespace.singleton = (function () {
  let objectA = {
    method1 () {
      // ...
    }
  }

  let objectB = {
    method1 () {
      // ...
    }
  }

  return someCondition ? objectA : objectB
})()
```
