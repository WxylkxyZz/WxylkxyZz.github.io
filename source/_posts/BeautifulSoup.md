---
title: "BeautifulSoup"
date: 2023-12-30 16:26:24
categories:
  - "Python"
tags:
  - "BeautifulSoup"
aging: true
aging_days: 30
---

# BeautifulSoup学习

简单的直接看网上教程即可

<!--more-->

[知乎](https://zhuanlan.zhihu.com/p/490453005)

## 创建BeautifulSoup对象

```html
<html>
<head>
<meta charset="utf-8">
<title>Pyhton教程(www.sanmanong.com)</title>
</head>
<body>
<h1>我的第一个标题</h1>
<p class="title" name="sanmanong">我的第一个段落。</p>
<a href="http://www.sanmanong.com" target="_blank" rel="noopener noreferrer">Python教程</a>
</body>
</html>
```

以上述网页为例

```python
# 导库
from bs4 import BeautifulSoup
 
# 创建对象
soup = BeautifulSoup(html,'html.parser')
 
# 使用prettify()方法可以可以把要解析的字符串以标准的缩进格式输出。打印一下soup对象的内容，格式化输出：
print(soup.prettify())
```

## 四种对象

1. `Tag`

   - 即标签
   - 两种属性
     - `name` : 得到标签的名字
     - `attrs`: 得到标签的所有属性,类型是一个字典。 `soup.p['class']="newClass" del soup.p['class']`
2. `NavigableString`

   - 使用.string即可获取标签内部的文字
3. `BeautifulSoup`

   - BeautifulSoup对象表示的是一个文档的全部内容。大部分时候,可以把它当作 Tag对象，是一个特殊的Tag，同样可以获取它的类型，名称。
4. `Comment`

   - Comment对象是一个特殊类型的NavigableString对象。

## 遍历文档树

HTML基本格式:<>…构成了所属关系，形成了标签的树形结构

- 标签树的上行遍历  
  `.parent` : 节点的父亲标签  
  `.parents` : 节点先辈标签的迭代类型，用于循环遍历先辈节点
- 标签树的下行遍历：  
  `.contents` : 子节点的列表，将所有儿子节点存入列表  
  `.children` : 子节点的 __迭代类型__，与`.contents`类似，用于循环遍历儿子节点  
  `.descendants` : 子孙节点的 __迭代类型__，包含所有子孙节点，用于循环遍历
- 标签树的平行遍历  
  `.next_sibling` : 返回按照HTML文本顺序的下一个平行节点标签  
  `.previous_sibling` : 返回按照HTML文本顺序的上一个平行节点标签  
  `.next_siblings` : **迭代类型** ，返回按照HTML文本顺序的后续所有平行节点标签  
  `.previous_siblings` : __迭代类型__，返回按照HTML文本顺序的前续所有平行节点标签

下面以下述html为例

```python
html = 
"""
<html>
<head><meta charset="utf-8"><title>Python教程(www.sanmanong.com)</title></head>
<body>
<h1>我的第一个标题</h1>
<p class="title" name="sanmanong">Python教程学习网站
<a href="http://www.sanmanong.com" id="link1">三码农</a>
<a href="https://www.runoob.com/" id="link2">三码农</a>
<a href="https://www.w3school.com.cn/" id="link3">三码农</a>
</p>
</body>
</html>
"""
```

将上面内容标准化后 如下

```html
<html>
 <head>
  <meta charset="utf-8"/>
  <title>
   Python教程(www.sanmanong.com)
  </title>
 </head>
 <body>
  <h1>
   我的第一个标题
  </h1>
  <p class="title" name="sanmanong">
   Python教程学习网站
   <a href="http://www.sanmanong.com" id="link1">
    三码农
   </a>
   <a href="https://www.runoob.com/" id="link2">
    三码农
   </a>
   <a href="https://www.w3school.com.cn/" id="link3">
    三码农
   </a>
  </p>
 </body>
</html>
```

### 上行遍历

1. **parent**  
   节点的父亲标签

```python
print(soup.p.parent.name)
# 
body
```

2. **parents**  
   通过元素的parents属性可以递归得到元素的所有父辈节点。

   ```python
content = soup.head.title.string
for parent in content.parents:
    print(parent.name)
 
# content所有父节点依次是title、head、html 当然soup也算是个特殊的tag对象所以才有了[document]
title
head
html
[document]
```

### 下行遍历

1. **contents**

子节点列表 切记 是只有子节点

```python
print(soup.head.contents) # -> list类型
 
[<meta charset="utf-8"/>, <title>Python教程(www.sanmanong.com)</title>]
```

2. **children**

   ```python
print(soup.head.children)  # -> list_iterator 列表生成器
 
<list_iterator object at 0x0000013A6FBC5C88>
 
# 遍历获取所有子节点
for i in soup.head.children:
    print(i)
 
<meta charset="utf-8"/>
<title>Python教程(www.sanmanong.com)</title>
 
# 这里注意-> body标签的子节点只有title和p, a标签的输出是作为p标签内容的一个整体
for i in soup.body.children:  
    print(i)
 
输出结果：
<h1>我的第一个标题</h1>
 
 
<p class="title" name="sanmanong">Python教程学习网站
<a href="http://www.sanmanong.com" id="link1">三码农</a>
<a href="https://www.runoob.com/" id="link2">三码农</a>
<a href="https://www.w3school.com.cn/" id="link3">三码农</a>
</p>
```

3. **descendants**  
   descendants属性可以对所有tag的子孙节点进行递归循环

   ```python
for i in soup.body.descendants:
    print(i)
```

### 平行遍历

1. **兄弟节点**

也就是同级别的节点，且存在于一个父标签下

- `next_sibling`
  - 获取当前节点的下一个兄弟节点
- `previous_sibling`
  - 获取当前节点的上一个兄弟节点，如果节点不存在，则返回 None。

> PS：实际文档中的tag的next_sibling和previous_sibling属性通常是字符串或空白，因为空白或者换行也可以被视作一个节点，所以得到的结果可能是空白或者换行。

**这里可以看看Xpath学习中的节点类型**

> 在 XPath 中，有七种类型的节点：元素、属性、文本、命名空间、处理指令、注释以及文档（根）节点

```python
# repr()函数将值转化为供解释器读取的字符串形式，能将换行以“\n”形式输出
print(repr(soup.p.next_sibling))
输出结果：
'\n'
 
print(repr(soup.p.previous_sibling)) 
输出结果：
'\n'
```

如果将p标签前面的换行去掉，紧跟h1标签，运行结果如下。

```python
print(repr(soup.p.previous_sibling)) 
输出结果：
<h1>我的第一个标题</h1>
```

PS: 其实一般情况下用不到, 但我昨天还真就遇到了一个特殊情况 如下：

```python
from bs4 import BeautifulSoup
 
html = """
<div>
    <span></span>
    "内容"
    <a></a>
</div>
"""
 
soup = BeautifulSoup(html, 'html.parser')
 
# 获取第一个<span>节点之后的文本内容
text_after_first_span = soup.find('span').find_next(text=True)
 
# 获取第一个<span>节点和<a>节点之间的文本内容
text_between_span_and_a = soup.find('span').find_next('a').previous_sibling.strip()
 
print("第一个<span>节点之后的文本内容:", text_after_first_span.strip())
print("第一个<span>节点和<a>节点之间的文本内容:", text_between_span_and_a)
```

2. **全部兄弟节点**

通过next_siblings和previous_siblings属性可以对当前节点的兄弟节点迭代输出。

```python
# 以第一个标签a为当前起点，向下查找
for sibling in soup.a.next_siblings:  
    print(repr(sibling))
 
#    
\n'
<a href="https://www.runoob.com/" id="link2">三码农</a>
'\n'
<a href="https://www.w3school.com.cn/" id="link3">三码农</a>
'\n'
 
# 以第一个标签a为当前起点，向上查找
for sibling in soup.a.previous_siblings:
    print(repr(sibling))
 
输出结果：
'Python教程学习网站\n'
```

此时你一定会迷惑, 不是寻找兄弟节点吗？ 怎么把p标签内容拿来了？ 我开始学也有疑惑, 这也就是为什么要知道节点类型！

**因为文本也是节点类型！**

## 搜索文档树

### find_all()

`find_all()` 方法是 BeautifulSoup 库中用于查找所有匹配标签的方法。该方法用于搜索文档树，返回所有符合条件的元素，结果以列表形式返回。

#### 方法签名：

```python
find_all(name, attrs, recursive, string, limit, **kwargs)
```

#### 参数说明：

1. **name（标签名）：**

   - 可以是字符串，正则表达式，方法，列表或 True。
   - 如果传递字符串，查找所有匹配该标签名的元素。
   - 如果传递正则表达式，查找所有匹配该正则的元素。
   - 如果传递方法，该方法将被用于过滤元素。
   - 如果传递 True，返回所有标签。
2. **attrs（属性）：**

   - 可以是字典，包含标签的属性和值。
   - 如果传递字典，查找所有匹配该属性值的元素。
3. **recursive（递归）：**

   - 布尔值，默认为 True。
   - 如果为 True，find_all() 将递归查找所有子孙节点。
   - 如果为 False，只查找直接子节点。
4. **string（字符串）：**

   - 可以是字符串，正则表达式，方法，列表或 True。
   - 如果传递字符串，查找所有匹配该字符串内容的元素。
   - 如果传递正则表达式，查找所有匹配该正则的元素。
   - 如果传递方法，该方法将被用于过滤元素。
   - 如果传递 True，返回所有包含字符串的元素。
5. **limit（限制数量）：**

   - 数字，用于限制返回结果的数量。
6. **kwargs：**

   - 关键字参数，用于过滤元素的其他属性。

#### 使用示例：

```python
from bs4 import BeautifulSoup
 
html = """
<html>
<head>
    <title>Beautiful Soup Example</title>
</head>
<body>
    <div class="container">
        <h1>Example Page</h1>
        <ul>
            <li><a href="https://example.com">Link 1</a></li>
            <li><a href="https://example.com">Link 2</a></li>
            <li><a href="https://example.com">Link 3</a></li>
        </ul>
        <p class="description">This is an example page.</p>
    </div>
</body>
</html>
"""
 
soup = BeautifulSoup(html, 'html.parser')
 
# 查找所有 a 标签
links = soup.find_all('a')
for link in links:
    print(link.get('href'))
 
# 查找所有包含字符串 "Link" 的 li 标签
link_li = soup.find_all('li', string='Link')
for li in link_li:
    print(li.text)
 
# 查找所有包含 "description" 类的 p 标签
desc_p = soup.find_all('p', class_='description')
for p in desc_p:
    print(p.text)
```

这是一个简单的示例，说明了如何使用 `find_all()` 方法。你可以根据实际需求灵活使用参数，以获取所需的元素列表。

### find()

二者用法基本相同, 只不过后者返回的是单个元素 ，也就是第一个匹配的元素（类型依然是Tag类型），而前者返回的是所有匹配的元素组成的列表。

### 其他函数

> 另外，还有许多查询方法，其用法与前面介绍的find_all()、find()方法完全相同， 只不过查询范围不同，这里简单说明一下 。

- `find_parents()`和`find_parent()`

  - `find_parents()`返回所有祖先节点，`find_parent()`返回直接父节点。
- `find_next_siblings()`和`find_next_sibling()`

  - `find_next_siblings()`返回后面所有兄弟节点，`find_next_sibling()`返回后面第一个兄弟节点。
- `find_previous_siblings()`和`find_previous_sibling()`

  - `find_previous_siblings()`返回前面所有兄弟节点，`find_previous_sibling()`返回前面第一个兄弟节点。
- `find_all_next()`和`find_next()`

  - `find_all_next()`返回节点后所有符合条件的节点, `find_next()`返回节点后第一个符合条件的节点。
- `find_all_previous()`和`find_previous()`

  - `find_all_previous()`返回节点前所有符合条件的节点, `find_previous()`返回节点前第一个符合条件的节点。

## CSS选择器

### 介绍

CSS（层叠样式表）选择器是一种用于选择 HTML 元素的模式。它允许你根据元素的标签名、类、ID、属性等条件来选择和样式化页面中的元素。以下是一些常用的 CSS 选择器：

1. **元素选择器：**

   - `element`: 选择所有具有指定标签名的元素。

     ```css
p {
  color: blue;
}
```

2. **类选择器：**

   - `.class`: 选择所有具有指定类名的元素。

     ```css
.highlight {
  background-color: yellow;
}
```

3. **ID 选择器：**

   - `#id`: 选择具有指定 ID 的元素。

     ```css
#header {
  font-size: 18px;
}
```

4. **后代选择器：**

   - `ancestor descendant`: 选择 ancestor 内的所有 descendant 元素。

     ```css
article p {
  font-style: italic;
}
```

5. **子元素选择器：**

   - `parent > child`: 选择 parent 元素下的所有直接子元素 child。

     ```css
nav > ul {
  list-style-type: none;
}
```

6. **通用选择器：**

   - `*`: 选择所有元素。

     ```css
* {
  margin: 0;
  padding: 0;
}
```

7. **属性选择器：**

   - `[attribute]`: 选择具有指定属性的元素。

     ```css
input[type="text"] {
  border: 1px solid #ccc;
}
```

   - `[attribute=value]`: 选择具有指定属性和值的元素。

     ```css
a[href="https://www.example.com"] {
  color: red;
}
```

8. **伪类选择器：**

   - `:hover`: 选择鼠标悬停在元素上的状态。

     ```css
a:hover {
  text-decoration: underline;
}
```

   - `:nth-child(n)`: 选择元素的第 n 个子元素。

     ```css
li:nth-child(odd) {
  background-color: #f2f2f2;
}
```

这只是 CSS 选择器的一小部分。CSS 选择器允许你根据各种条件来选择元素，以便更精确地定义样式。使用 CSS 选择器可以提高代码的可维护性和灵活性。

### 例子

Beautiful Soup 提供了对 CSS 选择器的支持，你可以使用 `select` 方法通过 CSS 选择器来筛选文档中的元素。下面是一些使用 BeautifulSoup 中 CSS 选择器的基本用法：

```python
from bs4 import BeautifulSoup
 
html = """
<html>
  <body>
    <div id="container">
      <h1 class="title">Beautiful Soup</h1>
      <ul class="list">
        <li>Item 1</li>
        <li>Item 2</li>
        <li>Item 3</li>
      </ul>
      <p class="paragraph">Hello, Beautiful Soup!</p>
    </div>
  </body>
</html>
"""
 
soup = BeautifulSoup(html, 'html.parser')
 
# 使用 CSS 选择器选择元素
title = soup.select('h1.title')  # 选择 class 为 'title' 的 h1 元素
print(title[0].text)
 
items = soup.select('ul.list li')  # 选择 class 为 'list' 的 ul 下的所有 li 元素
for item in items:
    print(item.text)
 
paragraph = soup.select('div#container > p.paragraph')  # 选择 id 为 'container' 的 div 直接子元素中 class 为 'paragraph' 的 p 元素
print(paragraph[0].text)
```

解释一下这个例子：

- `h1.title` 选择所有 class 为 ‘title’ 的 h1 元素。
- `ul.list li` 选择 class 为 ‘list’ 的 ul 元素下的所有 li 元素。
- `div#container > p.paragraph` 选择 id 为 ‘container’ 的 div 直接子元素中 class 为 ‘paragraph’ 的 p 元素。

在 `select` 方法中，你可以使用常见的 CSS 选择器语法，包括标签名、类选择器、ID 选择器、子元素选择器等。这使得 Beautiful Soup 在处理 HTML 或 XML 文档时更加灵活。

PS:还有要说的一点是 **层级选择器** 中`>` 表示一个层级 `空格`代表 多个层级

### 伪类选择器

CSS 伪类是用于选择文档中不同状态或位置的元素的特殊选择器。以下是一些常见的 CSS 伪类选择器：

1. **`:hover`：**

   - 选择鼠标悬停的元素。
   - 示例：`a:hover { color: red; }`
2. **`:active`：**

   - 选择用户点击并保持按下的元素。
   - 示例：`button:active { background-color: yellow; }`
3. **`:focus`：**

   - 选择当前获得焦点的表单元素。
   - 示例：`input:focus { border: 2px solid blue; }`
4. **`:first-child`：**

   - 选择父元素下的第一个子元素。
   - 示例：`li:first-child { font-weight: bold; }`
5. **`:last-child`：**

   - 选择父元素下的最后一个子元素。
   - 示例：`li:last-child { color: green; }`
6. **`:nth-child(an+b)`：**

   - 选择父元素下按照规律排列的子元素，其中 `a` 和 `b` 是整数。
   - 示例：`tr:nth-child(2n) { background-color: #f2f2f2; }`
7. **`:nth-of-type(an+b)`：**

   - 选择父元素下按照规律排列的特定类型的子元素。
   - 示例：`ul li:nth-of-type(odd) { background-color: #f0f0f0; }`
8. **`:not(selector)`：**

   - 选择不匹配指定选择器的元素。
   - 示例：`input:not([type="text"]) { opacity: 0.5; }`
9. **`:first-of-type`：**

   - 选择父元素下的特定类型的第一个子元素。
   - 示例：`h2:first-of-type { font-size: 24px; }`
10. **`:last-of-type`：**

    - 选择父元素下的特定类型的最后一个子元素。
    - 示例：`p:last-of-type { color: red; }`

这些伪类选择器允许开发者根据元素的状态、位置或结构选择不同的元素，使得 CSS 样式更加灵活和适应各种情境。  
“按照规律排列” 指的是按照一定的数学规律选择元素。在 `:nth-child(an+b)` 和 `:nth-of-type(an+b)` 中，`a` 和 `b` 是整数，用于定义选择元素的规律。

- `an` 表示周期的长度，每隔 `a` 个元素会重复一次选择。
- `b` 表示起始位置，表示从第 `b` 个元素开始选择。

举例说明：

- `:nth-child(2n)` 选择的是所有位置是偶数的元素。
- `:nth-child(3n+1)` 选择的是所有位置是 1、4、7、10、… 的元素。
- `:nth-of-type(odd)` 选择的是所有位置是奇数的某一特定类型的元素。

这样的规律选择使得样式能够更加灵活地应用到特定位置或类型的元素上。

**“按照规律排列”** 指的是按照一定的数学规律选择元素。在 `:nth-child(an+b)` 和 `:nth-of-type(an+b)` 中，`a` 和 `b` 是整数，用于定义选择元素的规律。

- `an` 表示周期的长度，每隔 `a` 个元素会重复一次选择。
- `b` 表示起始位置，表示从第 `b` 个元素开始选择。

举例说明：

- `:nth-child(2n)` 选择的是所有位置是偶数的元素。
- `:nth-child(3n+1)` 选择的是所有位置是 1、4、7、10、… 的元素。
- `:nth-of-type(odd)` 选择的是所有位置是奇数的某一特定类型的元素。

这样的规律选择使得样式能够更加灵活地应用到特定位置或类型的元素上。

## 记录刚学习的问题:

### 实例化传入html时的问题

1. 在实例化soup对象时 `response`可以直接传 本地文件要先读入再传

```python
1. 直接传response
soup = BeautifulSoup(response,'html.parser')
 
2. 先读入html再传
filename = 'your_html_file.html'
with open(filename, 'r', encoding='utf-8') as file:
    html_content = file.read()
soup = BeautifulSoup(html_content, 'html.parser')
```

### `class`标签

`class`标签内内容有空格隔开

如果一个 HTML 元素的 `class` 属性中有空格分隔的多个值，每个空格分隔的值都被视为一个独立的类名。在你提供的例子中，`class` 属性的值是 “a-size-large product-title-word-break”，这表示该元素具有两个类名： “a-size-large” 和 “product-title-word-break”。

你可以使用以下方式选择这个具有两个类名的元素：

```css
.a-size-large {
    /* 这里是你的样式规则 */
}
 
.product-title-word-break {
    /* 这里是你的样式规则 */
}
```

或者，你也可以选择同时具有这两个类的元素：

```css
.a-size-large.product-title-word-break {
    /* 这里是你的样式规则 */
}
```

这个选择器会匹配同时具有 “a-size-large” 和 “product-title-word-break” 两个类的元素。

### 三种获取文本内容的区别

在 BeautifulSoup 中，`text`、`string` 和 `get_text()` 都用于获取标签内的文本内容，但它们有一些微小的差异。

1. **`text` 属性：**

   - `tag.text` 返回标签内的文本内容。
   - 如果标签包含嵌套标签，`text` 将递归获取所有嵌套标签内的文本，并将它们连接起来。
   - 如果标签内有注释，`text` 也会获取注释内容。

```python
from bs4 import BeautifulSoup
 
html = "<p>This is <b>bold</b> text.</p>"
soup = BeautifulSoup(html, 'html.parser')
p_tag = soup.find('p')
 
print(p_tag.text)
# 输出: This is bold text.
```

2. **`string` 属性：**

   - `tag.string` 用于获取标签内的文本内容。
   - 如果标签包含嵌套标签，`string` 只会获取第一个直接子元素的文本，而不会递归获取所有嵌套标签的文本。
   - 如果标签内有注释，`string` 不会获取注释内容。

```python
from bs4 import BeautifulSoup
 
html = "<p>This is <b>bold</b> text.</p>"
soup = BeautifulSoup(html, 'html.parser')
p_tag = soup.find('p')
 
print(p_tag.string)
# 输出: This is
```

3. **`get_text()` 方法：**

   - `tag.get_text()` 返回标签内的文本内容。
   - 与 `text` 类似，`get_text()` 会递归获取所有嵌套标签内的文本，包括注释。
   - 可以传递参数来定制分隔符。

```python
from bs4 import BeautifulSoup
 
html = "<p>This is <b>bold</b> text.</p>"
soup = BeautifulSoup(html, 'html.parser')
p_tag = soup.find('p')
 
print(p_tag.get_text())
# 输出: This is bold text.
```

总体而言，这三个方法在大多数情况下都可以互相替代，选择使用哪个主要取决于你对文本内容的需求以及是否希望递归获取所有嵌套标签的文本。

### select跟select_one区别

`select_one` 和 `select` 是 BeautifulSoup 提供的两种方法，用于在 HTML 或 XML 文档中选择元素。它们之间的主要区别在于返回的结果集的不同：

1. **`select` 方法：**

   - 返回一个 `ResultSet` 对象，这是一个包含所有匹配元素的列表。
   - 如果没有匹配的元素，返回空列表 `[]`。
   - 适用于选择器表达式匹配多个元素的情况。

   示例：

   ```python
elements = soup.select('div.my-class')  # 返回一个包含所有匹配元素的列表
```

2. **`select_one` 方法：**

   - 返回找到的第一个匹配元素，而不是 `ResultSet`。
   - 如果没有找到匹配的元素，返回 `None`。
   - 适用于只关心第一个匹配元素的情况。

   示例：

   ```python
element = soup.select_one('div.my-class')  # 返回找到的第一个匹配元素
```

使用 `select_one` 的主要优势在于，如果你确定只需要获取第一个匹配的元素，可以避免获取整个 `ResultSet` 对象，提高代码的效率。但如果你关心多个匹配的元素，应该使用 `select` 方法。

总体而言，选择使用哪个方法取决于你的需求。如果你只需要获取一个元素，可以使用 `select_one`；如果你需要多个元素，可以使用 `select`。
