# XML基础

标签（空格分隔）： 前端知识体系

---

## 介绍

可扩展标记语言，用于描述文档或结构化数据

### 可扩展

可以通过DTD定义自己领域的标记语言

* 网页：HTML、SVG、MathML、化学ML
* 订阅：RSS、ATOM、RDF
* 描述XML：XSL（XSLT、XPath、XSL-FO）、XLink

### 标记

使用标记来表示语义和结构，样式使用样式单表示，与XML分离

## 语法

### 指令

```xml
<?target data?>
```

实例

```xml
<?xml version="1.0" standalone="yes" encoding="utf-8"?>
<?xml-stylesheet type="text/css" href="./demo.css"?>
```

### 注释

```xml
<!-- 注释 不能有两相连的连字符 -->
```

### CDATA

```xml
<![CDATA[ 字符数据 不是结尾三字符 ]]>
```

实例

```xml
<![CDATA[
    <?xml version="1.0"?>
    <!-- 不是注释 -->
    <greeting>hello xml</greeting>
    <h1></h1>
  ]]>
```

### 标记

```xml
<!-- 有内容 -->
<tagName attrName="attrValue">内容</tagName>

<!-- 无内容 -->
<tagName attrName="attrValue"/>
```

#### 标记名

* 字母、数字、下划线、连字符组成
* 字母、下划线开头
* 大小写不同

#### 属性

等号分隔的属性名、属性值对

* 属性名同标记名，不能重复
* 属性值同文本，引号包围，可以有实体引用

#### 内容

* 文本：可以有实体引用
* 嵌套标记
* 注释
* CDATA

### 实体引用

```
&entity;
```

#### 预定义实体引用

| 实体引用 | 字符 |
| -- | -- |
| `&lt;` | < |
| `&gt;` | > |
| `&quot;` | " |
| `&apos;` | ' |
| `&amp;` | & |

#### Unicode字符引用

```
&#十进制数字;
&#x十六进制数字;
```

## 文档结构

* 主文档
* 附属文档：文本文件（如CSS文件）、二进制文件（如 图片）

### 完整性

* 以xml声明开头，XML解析器会将开头的几个字符与<?xml的各类编码比较确认编码方式
* 所有元素包含在根元素中
* 元素正确结束、正确嵌套
* 属性值引号包围
* 实体分号结尾

```xml
<?xml version="1.0"?> <!-- xml声明 在开头 -->
<?xml-stylesheet type="text/css" href="./demo.css"?>
<!-- 注释 -->
<![CDATA[ 字符数据 ]]>
<root attrName="attrValue"> <!-- 根元素 -->
  文本&lt;
  <tagName prop="&lt;">嵌套标记</tagName>
  <!-- 注释 -->
  <![CDATA[ 字符数据 ]]>
</root>
```
