# 面向对象

标签（空格分隔）： 前端知识体系

---

## 灵活性

任何东西都可以在运行时修改

* 编程：函数式编程、面向对象编程
* 弱类型：自动类型转换、强制类型转换
* 函数：作为变量、参数、返回值、闭包
* 对象：一切皆对象、属性可任意改变

## 接口

针对接口编程

### 注释法

用注释描述接口

```javascript
/**
 * interface Composite {
 *   function add(child);
 *   function remove(child);
 *   function getChild(index);
 * }
 *
 * interface FormItem {
 *   function save();
 * }
 */
const CompositeForm = function (id, method, action) { // implements Composite, FormItem
  // ...
}

// implement the Composite interface
CompositeForm.prototype.add = function (child) {
  // ...
}
CompositeForm.prototype.remove = function (child) {
  // ...
}
CompositeForm.prototype.getChild = function (index) {
  // ...
}

// implete the FormItem interface
COmpositeForm.prototype.save = function () {
  // ...
}
```

### 属性检查法

使用属性声明实现的接口

```javascript
/**
 * interface Composite {
 *   function add(child);
 *   function remove(child);
 *   function getChild(index);
 * }
 *
 * interface FormItem {
 *   function save();
 * }
 */
const CompositeForm = function (id, method, action) { // implements Composite, FormItem
  this.implementsInterfaces = ['Composite', 'FormItem']
  // ...
}

function addForm (formInstance) {
  if (!implements(formInstance, 'Composite', 'FormItem')) {
    throw new Error('Object does not implement a required interface')
  }
}

function implements (object, ...args) {
  const map = arrayToMap(object.implementsInterfaces)
  return args.every(item => map[item])
}

function arrayToMap (arr) {
  return arr.reduce((result, item) => {
    result[item] = true
    return result
  }, {})
}
```

### 鸭式辨型法

检查是否实现了接口声明的方法

```javascript
function Interface (name, methods) {
  if (arguments.length !== 2) {
    throw new Error(`Interface constructor called with ${arguments.length} arguments, but expected exactly 2.`)
  }

  this.name = name
  this.methods = methods.map(method => {
    if (typeof method !== 'string') {
      throw new Error(`Interface constructor expects method names to be passed in as a string`)
    }
    return method
  })
}

Interface.ensureImplements = function (object, ...args) {
  if (!args.length) {
    throw new Error(`Function Interface.ensureImplements called with ${arguments.length} arguments but expected exactly at least 2.`)
  }

  args.forEach(item => {
    if (!(item instanceof Interface)) {
      throw new Error(`Function Interface.ensureImplements expects arguments two and above to be instance of Interface.`)
    }

    item.methods.forEach(method => {
      if (!object[method] || typeof object[method] !== 'function') {
        throw new Error(`Function Interface.ensureImplements: object does not implement the ${interface.name} interface. Method ${method} was not found`)
      }
    })
  })
}

const Composite = new Interface('Composite', ['add', 'remove', 'child'])
const FormItem = new Interface('FormItem', ['save'])

const CompositeForm = function (id, method, action) {
  // ...
}

function addForm (formInstance) {
  Interface.ensureImplements(formInstance, Composite, FormItem)
  // ...
}
```

## 封装

对对象进行信息隐藏

### 门户大开型

所有属性和方法都是公开的，无法保护内部数据

```javascript
function Book (isbn, title, author) {
  if (isbn == undefined) {
    throw new Error('Book constructor rquires an isbn.')
  }
  // 公有属性
  this.isbn = isbn
  this.title = title || 'No title specified'
  this.author = author || 'No author specified'
}

// 公有方法
Book.prototype.display = function () {
  // ...
}
```

### 约定型

使用命名规范区别私有成员

```javascript
function Book (isbn, title, author) {
  this.setIsbn(isbn)
  this.setTitle(title)
  this.setAuthor(author)
}

Book.prototype = {
  _checkIsbn () {
    // ...
  },
  getIsbn () {
    return this._isbn
  },
  setIsbn (isbn) {
    if (!this._checkIsbn(isbn)) {
      throw new Error('Book: invalid ISBN')
    }
    this._isbn = isbn
  },
  getTitle () {
    return this._title
  },
  setTitle (title) {
    this._title = title || 'No title specified'
  },
  getAuthor () {
    return this._author
  },
  setAuthor (author) {
    this._author = author || 'No author specified'
  },
  display () {
    // ...
  }
}
```

### 闭包型

使用闭包实现私有成员

```javascript
function Book (isbn, title, author) {
  // 私有属性
  let _isbn, _title, _author

  // 私有方法
  function _checkIsbn () {
    // ...
  }

  // 特权方法
  this.getIsbn = function () {
    return _isbn
  }
  this.setIsbn = function (isbn) {
    if (!_checkIsbn(isbn)) {
      throw new Error('Book: invalid ISBN')
    }
    _isbn = isbn
  }

  this.getTitle = function () {
    return _title
  }
  this.setTitle = function (title) {
    _title = title || 'No title specified'
  }

  this.getAuthor = function () {
    return _author
  }
  this.setAuthor = function (author) {
    _author = author || 'No author specified'
  }

  this.setIsbn(isbn)
  this.setTitle(title)
  this.setAuthor(author)
}

Book.prototype = {
  display () {
    // ...
  }
}
```

### 静态成员

包括静态属性、静态方法、私有静态属性、私有静态方法