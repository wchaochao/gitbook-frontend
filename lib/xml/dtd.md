# DTD

标签（空格分隔）： 前端知识体系

---

文档类型定义，声明元素、属性、实体及其相互关系

## 文档类型声明

指定文档使用的DTD

* 处于XML声明之后、根元素之前
* 包含或引用文档类型定义

### 内部DTD声明

```xml
<!DOCTYPE 根元素 [DTD定义]>
```

### 外部DTD声明

私有DTD

```xml
<!DOCTYPE 根元素 SYSTEM "DTD文件地址">
```

标准DTD

```xml
<!DOCTYPE 根元素 PUBLIC "DTD文件名称" "DTD文件地址">
```

标准DTD文件名称格式为 `前缀//作者//描述//语言`

* ISO标准组织的DTD，前缀为ISO
* 非ISO标准组织的DTD，前缀为+
* 非标准组织的DTD，前缀为-

```xml
<!DCOTYPE HTML PUBLIC "-//W3C//DTD HTML//EN">
```

### 混合DTD声明

```xml
<!DOCTYPE 根元素 SYSTEM "DTD文件地址" [DTD定义]>
<!DOCTYPE 根元素 PUBLIC "DTD文件名称" "DTD文件地址" [DTD定义]>
```

## 注释

```xml
<!-- DTD注释 -->
```

## 元素声明

声明元素的名称和包含的内容

### EMPTY

空内容

```xml
<!ELEMENT 元素名称 EMPTY>
```

### ANY

任意内容

```xml
<!ELEMENT 元素名称 ANY>
```

### #PCDATA

只包含文本

```xml
<!ELEMENT 元素名称 (#PCDATA)>
```

### 子元素序列

按指定序列嵌套子元素

```
<!ELEMENT 元素名称 (子元素1, 子元素2,.....)>
```

### 子元素数目

```xml
<!ELEMENT 元素名称 (子元素名称?)> <!-- 出现零次或一次 -->
<!ELEMENT 元素名称 (子元素名称*)> <!-- 出现零次或多次 -->
<!ELEMENT 元素名称 (子元素名称+)> <!-- 出现一次或多次 -->
```

### 子元素选择

选择某个子元素

```xml
<!ELEMENT 元素名称 (子元素1 | 子元素2 |...)>
```

### 子元素分组

数个子元素当作一个分组

```xml
<!ELEMENT 元素名称 ((分组1), (分组2 | 分组3), (分组4)*)>
```

### 混合

内容为文本或子元素

```xml
<!ELEMENT 元素名称 (#PCDATA | 子元素1 | 子元素2 |...)>
<!ELEMENT 元素名称 ((#PCDATA | 子元素1 | 子元素2 |...)*)>
```

## 实体声明

声明实体，可以在文档或DTD中引用

### 实体

保存XML文档片段的项目，可通过引用载入实体

* 通用实体使用`&entity;`引用，载入数据到文档中
* 参数实体使用`%entity;`引用，载入数据到文档的DTD中

### 通用实体声明

引用时替换为实体值，实体值可以为XML片段或只包含文本声明的XML文档

```xml
<!-- 内部通用实体声明 -->
<!ENTITY 实体名称 "实体值">

<!-- 外部通用实体声明 -->
<!ENTITY 实体名称 SYSTEM "实体值地址">
<!ENTITY 实体名称 PUBLIC "实体值地址">
```

### 参数实体声明

仅可在DTD中引用，引用时替换为实体值，实体值可以为DTD片段

```xml
<!-- 内部参数实体声明 -->
<!ENTITY % 实体名称 "实体值">

<!-- 外部参数实体声明 -->
<!ENTITY % 实体名称 SYSTEM "实体值地址">
<!ENTITY % 实体名称 PUBLIC "实体值地址">
```

示例

```xml
<!ENTITY % PCD "(#PCDATA)">
<!ELEMENT FOOD %PCD;>

<!ENTITY % inlines "(PERSON | DEGREE | MODEL)">
<!ELEMENT CELL %inlines;>

<!ENTITY % player SYSTEM "palyer.dtd">
%player;
```
