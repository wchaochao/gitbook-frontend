# UEditor HTML操作

标签（空格分隔）： 前端知识体系

---

## UE.uNode接口

模拟Node接口

```javascript
interface UE.uNode {
  constructor();

  DOMString type;
  DOMString tagName;
  record<DOMString, DOMString> attrs;
  string data;
  UE.uNode parentNode;
  sequence<UE.uNode>? children;

  static UE.uNode createElement(DOMString html);
  static UE.uNode createText(DOMString data, boolean noTrans);

  UE.uNode? previousSibling();
  UE.uNode? nextSibling();
  UE.uNode? firstChild();
  UE.uNode? lastChild();

  UE.uNode appendChild(UE.uNode child);
  UE.uNode removeChild(UE.uNode child, boolean keepChildren);
  UE.uNode insertBefore(UE.uNode newChild, UE.uNode referenceChild);
  UE.uNode insertAfter(UE.uNode newChild, UE.uNode referenceChild);
  UE.uNode replaceChild(UE.uNode newChild, UE.uNode referenceChild);

  unsigned long getIndex();
  UE.uNode? getNodeById();
  sequence<UE.uNode> getNodesByTagName();
  UE.uNode traversal(fn);

  DOMString getAttr(DOMString attrName);
  void setAttr(DOMString attrName, optional DOMString attrVal);
  void setAttr(record<DOMString, DOMString or boolean> attrObj);
  void setAttr();

  DOMString getStyle(DOMString name);
  void setStyle(DOMString name, DOMString val);
  void setStyle(record<DOMString, DOMString> styleObj)

  DOMString toHtml(boolean formatter);

  DOMString innerHTML();
  UE.uNode innerHTML(optional DOMString htmlstr);
  DOMString innerText();
  UE.uNode innerHext(DOMString textStr, optional boolean noTrans);
  DOMString getData();
}
```

### 特征属性

* type：节点类型
 * root：根节点
 * element：元素节点
 * text：文本节点
 * comment：评论节点
* tagName：元素节点标签
* attrs：元素节点属性
* data：CharacterData节点的数据
* parentNode：父节点
* children：子节点，自闭合元素的children为null

### new UE.uNode(obj)

创建uNode节点，初始化uNode节点各特征属性

### 静态方法

* createElement(html)：创建元素节点
 * html为标签时，根据标签创建uNode节点
 * html为html字符串时，将html字符串解析为uNode节点，返回根节点的第一个子元素
* createText(data, noTrans)：创建文本节点，noTrans为true时不对data进行HTML转义

### 节点关系

* previousSibling()：获取前一个兄弟节点
* nextSibling()：获取后一个兄弟节点
* firstChild()：获取第一个子节点
* lastChild()：获取最后一个子节点

### 节点操作

* appendChild(child)：追加子节点
* removeChild(child, keepChildren)：移除子节点，keepChildren为true时保留child的子节点
* insertBefore(newChild, referenceChild)：插入到指定子节点前
* insertAfter(newChild, referenceChild)：插入到指定子节点后
* replaceChild(newChild, referenceChild)：替换子节点

### 节点查找

* getIndex()：获取当前节点在父节点下的位置索引
* getNodeById(id)：根据id查找节点
* getNodesByTagName(tagNames)：根据tagName查找节点列表，tagNames可以说空格分隔的tagName
* traversal()：遍历所有后代节点，后序遍历

### 属性操作

* getAttr(attrName)：获取属性
* setAttr(attrName, attrVal)：设置单个属性，attrVal不存在时删除属性
* setAttr(attrObj)：设置多个属性，属性值为false时删除属性
* setAttr()：删除所有属性

### 样式操作

* getStyle(name)：获取style属性中的样式值
* setStyle(name, val)：设置单个style属性的样式值
* setStyle(styleObj)：设置多个style属性的样式值

### HTML转换

#### UE.htmlparser(htmlstr, ignoreBlank)

将html字符串转换为uNode节点

```
1. 去除html字符串中的fillChar字符
2. ignoreBlank为false时，去除标签前后的空白符
3. 设置根uNode节点，使用正则匹配html字符串中的开始标签、结束标签、注释
4. 匹配到开始标签
  a. 开始标签在dtd定义的$cdata标签（script、style）中，当作文本节点处理
  b. 开始标签不在dtd定义的$cdata标签中，当作元素节点处理
    i. 开始标签需要特定的父标签时，从当前节点往上查，能查到则开始标签的parent为查到的节点，不能查到则在当前节点下生成一个父标签节点
    ii. 生成元素节点，初始化tagName、attrs、parentNode、children，append进parent
    iii. 开始标签为自闭合标签时，当前节点设为parent，否则当前节点设为生成的元素节点
6. 匹配到结束标签
  a. 结束标签在dtd定义的$cdata标签中，当作文本节点处理
  b. 结束标签不在dtd定义的$cdata标签中，从当前节点开始往上查找与结束标签匹配的节点
    i. 能查到则将当前节点置为查找到的节点的父节点
    ii. 不能查到则放弃处理该结束标签
7. 匹配到注释，生成注释节点，初始化data
8. 处理两次匹配之间的文本和最后剩下的文本
  a. 当前节点不需要特定子元素时，生成文本节点，append进parent
  b. 当前节点需要特定子元素时，生成特定子元素的节点，append进parent，生成文本节点append进子元素节点
```

#### toHtml(formatter)

将uNode节点转换为html字符串

```
1. 按深度递归uNode节点，根据type类型生成相应的html
2. type为element时
  a. 元素为自闭合元素，输出<tagName attrhtml/>
  b. 元素为非闭合元素，输出<tagName attrhtml>children</tagName>
3. type为txet时
  a. parent为pre时，不做任何处理直接输出
  b. parent为script、style时，对data进行html反转义输出
  c. parent为其他时，将data中的空格转换为&nbsp;输出
4. type为comment时，输出<!--data-->
5. formatter为true时，添加换行符和缩进
  a. root从第二个子元素开始的块级元素前进行换行
  b. element不为pre且有子元素时，在开始标签后、结束标签前进行换行符，子元素进行缩进，从第二个子元素开始的块级元素前进行换行
```

### 内容操作

* innerHTML()：获取innerHTML
* innerHTML(htmlStr)：设置innerHTML
* innerText()：获取innerText
* innerText(textStr, noTrans)：设置innerText
* getData()：获取文本或注释内容
