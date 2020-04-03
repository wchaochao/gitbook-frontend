# XML基础

标签（空格分隔）： 前端知识体系

---

## 介绍

可扩展标记语言，用于描述文档或结构化数据

### 可扩展

可以通过DTD定义自己领域的标记语言

* 网页：HTML、SVG、MathML、化学ML
* 订阅：RSS、ATOM、RDF
* 描述XML：XSL（XSLT、Xpath、XSL-FO）、XLink

### 标记

使用标记来表示语义和结构，样式使用样式单表示，与XML分离

## 语法

### 指令

```xml
<? 指令 ?>
```

实例

```xml
<?xml version="1.0" standalone="yes"?>
<?xml-stylesheet type="text/css" href="./demo.css" ?>
```

## 注释

```xml
<!-- 注释 -->
```

## CDATA

```xml
<![CDATA[ 字符数据 ]]>
```

## 标记

```xml
<!-- 有内容 -->
<tagName attrName="attrValue">内容</tagName>

<!-- 无内容 -->
<tagName attrName="attrValue"/>
```

### 标记名

* 字母、数字、下划线组成
* 数字不开头
* 大小写不同

### 属性

由属性名和属性值组成

* 属性名同标记名
* 属性值同文本，可以有实体

### 内容

* 文本：可以有实体
* 嵌套标记
* 注释
* CDATA

## 实体

```
&entity;
```

| 实体 | 字符 |
| -- | -- |
| `&lt;` | < |
| `&gt;` | > |
| `&quot;` | " |
| `&apos;` | ' |
| `&amp;` | & |

## 文档结构

* 主文档
* 附属文档：文本文件（如CSS文件）、二进制文件（如图片）

```xml
<?xml version="1.0"?>
<?xml-stylesheet type="text/css" href="./demo.css"?>
<!-- 注释 -->
<![CDATA[ 字符数据 ]]>
<root attrName="attrValue">
  文本&lt;
  <tagName>嵌套标记</tagName>
  <!-- 注释 -->
  <![CDATA[ 字符数据 ]]>
</root>
```
