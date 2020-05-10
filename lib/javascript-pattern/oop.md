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

* 通过取值器、赋值器实现对取值、赋值的完全控制
* 隐藏内部细节、公开部分接口，方便修改内部的数据和算法

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

```javascript
const Book = (function () {
  // 私有静态属性
  let numOfBooks = 0

  // 私有静态方法
  function checkIsbn (isbn) {
    // ...
  }

  return function Book (isbn, title, author) {
    let _isbn, _title, _author

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

    numOfBooks++
    if (numOfBooks > 50) {
      throw new Error('Book: Only 50 instances of Book can be created')
    }

    this.setIsbn(isbn)
    this.setTitle(title)
    this.setAuthor(author)
  }
})()

// 公共静态方法
Book.convertToTitleCase = function (inputString) {
  // ...
}

Book.prototype = {
  display () {
    // ...
  }
}
```

### 常量

通过创建只有取值器没有赋值器的私有变量来模仿常量

```javascript
// 常量
const Book = (function () {
  let constants = {
    UPPER_BOUND: 10,
    LOWER_BOUND: -10
  }

  const Ctor = function (arg) {
    // ...
  }

  // 特权静态方法
  Ctor.getConstant = function (name) {
    return constants[name]
  }

  return Ctor
})()
```

## 继承

继承公有成员和特权成员，减少重复性代码

### 类式继承

一个类继承另一个类

```javascript
function Person (name) {
  this.name = name
}

Person.prototype.getName = function () {
  return this.name
}

function Author (name, books) {
  Person.call(this, name)
  this.books = books
}

Author.prototype = new Person()
Author.prototype.constructor = Author
Author.prototype.getBooks = function () {
  return this.books
}
```

#### extend函数

使用extend函数完成类似继承

```javascript
function extend (subClass, superClass) {
  const F = function () {}
  F.prototype = superClass.prototype
  subClass.prototype = new F()
  subClass.prototype.constructor = subClass

  subClass.super = superClass.prototype
  if (superClass.prototype.constructor === Object.prototype.constructor) {
    superClass.prototype.constructor = superClass
  }
}

function Person (name) {
  this.name = name
}

Person.prototype.getName = function () {
  return this.name
}

function Author (name, books) {
  Author.super.constructor.call(this, name)
  this.books = books
}

extend(Author, Person)

Author.prototype.getBooks = function () {
  return this.books
}
Author.prototype.getName = function () {
  const name = Author.super.getName.call(this)
  return `${name}, Author of ${this.getBooks().join(, )}`
}
```

### 原型式继承

利用原型链实现继承

**注意**：创建自己的引用类型副本，不要修改原型对象上的引用类型

```javascript
function inherit (object) {
  function F () {}
  F.prototype = object
  return new F()
}

const Person = {
  name: 'default name',
  getName = function () {
    return this.name
  }
}

const Author = inherit(Person)
Author.books = []
Author.getBooks = function () {
  return this.books
}

const author = inherit(Author)
author.name = 'Jack'
author.books = ['JavaScript Design Patterns']
```

### 多亲继承

通过混合将一些通用方法扩充到类中

```javascript
function augment (receivingClass, givingClass, ...args) {
  if (args.length) {
    for (const methodName of args) {
      receivingClass.prototype[methodName] = givingClass.prototype[methodName]
    }
  } else {
    for (const methodName in givingClass.prototype) {
      if (!receivingClass.prototype[methodName]) {
        receivingClass.prototype[methodName] = givingClass.prototype[methodName]
      }
    }
  }
}

function Mixin () {}
Mixin.prototype = {
  serialize () {
    let output = []
    for (const key in this) {
      output.push(`${key}: ${this.key}`)
    }
    return output.join(', ')
  }
}

augment(Author, Mixin, serialize)
```
