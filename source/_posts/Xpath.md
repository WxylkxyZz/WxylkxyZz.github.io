---
title: "Xpath"
date: 2023-12-30 16:29:46
categories:
  - "Python"
tags:
  - "Xpath"
aging: true
aging_days: 30
---

# Xpath学习

<!--more-->

本片完全基于[菜鸟教程](https://www.runoob.com/xpath/xpath-nodes.html)学习

## 术语

```xml
<?xml version="1.0" encoding="UTF-8"?>
 
<bookstore>
  <book>
    <title lang="en">Harry Potter</title>
    <author>J K. Rowling</author>
    <year>2005</year>
    <price>29.99</price>
  </book>
</bookstore>
```

下面以此xml文档为练习

- 节点  
  在 XPath 中，有七种类型的节点：元素、属性、文本、命名空间、处理指令、注释以及文档（根）节点。XML 文档是被作为节点树来对待的。树的根被称为文档节点或者根节点。

  - 文档节点/根节点 : `<bookstore>`
  - 属性节点 : `lang="en"`
  - 元素节点 : 例如 `<year>2005</year>`
- 基本值(原子值)  
  基本值是无父或无子的节点

  - eg : `J K. Rowling`、`"en"`
- 项目（Item）  
  项目是基本值或者节点
- 节点关系

  - 父
  - 子
  - 同胞
  - 先辈 (从父往上)
  - 后代 (从子往下)

## 语法

```xml
<?xml version="1.0" encoding="UTF-8"?>
  
<bookstore>
  
<book>
  <title lang="eng">Harry Potter</title>
  <price>29.99</price>
</book>
  
<book>
  <title lang="eng">Learning XML</title>
  <price>39.95</price>
</book>
  
</bookstore>
```

下面以此xml文档为练习

### 选取节点

| 表达式 | 描述 |
| --- | --- |
| nodename | 选取此节点的所有子节点 |
| / | 从根节点起选取 |
| // | 从匹配选择的当前节点选择文档中的节点，而不考虑它们的位置（取子孙节点） |
| . | 选取当前节点 |
| .. | 选取当前节点的父节点 |
| @ | 选取属性 |

举例:

| xpath表达式 | 结果 |
| --- | --- |
| bookstore | 选取bookstore的所有子节点 |
| /bookstore | 以bookstore作根节点 (注释：假如路径起始于正斜杠( / )，则此路径始终代表到某元素的绝对路径！) |
| bookstore/book | 选取属于 bookstore 的子元素的所有 book 元素 |
| //book | 选取所有 book 子元素，而不管它们在文档中的位置 |
| bookstore//book | 选择属于 bookstore 元素的后代的所有 book 元素，而不管它们位于 bookstore 之下的什么位置。 |

### 谓语

谓语用来查找某个特定的节点或者包含某个指定的值的节点。  
谓语被嵌在方括号中。

| 表达式 | 结果 |
| :--- | :--- |
| /bookstore/book[1] | /bookstore下第一个book节点 |
| /bookstore/book[last()] | /bookstore下最后一个book节点 |
| /bookstore/book[last()-1] | /bookstore下倒数第二个book节点 |
| /bookstore/book[position()<3] | 选取最前面的两个属于/bookstore的子元素的book元素 |
| //title[@lang] | 所有具有lang属性的title |
| //title[@lang=’eng’] | 所有带lang属性且属性值为eng的title |
| /bookstore/book[price>35] | /bookstore的所有price元素>35的book元素 |
| /bookstore/book[price>35]/title | 所有/bookstore下price>35的book元素的子元素title |

### 通配符

`*` : 匹配任意节点  
`@*`: 匹配任意属性节点  
`node()`: 匹配任意类型的节点

### 选取若干路径

`|`运算符

| 路径表达式 | 结果 |
| --- | --- |
| //book/title \| //book/price | 选取 book 元素的所有 title 和 price 元素。 |

## 轴

| 轴名称 | 结果 |
| --- | --- |
| ancestor | 选取当前节点的所有先辈（父、祖父等）。 |
| ancestor-or-self | 选取当前节点的所有先辈（父、祖父等）以及当前节点本身。 |
| attribute | 选取当前节点的所有属性。 |
| child | 选取当前节点的所有子元素。 |
| descendant | 选取当前节点的所有后代元素（子、孙等）。 |
| descendant-or-self | 选取当前节点的所有后代元素（子、孙等）以及当前节点本身。 |
| following | 选取文档中当前节点的结束标签之后的所有节点。 |
| following-sibling | 选取当前节点之后的所有兄弟节点。 |
| namespace | 选取当前节点的所有命名空间节点。 |
| parent | 选取当前节点的父节点。 |
| preceding | 选取文档中当前节点的开始标签之前的所有节点。 |
| preceding-sibling | 选取当前节点之前的所有同级节点。 |
| self | 选取当前节点。 |
