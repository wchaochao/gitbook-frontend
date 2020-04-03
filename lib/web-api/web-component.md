# Web Component

标签（空格分隔）： 前端知识体系

---

Web组件

* 有自己的DOM结构
* 有自己的CSS样式
* 有自己的JS与外界交互

## Custom Element

自定义HTML元素

### Automonous custom elements

继承自HTMLElement

#### 定义

```javascript
class MyElement extends HTMLElement {
  constructor () {
    super()
    // 创建元素节点
  }

  connectedCallback () {
    // 节点被加入到文档中时的回调
    // 属性已被解析，可以访问
    // 子元素还未解析，可以在setTimeout中访问
  }

  disconnectedCallback () {
    // 节点从文档中移除时的回调
  }

  static get observedAttributes () {
    return [/* 要观察的属性 */]
  }

  attributeChangedCallback (name, oldValue, newValue) {
    // 观察的属性变化时的回调
  }

  adoptedCallback () {
    // 节点被移到另一个文档时的回调
  }
}
```

#### 注册

* customElements.define('my-element', MyElement)
 * 自定义元素名要包含`-`
 * 注册前，自定义元素无法识别，注册后，自动识别
* customElements.whenDefined(name): 返回promise，注册时resolve
* customElements.get(name): 获取自定义元素的类

#### 使用

```html
<my-element></my-element>
```

### Customized built-in elements

继承自某个具体的HTML元素

#### 定义

```javascript
class HelloButton extends HTMLButtonElement {
  constructor () {
    super()
    this.addEventListener('click' => () => alert('hello'))
  }
}
```

#### 注册

```javascript
customElements.define('hello-button', HelloButton, {
  extends: 'button'
})
```

#### 使用

```html
<button is="hello-button">Click me</button>
```

## Shadow DOM

* 有自己的Shadow DOM树，外界无法通过选择器查询
* 有自己的CSS，外界样式不生效

### Shadow Host

Shadow DOM的宿主元素，用于挂载Shadow DOM

* 自定义元素
* HTML元素：`body, div, span, blockquote, header, main, aside, footer, nav, section, article`

DOM树

* light tree: 正常的DOM树
* Shadow tree: 隐藏的Shadow DOM树

两者同时存在时只会渲染Shadow tree

### Shadow Tree

* elem.attachShadow({mode: 'open' | 'closed'})：创建Shadow tree, 返回Shadow Root
 * 为open时可以通过elem.shadowRoot访问Shadow Root，为closed时elem.shadowRoot = null

### Shadow Root

Shadow DOM的根节点，类似于元素节点

* host: 宿主元素
* 元素节点属性或方法
