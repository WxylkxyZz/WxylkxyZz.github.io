---
title: "Learn Python"
date: 2023-12-30 16:21:36
categories:
  - "Python"
tags:
  - "Python"
aging: true
aging_days: 30
---

# Python-100-days

## 基础

### 常见内置函数、方法

<!--more-->

- type()：检测变量类型
- int()：将一个数值或字符串转换成整数，可以指定进制。
- float()：将一个字符串转换成浮点数
- str()：将指定的对象转换成字符串形式，可以指定编码
- chr()：将整数转换成该编码对应的字符串（一个字符）
- ord()：将字符串（一个字符）转换成对应的编码（整数）
- len()：计算字符串的长度
- isinstance(object, classinfo)：用于检查一个对象是否是指定的类型或类的实例
- dict.items(): 以列表返回可遍历的(键, 值) 元组数组  
  for key,value in dict.items():#当两个参数时 返回的时元组内的k跟v  
  for i in dict.items():#当参数只有一个时 返回的是元组
- zip(): 用于将多个可迭代对象（例如列表、元组等）中对应的元素打包成元组，然后返回这些元组组成的迭代器。它能够按照最短的输入序列长度进行配对，当其中一个序列到达末尾时，迭代就停止。

### 取模与取余

通常取模运算也叫取余运算，它们返回的结果都是余数。  
对于整数a，b来说，取模运算或者取余运算的方法要分如下两步进行：

- 第一步：求整数商c = a/b
- 第二步：计算模或者余数r = a - (c * b)

> 取模运算和取余运算唯一的差别在于第一步。  
> 取模运算在计算整数商时，采用的是向负无穷大的方向取整。  
> 取余运算在计算整数商时，采用的是向0方向取整。

**举个栗子：**

a = 4 , b = -3  
第一步： 4/(-3) = -1.333…3..3(无限循环)  
对于取模运算，得到的整数商将为-2（向负无穷取整）  
对于取余运算，得到的整数商将为-1（向0取整）  
取模结果为 c = -2  
取余结果为 c = -1  
第二步：  
对于取模运算，r = 4 - ((-2) * (-3)) = 2  
对于取余运算，r = 4 - ((-1) * (-3)) = 1  
故，取模结果为2，取余结果为1。

**总结**

1. 如果a，b符号相同，得到的整数商必然大于0，因此取模运算和取余运算取整的方向相同，因此得到的结果相同。
2. 如果a，b符号不同，得到的整数商必然小于0,取模运算向负无穷方向取整，取余运算向0方向取整，两个方向不同，得到的结果必然不同。并且取模运算得到的结果的符号与b的符号相同，取余运算的结果的符号与a的结果相同。

### 函数的参数

我们给下面两个函数的参数都设定了默认值，这也就意味着如果在调用函数的时候如果没有传入对应参数的值时将使用该参数的默认值  
所以在上面的代码中我们可以用各种不同的方式去调用add函数，这跟其他很多语言中函数重载的效果是一致的

```python
from random import randint
 
 
def roll_dice(n=2):
    """摇色子"""
    total = 0
    for _ in range(n):
        total += randint(1, 6)
    return total
 
 
def add(a=0, b=0, c=0):
    """三个数相加"""
    return a + b + c
 
 
# 如果没有指定参数那么使用默认值摇两颗色子
print(roll_dice())
# 摇三颗色子
print(roll_dice(3))
print(add())
print(add(1))
print(add(1, 2))
print(add(1, 2, 3))
# 传递参数时可以不按照设定的顺序进行传递
print(add(c=50, a=100, b=200))
```

其实上面的add函数还有更好的实现方案，因为我们可能会对0个或多个参数进行加法运算，而具体有多少个参数是由调用者来决定，我们作为函数的设计者对这一点是一无所知的  
因此在不确定参数个数的时候，我们可以使用可变参数，代码如下所示。

```python
# 在参数名前面的*表示args是一个可变参数
def add(*args):
    total = 0
    for val in args:
        total += val
    return total
 
 
# 在调用add函数时可以传入0个或多个参数
print(add())
print(add(1))
print(add(1, 2))
print(add(1, 2, 3))
print(add(1, 3, 5, 7, 9))
```

### 字符串跟常见数据结构

#### 字符串

> Python为字符串类型提供了非常丰富的运算符，  
> 我们可以使用+运算符来实现字符串的拼接，  
> 可以使用*运算符来重复一个字符串的内容，  
> 可以使用in和not in来判断一个字符串是否包含另外一个字符串（成员运算），  
> 我们也可以用[]和[:]运算符从字符串取出某个字符或某些字符（切片运算）

代码如下所示。

```python
s1 = 'hello ' * 3
print(s1) # hello hello hello 
s2 = 'world'
s1 += s2
print(s1) # hello hello hello world
print('ll' in s1) # True
print('good' in s1) # False
str2 = 'abc123456'
# 从字符串中取出指定位置的字符(下标运算)
print(str2[2]) # c
# 字符串切片(从指定的开始索引到指定的结束索引)
print(str2[2:5]) # c12
print(str2[2:]) # c123456
print(str2[2::2]) # c246
print(str2[::2]) # ac246
print(str2[::-1]) # 654321cba
print(str2[-3:-1]) # 45
```

**注意**

1. `print(str2[::-1]) # 654321cba`  
    str2[::-1] 使用了 Python 中的切片（slice）操作来实现字符串的倒序输出。

   解释：  
    str2 是一个字符串。  
    [::] 是切片语法，它用于从一个序列（如字符串）中获取子序列。  
    第一个空位 :: 表示从头到尾，即包含了整个字符串。  
    最后一个数字 1 是步长（stride），它表示 **从左到右** 每次取一个字符。  
    当我们将步长设为 -1 时，就会 **从右到左** 每次取一个字符，因此，str2[::-1] 就实现了将字符串倒序输出的效果。

   举个例子，假如 str2 的值是 “Hello”，那么 str2[::-1] 就会返回 “olleH”，因为它是将 “Hello” 从右到左每次取一个字符。

   **列表切片亦是如此**
2. 索引是左开右闭的

在Python中，我们还可以通过一系列的方法来完成对字符串的处理，代码如下所示。

```python
str1 = 'hello, world!'
# 通过内置函数len计算字符串的长度
print(len(str1)) # 13
# 获得字符串首字母大写的拷贝
print(str1.capitalize()) # Hello, world!
# 获得字符串每个单词首字母大写的拷贝
print(str1.title()) # Hello, World!
# 获得字符串变大写后的拷贝
print(str1.upper()) # HELLO, WORLD!
# 从字符串中查找子串所在位置
print(str1.find('or')) # 8
print(str1.find('shit')) # -1
# 与find类似但找不到子串时会引发异常
# print(str1.index('or'))
# print(str1.index('shit'))
# 检查字符串是否以指定的字符串开头
print(str1.startswith('He')) # False
print(str1.startswith('hel')) # True
# 检查字符串是否以指定的字符串结尾
print(str1.endswith('!')) # True
# 将字符串以指定的宽度居中 在两侧填充指定的字符
print(str1.center(50, '*'))
# 将字符串以指定的宽度靠右放置 在左侧填充指定的字符
print(str1.rjust(50, ' '))
# 将字符串以指定的宽度靠左放置 在右侧填充指定的字符
print(str1.ljust(50, ' '))
str2 = 'abc123456'
# 检查字符串是否由数字构成
print(str2.isdigit())  # False
# 检查字符串是否以字母构成
print(str2.isalpha())  # False
# 检查字符串是否以数字和字母构成
print(str2.isalnum())  # True
str3 = '  jackfrued@126.com '
print(str3)
# 获得字符串修剪左右两侧空格之后的拷贝
print(str3.strip())
 
# rfind()它用于在字符串中从右边开始查找指定子字符串，并返回子字符串的最后一次出现的位置（索引）。
 
```

##### f-string（格式化字符串字面量）

用法如下

```python
name = "Charlie"
age = 35
print(f"My name is {name} and I am {age} years old.")
```

> 在 f-string 中，你可以使用类似于传统的字符串格式化方法来格式化数字。你可以在 f-string 中使用 :{format_spec} 语法，其中 format_spec 是一个格式规范字符串，用于指定如何格式化数字

```python
a = 3.1415
b = -3.1415
print(f'保留两位小数-> {a:.2f}')
print(f'保留两位小数-> {b:.2f}')
# 这里说明数的正负跟下面的例子中的+-没关系
# + 表示显示正负号，无论数字是正数还是负数，都会显示符号 就是不论正负数都带符号 因为正数可以显示不带+号 +1 = 1
print('-'*100)
 
print(f'保留两位小数-> {a:+.2f}')
print(f'保留两位小数-> {b:+.2f}')
print('-'*100)
 
# - 表示对数字进行左对齐 （默认是右对齐）
print(f'保留两位小数-> {a:-.2f}')
print(f'保留两位小数-> {b:-.2f}')
print('-'*100)
 
# 不保留小数
print(f'不保留小数-> {a:.0f}')
print(f'不保留小数-> {b:.0f}')
print('-'*100)
 
# ^, <, > 分别是居中、左对齐、右对齐，后面带宽度， : 号后面带填充的字符，只能是一个字符，不指定则默认是用空格填充。
# 也就是说箭头指哪 本体在哪！
c = 5
print(f'填充字符{c:*<8d}')
print(f'填充字符{c:*>8d}')
print('-'*100)
 
print(f'默认右对齐{c:>10d}')
print(f'改为左对齐{c:<10d}')
print(f'改居中对齐{c:^10d}')
print('-'*100)
 
# 逗号分隔的数字格式
d = 1000000000
print(f'逗号分隔模式 {d:,d}')
print('-'*100)
 
# 百分比格式
e = 0.48
print(f'百分数 {e:.2%}')
print('-'*100)
# 指数数字格式
print(f'指数数字格式 {d:.1e}')
print(f'指数数字格式 {d:.3e}')
print('-'*100)
 
# 进制转换
r = 18
print(f'十进制:-> {r:d}')
print(f'二进制:-> {r:b}')
print(f'八进制:-> {r:o}')
print(f'16进制:-> {r:x}')
# 加上前缀 -> 0x 是一个用来标识后面的数字是十六进制的前缀。
print(f'16进制:-> {r:#x}')
print(f'16进制:-> {r:#X}')
print('-'*100)
 
```

#### 列表

1. enumerate函数

> enumerate() 是 Python 中的一个内置函数，用于将一个可遍历的数据对象（如列表、元组、字符串等）组合为一个索引序列，__同时返回元素和索引__。它的基本语法是：`enumerate(iterable, start=0)`  
> 其中：  
> `iterable` 是可遍历的数据对象，如列表、元组、字符串等。  
> `start` 是可选参数，表示起始的索引值，默认为0。  
> `enumerate()` 返回一个可迭代的对象，每个元素是一个包含索引和对应元素的元组。

```python
list1 = [1, 3, 5, 7, 100]
# 通过enumerate函数处理列表之后再遍历可以同时获得元素索引和值
for index, elem in enumerate(list1):
    print(index, elem)
```

2. 如何向列表中添加元素以及如何从列表中移除元素。

   ```python
list1 = [1, 3, 5, 7, 100]
# 添加元素
list1.append(200)
list1.insert(1, 400)
 
# 合并两个列表
# list1.extend([1000, 2000])
list1 += [1000, 2000]
print(list1) # [1, 400, 3, 5, 7, 100, 200, 1000, 2000]
print(len(list1)) # 9
 
# 先通过成员运算判断元素是否在列表中，如果存在就删除该元素
if 3 in list1:
    list1.remove(3)
if 1234 in list1:
    list1.remove(1234)
print(list1) # [1, 400, 5, 7, 100, 200, 1000, 2000]
 
# 从指定的位置删除元素
list1.pop(0)
list1.pop(len(list1) - 1)
print(list1) # [400, 5, 7, 100, 200, 1000]
 
# 清空列表元素
list1.clear()
print(list1) # []
```

3. 列表排序操作

```python
list1 = ['orange', 'apple', 'zoo', 'internationalization', 'blueberry']
list2 = sorted(list1)
# sorted函数返回列表排序后的拷贝 并不会修改传入的列表！
# 函数的设计就应该像sorted函数一样尽可能不产生副作用
list3 = sorted(list1, reverse=True)
# 通过key关键字参数指定 根据字符串长度进行排序 而不是默认的字母表顺序
list4 = sorted(list1, key=len)
print(list1)
print(list2)
print(list3)
print(list4)
# 给列表对象发出排序消息 直接在列表对象上进行排序
list1.sort(reverse=True)
print(list1)
```

#### 列表生成式和 生成器

```python
f = [x for x in range(1, 10)]
print(f)
f = [x + y for x in 'ABCDE' for y in '1234567']
print(f)
# 用列表的生成表达式语法创建列表容器
# 用这种语法创建列表之后元素已经准备就绪所以需要耗费较多的内存空间
f = [x ** 2 for x in range(1, 1000)]
print(sys.getsizeof(f))  # 查看对象占用内存的字节数
print(f)
 
# 请注意下面的代码创建的不是一个列表而是一个 生成器对象
# 通过生成器可以获取到数据但它不占用额外的空间存储数据
# 每次需要数据的时候就通过内部的运算得到数据(需要花费额外的时间)
f = (x ** 2 for x in range(1, 1000))
print(sys.getsizeof(f))  # 相比生成式生成器不占用存储数据的空间
print(f) # <generator object <genexpr> at 0x000002637460FED0> 生成了一个对象
for val in f:
    print(val)
```

**注意** :

`[x for x in range(1, 11) if x % 2 == 0]` -> `[2, 4, 6, 8, 10]`

`[x if x % 2 == 0 for x in range(1, 11)]` -> `SyntaxError: invalid syntax`

第一句之所以可以成功输出 是因为跟在`for`后面的`if`是一个筛选条件，不能带`else`，不然如何筛选啊？🤬如果你把`if`写在`for`前， 那必然会报错， 因为写在前面就是个表达式了！必须带`else`,它必须根据`x`计算出一个结果。

可见，在一个列表生成式中，`for`前面的`if ... else`是表达式，而`for`后面的`if`是过滤条件，不能带`else`。

还有一点:  
两次for大的在前面奥  
eg:  
`values = [cell.value for row in scope for cell in row]`

还有+

在切片的表示中，[start:stop] 表示从索引 `start` 开始，一直到索引 `stop` 之前的位置,也就是左闭右开.  
还有切片是从左往右切 所以如果负数的话这样写 `[-2, -1]`  
还有一点在切片中，如果不指定 `stop` ，则默认会一直取到列表的末尾（这里注意是结尾 包含了最后一个值）

#### 元组

元组跟列表的不同之处是 **元组的元素不能修改**

```python
# 定义元组
t = ('骆昊', 38, True, '四川成都')
print(t)
# 获取元组中的元素
print(t[0])
print(t[3])
# 遍历元组中的值
for member in t:
    print(member)
# 重新给元组赋值
# t[0] = '王大锤'  # TypeError!!!!
 
# 变量t重新引用了新的元组原来的元组将被垃圾回收
t = ('王大锤', 20, True, '云南昆明')
print(t)
 
# 将元组转换成列表
person = list(t)
print(person)
# 列表是可以修改它的元素的
person[0] = '李小龙'
person[1] = 25
print(person)
# 将列表转换成元组
fruits_list = ['apple', 'banana', 'orange']
fruits_tuple = tuple(fruits_list)
print(fruits_tuple)
```

> 如果可能，能用tuple代替list就尽量用tuple

**坑**

```python
tuple_1 = (1)
print(tuple_1, type(tuple_1))
 
tuple_2 = (1,)
print(tuple_2, type(tuple_2))
 
# print
1 <class 'int'>
(1,) <class 'tuple'>
```

> 这是因为括号()既可以表示tuple，又可以表示数学公式中的小括号，这就产生了歧义，因此，Python规定，这种情况下，按小括号进行计算，计算结果自然是1

#### 集合

> Python中的集合跟数学上的集合是一致的，不允许有重复元素，而且可以进行交集、并集、差集等运算。

```python
# 1. 创建和使用集合
 
# 创建集合的字面量语法
set1 = {1, 2, 3, 3, 3, 2}
print(set1)
print('Length =', len(set1))
# 创建集合的构造器语法(面向对象部分会进行详细讲解)
set2 = set(range(1, 10))
set3 = set((1, 2, 3, 3, 2, 1))
print(set2, set3)
# 创建集合的推导式语法(推导式也可以用于推导集合)
set4 = {num for num in range(1, 100) if num % 3 == 0 or num % 5 == 0}
print(set4)
 
# 2. 向集合添加元素和从集合删除元素。
 
set1.add(4)
set1.add(5)
set2.update([11, 12])
set2.discard(5)
if 4 in set2:
    set2.remove(4)
print(set1, set2)
print(set3.pop())
print(set3)
 
# 3. 集合的成员、交集、并集、差集等运算。
# 集合的交集、并集、差集、对称差运算
print(set1 & set2)
# print(set1.intersection(set2))
print(set1 | set2)
# print(set1.union(set2))
print(set1 - set2)
# print(set1.difference(set2))
print(set1 ^ set2)
# print(set1.symmetric_difference(set2))
# 判断子集和超集
print(set2 <= set1)
# print(set2.issubset(set1))
print(set3 <= set1)
# print(set3.issubset(set1))
print(set1 >= set2)
# print(set1.issuperset(set2))
print(set1 >= set3)
# print(set1.issuperset(set3))
 
```

#### 字典

> 字典是另一种可变容器模型，Python中的字典跟我们生活中使用的字典是一样一样的，它可以存储任意类型对象，与列表、集合不同的是，字典的每个元素都是由一个键和一个值组成的“键值对”，键和值通过冒号分开。下面的代码演示了如何定义和使用字典。

```python
# 创建字典的字面量语法
scores = {'骆昊': 95, '白元芳': 78, '狄仁杰': 82}
print(scores)
# 创建字典的构造器语法
items1 = dict(one=1, two=2, three=3, four=4)
# 通过zip函数将两个序列压成字典
items2 = dict(zip(['a', 'b', 'c'], '123'))
# 创建字典的推导式语法
items3 = {num: num ** 2 for num in range(1, 10)}
print(items1, items2, items3)
# 通过键可以获取字典中对应的值
print(scores['骆昊'])
print(scores['狄仁杰'])
# 对字典中所有键值对进行遍历
for key in scores:
    print(f'{key}: {scores[key]}')
# 更新字典中的元素
scores['白元芳'] = 65
scores['诸葛王朗'] = 71
scores.update(冷面=67, 方启鹤=85)
print(scores)
if '武则天' in scores:
    print(scores['武则天'])
print(scores.get('武则天'))
# get方法也是通过键获取对应的值但是可以设置默认值
print(scores.get('武则天', 60))
# 删除字典中的元素
print(scores.popitem())
# popitem() 会弹出字典中的一个任意键值对（在 Python 3.7+ 中会弹出最后一个插入的键值对），并将该键值对以元组形式返回，然后将该键值对从字典中移除。
print(scores.popitem())
print(scores.pop('骆昊', 100))
# 清空字典
scores.clear()
print(scores)
```

### 文件读写

简单直接看[菜鸟教程](https://www.runoob.com/python/file-methods.html)即可  
主要就是  
\+ : 打开一个文件进行 **更新** (可读可写,比如r是只读,r+时读写咯)  
a : 追加 注意只有`a`是追加 `w跟r系列`文件指针都会放在开头，本来有内容会被覆盖  
x : 也是写入，但是不一样的是如果文件已经存在会产生异常  
![文件读写]()

**读写JSON文件** 一般只用几个

- `dump` : 将Python对象按照JSON格式序列化到文件中
- `dumps`: 将Python对象处理成JSON格式的字符串
- `load` : 将文件中的JSON数据反序列化成对象
- `loads`: 将字符串的内容反序列化成Python对象

> 这里出现了两个概念，一个叫序列化，一个叫反序列化。自由的百科全书维基百科上对这两个概念是这样解释的：“序列化（serialization）在计算机科学的数据处理中，是指将数据结构或对象状态转换为可以存储或传输的形式，这样在需要的时候能够恢复到原先的状态，而且通过序列化的数据重新获取字节时，可以利用这些字节来产生原始对象的副本（拷贝）。与这个过程相反的动作，即从一系列字节中提取数据结构的操作，就是反序列化（deserialization）”。

### random模块

| 函数 | 描述 |
| --- | --- |
| `random()` | 返回范围在 [0.0, 1.0) 之间的随机浮点数。 |
| `randint(a, b)` | 返回一个整数 N，使得 `a <= N <= b`。 |
| `uniform(a, b)` | 返回范围在 [a, b) 之间的随机浮点数 区间可以不是整数 |
| `randrange(start, stop, step)` | 从指定范围内随机选择一个元素。 |
| `choice(seq)` | 从非空序列中随机选择一个元素。 |
| `shuffle(seq)` | 原地随机打乱序列的元素顺序。 |
| `sample(population, k)` | 从总体中选择 k 个唯一元素，并返回一个列表。 |
| `seed([x])` | 使用给定的种子值初始化随机数生成器。 |

这些函数是 Python 中 `random` 模块的一部分，通常用于生成随机数或以随机方式操作序列。

### match case

```python
age = 15
 
match age:
    case age if age < 10:
        print(f'< 10 years old: {age}')
    case 10:
        print('10 years old')
    case 11 | 12 | 13 | 14 | 15 | 16:
        print('11~16 years old')
    case 17:
        print('17 years old')
    case _:
        print('not sure')
```

[菜鸟教程](https://www.runoob.com/python3/python-match-case.html)  
能用`match case`尽量不用`if elif else`

## 面向对象编程

定义: 类是对象的蓝图和模板，而对象是类的实例

### 定义类

```python
class Student:
    # __init__是一个特殊方法用于在创建对象时进行初始化操作
    # 通过这个方法我们可以为学生对象绑定name和age两个属性
    def __init__(self, name, age):
        self.name = name
        self.age = age
 
    # 在类中的函数，我们通常称之为（对象的）方法，这些方法就是对象可以接收的消息。
 
    def study(self, course_name):
        print(f'{self.name}正在学习{course_name}')
 
 
def main():
    # 创建学生对象并指定姓名和年龄
    stu1 = Student('小明', 38)
    # 给对象发study消息
    stu1.study('Python程序设计')
 
 
if __name__ == '__main__':
    main()
```

### 访问权限

> 对于上面的代码，有C++、Java、C#等编程经验的程序员可能会问，我们给Student对象绑定的name和age属性到底具有怎样的访问权限（也称为可见性）。因为在很多面向对象编程语言中，我们通常会将对象的属性设置为私有的（private）或受保护的（protected），简单的说就是不允许外界访问，而对象的方法通常都是公开的（public），因为公开的方法就是对象能够接受的消息。在Python中，属性和方法的访问权限只有两种，也就是公开的和私有的，如果希望属性是私有的，在给属性命名时可以用两个下划线作为开头，下面的代码可以验证这一点。

```python
class Test:
    def __init__(self, foo):
        self.__foo = foo
 
    def __bar(self):
        print(self.__foo)
        print('__bar')
 
 
def main():
    test = Test('Hello')
    test.__bar()
    # 这句报错 : AttributeError: 'Test' object has no attribute '__bar'
    print(test.__foo)
    # 这句报错: AttributeError: 'Test' object has no attribute '__foo'
 
 
if __name__ == '__main__':
    main()
 
```

> 但是，Python并没有从语法上严格保证私有属性或方法的私密性，它只是给私有的属性和方法换了一个名字来妨碍对它们的访问，事实上如果你知道更换名字的规则仍然可以访问到它们，下面的代码就可以验证这一点。之所以这样设定，可以用这样一句名言加以解释，就是”We are all consenting adults here”。因为绝大多数程序员都认为开放比封闭要好，而且程序员要自己为自己的行为负责。  
> 在实际开发中，我们并不建议将属性设置为私有的，因为这会导致子类无法访问（后面会讲到）。所以大多数Python程序员会遵循一种命名惯例就是让属性名以单下划线开头来表示属性是受保护的，本类之外的代码在访问这样的属性时应该要保持慎重。这种做法并不是语法上的规则，单下划线开头的属性和方法外界仍然是可以访问的，所以更多的时候它是一种暗示或隐喻

```python
class Test:
    def __init__(self, foo):
        self.__foo = foo
 
    def __bar(self):
        print(self.__foo)
        print('__bar')
 
 
def main():
    test = Test('hello')
    test._Test__bar()
    print(test._Test__foo)
    '''打印结果
    hello
    __bar
    hello
    '''
 
if __name__ == '__main__':
    main()
```

### @property装饰器

> 之前我们讨论过Python中属性和方法访问权限的问题，虽然我们不建议将属性设置为私有的，但是如果直接将属性暴露给外界也是有问题的，比如我们没有办法检查赋给属性的值是否有效。我们之前的建议是将属性命名以单下划线开头，通过这种方式来暗示属性是受保护的，不建议外界直接访问，那么如果想访问属性可以通过属性的getter（访问器）和setter（修改器）方法进行对应的操作。如果要做到这点，就可以考虑使用@property包装器来包装getter和setter方法，使得对属性的访问既安全又方便，代码如下所示。

```python
class Person:
    def __init__(self, name, age):
        self._name = name
        self._age = age
 
    # 访问器 getter方法
    @property
    def name(self):
        return self._name
 
    @property
    def age(self):
        return self._age
 
    @age.setter
    def age(self, age):
        self._age = age
 
    def play(self):
        if self._age <= 16:
            print('%s正在玩飞行棋.' % self._name)
        else:
            print('%s正在玩斗地主.' % self._name)
 
 
def main():
    person = Person('王大锤', 12)
    person.play()
    person.age = 22
    person.name = '小明'
    print(person.name) # AttributeError: property 'name' of 'Person' object has no setter
    person.play()
 
 
if __name__ == '__main__':
    main()
```

以上内容属实没看懂什么意思 以下为理解内容

`property` 装饰器是Python中用于管理类属性的一种特殊方法。它允许你 **将一个方法标记为属性** ，从而可以 **像访问普通属性一样访问该方法** 。

通常情况下，我们定义一个类的属性时，会使用类的实例变量来存储数据。然而，有时我们可能需要在获取或者设置属性值时 __执行一些额外的逻辑__，例如进行数据校验或转换。

`property` 装饰器允许你在访问属性时 **自定义其行为。** 它 **实际上将一个方法“伪装”成一个属性，当你访问这个属性时，实际上会调用相应的方法。**

举例来说:

假设你有一个类，表示一个人的信息，其中包括了名字和姓氏：

```python
class Person:
    def __init__(self, first_name, last_name):
        self.first_name = first_name
        self.last_name = last_name
```

现在，你想要获取这个人的全名，你可以使用下面的方法：

```python
person = Person('John', 'Doe')
full_name = f'{person.first_name} {person.last_name}'
```

但是如果你想让获取全名的方式更简单一些，就可以使用property装饰器。它让你可以像访问属性一样获取全名，而不是调用一个方法：

```python
class Person:
    def __init__(self, first_name, last_name):
        self.first_name = first_name
        self.last_name = last_name
 
    @property
    def full_name(self):
        return f'{self.first_name} {self.last_name}'
```

现在你可以这样获取全名：

```python
person = Person('John', 'Doe')
full_name = person.full_name
```

**这就像在获取一个普通的属性一样，但实际上在后台调用了一个方法。**

另外，如果你想要设置全名，你可以使用`@full_name.setter`装饰器来定义一个方法，使得你可以直接给`full_name`赋值：

```python
class Person:
    def __init__(self, first_name, last_name):
        self.first_name = first_name
        self.last_name = last_name
 
    @property
    def full_name(self):
        return f'{self.first_name} {self.last_name}'
 
    @full_name.setter
    def full_name(self, value):
        first, last = value.split()
        self.first_name = first
        self.last_name = last
```

现在你可以这样设置全名：

```python
person = Person('John', 'Doe')
person.full_name = 'Jane Doe'
```

### slots魔法

`__slots__` 是Python中一个特殊的类属性，它允许你限制一个类实例能拥有的属性。

通常情况下，Python的类实例可以动态地添加新的属性。这意味着你可以在实例化之后随时为其添加新的属性。例如：

```python
class Person:
    pass
 
person = Person()
person.name = 'John'
person.age = 30
```

在上面的例子中，我们创建了一个`Person`类的实例，并随时为其添加了`name`和`age`两个属性。

但是，有时候我们希望限制一个类实例可以拥有的属性，这时就可以使用`__slots__`。

以下是一个使用`__slots__`的例子：

```python
class Person:
    __slots__ = ('name', 'age')
 
    def __init__(self, name, age):
        self.name = name
        self.age = age
 
person = Person('John', 30)
person.name = 'Jane'  # 可以正常设置属性
person.city = 'New York'  # 会抛出 AttributeError，因为'city'不在__slots__中
```

在这个例子中，`__slots__` 被设置为一个包含了`'name'`和`'age'`的元组。这意味着`Person`类的实例只能拥有这两个属性，任何尝试添加其他属性都会引发`AttributeError`。

使用`__slots__`的好处是可以减少实例的内存消耗，因为Python不再需要为每个实例维护一个字典来存储属性。相反，它直接为属性分配一个固定的槽位，提高了访问速度。

需要注意的是，`__slots__` 只对当前类的实例起作用，不会影响子类。如果子类也定义了`__slots__`，那么它会覆盖父类的定义。

综上就是  
你初始化属性时 如果不想让人再访问某些属性 可以用`self.__name`定义属性 这样在访问时直接报错没这个属性 (但是也可以用特殊手段访问,比如用`_Person__name` 在该属性前加上下划线以及类的名称)

一般情况不建议将属性设置为私有的，但是如果直接将属性暴露给外界也是有问题的，比如我们没有办法检查赋给属性的值是否有效， 就比如让你定义`name`你直接`person.name = 18` 你叫18是吧🤬

建议是将属性命名以单下划线开头，通过这种方式来 __暗示属性是受保护的__，不建议外界直接访问，那么如果想访问属性可以通过属性的getter（访问器）和setter（修改器）方法进行对应的操作。

所以我们需要在自定义属性时 __执行一些额外的逻辑__，例如进行数据校验或转换时，就用`property`

> `property` 装饰器允许你在访问属性时 **自定义其行为。** 它 **实际上将一个方法“伪装”成一个属性，当你访问这个属性时，实际上会调用相应的方法。**

需要说的一点是`@property`会将一个方法转换为一个只读属性，相当于定义了一个`getter`方法  
在修改内容时用修改器也就是`setter`方法,比如有人在修改`name`时, 你可以做一些`手脚`,还有就是setter包装的函数不需要 `return`!  
`__slots__` 就是限制一个类实例可以拥有的属性，他是一个 **元组** 类型!

ps: 如果想在初始化时就进行检查，可以在__init__方法中添加相应的检查逻辑  
以下代码演示:

```python
class Person:
    __slots__ = ('_name', 'age')
 
    def __init__(self, name, age):
        self._name = name
        self.age = age
     
    @property
    def name(self):
        return self._name
 
    @name.setter
    def name(self, name):
        if isinstance(name, str):
            self._name = name
        else:
            self._name = '请输入正确名字' + '*' * 100
 
 
def main():
    person = Person('小李', '18')
    print(person.name)
    person.name = int('54345')
    print(person.name)  # 请输入正确名字*********************************************
 
    person.age = 88  # 可以正常设置属性
    person.hobby = '玩游戏'  # AttributeError: 'Person' object has no attribute 'hobby'
 
 
if __name__ == '__main__':
    main()
```

### 静态方法和类方法

静态方法比较容易理解如下

**静态方法**：

- 静态方法就像一个独立的工具，它并不依赖于特定的实例或类属性。你可以把它想象成 **一个独立的函数，只是碰巧它在类的内部定义了而已**
- 举个例子，如果我们有一个类代表一个工具箱，其中有一个静态方法叫做`hammer()`，那么你可以 **直接调用这个方法，不需要先实例化一个工具箱**

```python
from math import sqrt
 
 
class Triangle:
    def __init__(self, a, b, c):
        self._a = a
        self._b = b
        self._c = c
 
    @staticmethod
    def is_valid(a, b, c):
        return a + b > c and a + c > b and b + c > a
 
    def perimeter(self):
        return self._a + self._b + self._c
 
    def area(self):
        half = self.perimeter() / 2
        return sqrt(half * (half - self._a) *
                    (half - self._b) * (half - self._c))
 
 
def main():
    a, b, c = 3, 4, 5
    if Triangle.is_valid(a, b, c):
        t = Triangle(a, b, c)
        print(t.area())
        print(t.perimeter())
    else:
        print("无法构成三角形")
 
 
if __name__ == '__main__':
    main()
 
```

**注意:**

要在类的实例方法中调用类的静态方法，你可以使用 `self` 来访问类的成员，包括静态方法。在这种情况下，`self` 实际上是指向类的实例本身。  
虽然静态方法与类的实例无关，但它们属于类的命名空间。通过 `self`，你可以从实例的命名空间中访问静态方法。这是一种在实例方法中访问类级别成员（包括静态方法）的通用方式。

类方法有点难度 :

> 类方法的第一个参数约定名为cls，它代表的是当前 **类相关的信息** 的对象（类本身也是一个对象，有的地方也称之为类的元数据对象），通过这个参数我们可以 **获取和类相关的信息并且可以创建出类的对象**

最后一句

1. 获取和类相关的信息
2. 并且可以创建出类的对象！

**类方法**：

- 类方法就像一个能够访问工具箱本身的工具，但它并不依赖于具体的工具箱实例。你可以把它想象成一个能够处理工具箱的工具，但它并不关心具体是哪一个工具箱。
- 举个例子，如果我们有一个类代表所有工具箱，其中有一个类方法叫做`count_tools()`，它可以统计所有工具箱中的工具数量，而不需要先实例化一个具体的工具箱。

***所以，静态方法和类方法的区别在于它们处理的数据和上下文不同。静态方法更像一个独立的工具函数，而类方法更像是能够操作整个类的工具***

```python
from time import time, localtime, sleep
 
 
class Clock:
 
    def __init__(self, hour=0, minute=0, second=0):
        self._hour = hour
        self._minute = minute
        self._second = second
 
    @classmethod
    def new(cls):
        ctime = localtime(time())
        return cls(ctime.tm_hour, ctime.tm_min, ctime.tm_sec)
 
    def run(self):
        self._second += 1
        if self._second == 60:
            self._second = 0
            self._minute += 1
            if self._minute == 60:
                self._minute = 0
                self._hour += 1
                if self._hour == 24:
                    self._hour = 0
 
    def show(self):
        return f'当前时间：{self._hour:0>2d}-{self._minute:0>2d}-{self._second:0>2d}'
 
 
def main():
    clock = Clock.new()
    while True:
        print(clock.show())
        clock.run()
        sleep(1)
        os.system('cls')
 
 
if __name__ == '__main__':
    main()
```

### 继承

> 刚才我们提到了，可以在已有类的基础上创建新类，这其中的一种做法就是让一个类从另一个类那里将属性和方法直接继承下来，从而减少重复代码的编写。提供继承信息的我们称之为父类，也叫超类或基类；得到继承信息的我们称之为子类，也叫派生类或衍生类。子类除了继承父类提供的属性和方法，还可以定义自己特有的属性和方法，所以子类比父类拥有的更多的能力，在实际开发中，我们经常会用子类对象去替换掉一个父类对象，这是面向对象编程中一个常见的行为，对应的原则称之为里氏替换原则。下面我们先看一个继承的例子。

关于 `super().__init__()` 可以参考[这篇文章](https://blog.csdn.net/a__int__/article/details/104600972)

```python
class Person(object):
    """人"""
 
    def __init__(self, name, age):
        self._name = name
        self._age = int(age)
 
    @property
    def name(self):
        return self._name
 
    @property
    def age(self):
        return self._age
 
    @age.setter
    def age(self, age):
        self._age = age
 
    def play(self):
        print('%s正在愉快的玩耍.' % self._name)
 
    def watch_av(self):
        if self._age >= 18:
            print('%s正在观看爱情动作片.' % self._name)
        else:
            print('%s只能观看《熊出没》.' % self._name)
 
 
class Student(Person):
    def __init__(self, name, age, grade):
        super().__init__(name, age)
        self._grade = grade
 
    @property
    def grade(self):
        return self._grade
 
    @grade.setter
    def grade(self, grade):
        self._grade = grade
 
    def study(self, course):
        return f'{self._grade} 的 {self._name} 正在学习 {course}'
 
 
if __name__ == '__main__':
    stu = Student('小明', '19', '高二')
    print(stu.study('数学'))
    stu.watch_av()
```

### 多态

> 子类在继承了父类的方法后，可以对父类已有的方法给出新的实现版本，这个动作称之为方法重写（override）。通过方法重写我们可以让父类的同一个行为在子类中拥有不同的实现版本，当我们调用这个经过子类重写的方法时，不同的子类对象会表现出不同的行为，这个就是多态（poly-morphism）。

```python
from abc import ABC, abstractmethod
 
 
class Pet(ABC):
    """宠物"""
 
    def __init__(self, nickname):
        self._nickname = nickname
 
    @abstractmethod
    def make_voice(self):
        """发出声音"""
        pass
 
 
class Dog(Pet):
    """狗"""
 
    def make_voice(self):
        print('%s: 汪汪汪...' % self._nickname)
 
 
class Cat(Pet):
    """猫"""
 
    def make_voice(self):
        print('%s: 喵...喵...' % self._nickname)
 
 
def main():
    pets = [Dog('旺财'), Cat('凯蒂'), Dog('大黄')]
    for pet in pets:
        pet.make_voice()
 
 
if __name__ == '__main__':
    main()
```

> 在上面的代码中，我们将Pet类处理成了一个抽象类，所谓抽象类就是不能够创建对象的类，__这种类的存在就是专门为了让其他类去继承它__。Python从语法层面并没有像Java或C#那样提供对抽象类的支持，但是我们可以通过abc模块的ABCMeta元类和abstractmethod包装器来达到抽象类的效果，如果一个类中存在抽象方法那么这个类就不能够实例化（创建对象）。上面的代码中，Dog和Cat两个子类分别对Pet类中的make_voice抽象方法进行了重写并给出了不同的实现版本，当我们在main函数中调用该方法时，这个方法就表现出了多态行为（同样的方法做了不同的事情）。

我对**抽象方法**的理解

抽象方法就像是一个接口规定了一些要做的事情，但它本身并不知道如何去实现这些事情。抽象方法就好比是一份合同，规定了某个类必须提供某个方法，但不规定具体的实现。

举个例子，想象你是一名游戏开发者，你设计了一个游戏中的角色类，每个角色都必须有一个叫做 `攻击` 的方法。但是，你不知道具体每个角色如何实现攻击，因为不同角色可能有不同的攻击方式。

这时，你可以使用抽象方法来定义这个攻击方法，但不提供具体的实现。每个具体的角色（比如战士、法师、弓手等）都必须按照合同，提供自己独特的 `攻击` 实现。

在这个场景下，抽象方法就是合同中的规定，而具体的角色类就是按照这个规定提供实现。这样确保了所有的角色都有攻击方法，但实现方式可以因角色而异。

上面的代码中,无论是猫还是狗都一个共同的方法 就是`make_voice` 但是每个动物类对`make_voice`的实现方式又不同，猫有猫的叫法狗有狗的叫法，但是都会叫，这就是多态！ 用相同的方法处理不同类型的动物  
`Pet`是一个抽象类, 其中包含一个抽象方法`make_voice`, 猫狗都是pet，都继承了动物类，是动物的子类，都强制实现了`make_voice`方法， 抽象方法强制子类提供自己的实现，确保了在众多子类中都具有一致的接口，并且如果子类没有提供抽象方法的实现，或者在实例化时调用了抽象方法，将引发`TypeError`。

## 并发编程

关于并发编程建议好好读读 [《Python Parallel Programming Cookbook》](https://python-parallel-programmning-cookbook.readthedocs.io/zh-cn/latest/chapter1/index.html)

### 多线程

#### 实现方式

多线程实现方式有两种，我喜欢用自定义线程类

切记 要理解:

- t.start() : 当前线程准备就绪（等待CPU调度，具体时间是由CPU来决定）
- t.join() ：等待当前线程的任务 **执行完毕** 后再向下继续执行
- t.setDaemon ：守护线程（必须放在start之前）
  - t.setDaemon(True)，设置为守护线程，主线程执行完毕后，子线程也自动关闭。
  - t.setDaemon(False)，设置为非守护线程，主线程等待子线程，子线程执行完毕后，主线程才结束。（默认）

```python
from threading import Thread
 
 
class Download(Thread):
    def __init__(self, filename):
        self._filename = filename
        super().__init__()
 
    def run(self):
        print(f'开始下载 {self._filename}')
        time_to_download = random.randint(5, 10)
        time.sleep(time_to_download)
        print(f'下载完成 {self._filename}, 花费了{time_to_download}s')
 
 
def main():
    start = time.time()
    t1 = Download('鹿鼎记')
    t1.start()
 
    t2 = Download('红楼梦')
    t2.start()
 
    t1.join()
    t2.join()
    end = time.time()
    print(f'共花费了 {end - start:.2f}s')
 
 
if __name__ == '__main__':
    main()
```

#### 线程锁

Lock：同步锁  
RLock：递归锁

在多线程编程中，`Lock`和`RLock`（可重入锁）是用于控制多个线程对共享资源访问的工具。这两者都是互斥锁的变种，用于确保在任何给定时间内只有一个线程可以访问共享资源，以防止数据竞争和不一致的状态。

##### Lock

1. **作用：** 用于在任一时刻只允许一个线程访问共享资源。
2. **特性：** 一旦一个线程获取了`Lock`，其他线程就必须等待该线程释放锁，才能继续执行。
3. **例子：** 如果一个线程获得了某个资源的`Lock`，那么其他线程就不能获得同一个`Lock`，直到这个线程释放了`Lock`。

```python
import threading
 
# 创建一个Lock
lock = threading.Lock()
 
def example_function():
    with lock:
        # 临界区代码，只有一个线程能够执行这里的代码
        pass
```

##### RLock

1. **作用：** 允许同一个线程多次获得同一个锁，避免了死锁情况。
2. **特性：** 当一个线程获得了`RLock`后，它可以多次调用`acquire`，而不会阻塞，但必须相应地调用相同次数的`release`才能释放锁。
3. **例子：** 在递归函数或嵌套调用中，`RLock`可以确保同一线程能够多次获取锁，而不会引起死锁。

```python
import threading
 
# 创建一个RLock
rlock = threading.RLock()
 
def example_recursive_function():
    with rlock:
        # 可重入锁允许同一线程多次获取锁
        # 可以在嵌套调用中使用
        with rlock:
            pass
```

总体而言，`Lock`和`RLock`都是为了保护共享资源而存在的，通过防止多个线程同时访问临界区，确保线程安全。`RLock`相对于`Lock`的特殊之处在于它允许同一个线程多次获取锁，这在某些复杂的程序结构中可能很有用。

##### 死锁

死锁是多线程或多进程编程中常见的问题之一，它发生在两个或多个线程（或进程）互相等待对方释放资源时。简而言之，死锁是一种资源争用的情况，其中线程（或进程）之间相互等待对方释放资源，导致它们都无法继续执行。

以下是引起死锁的一般情况：

1. **互斥条件：** 资源被设计成一次只能被一个线程或进程占用，这就是互斥条件的基础。如果一个线程获得了一个资源，其他线程必须等待。
2. **占有且等待：** 一个线程在持有某个资源的同时，又请求获取其他资源，并且不释放已经占有的资源。如果所有线程都采用这种策略，就可能导致死锁。
3. **无抢占：** 已经获得资源的线程不能被强制释放资源，只能在自愿的情况下释放。如果一个线程在等待其他资源时不释放已经占有的资源，其他线程可能就无法继续执行，形成死锁。
4. **循环等待：** 一组线程形成一个循环等待链，每个线程都在等待下一个线程持有的资源，最终导致整个系统陷入死锁。

一个简单的例子可以说明死锁的发生。假设有两个线程 A 和 B，以如下方式请求资源：

- A获得资源1，等待资源2
- B获得资源2，等待资源1

如果A和B同时开始执行，并且A先获得资源1，B先获得资源2，然后它们会陷入等待对方释放资源的状态，而无法继续执行。这就是一个简单的死锁场景。

避免死锁的一般方法包括按照一定的顺序获取资源、使用超时机制、以及使用避免死锁的算法等。理解死锁的原因和常见模式是预防和解决死锁问题的关键。

### 线程池

> 事实上，线程的创建和释放都会带来较大的开销，频繁的创建和释放线程通常都不是很好的选择。利用线程池，可以提前准备好若干个线程，在使用的过程中不需要再通过自定义的代码创建和释放线程，而是直接复用线程池中的线程。Python 内置的concurrent.futures模块提供了对线程池的支持，代码如下所示。

线程池是一种并发编程的技术，它可以有效地管理和复用线程，提高程序的性能和效率。在Python中，线程池通常使用`concurrent.futures`模块来实现。以下是线程池的一些基本概念和细节：

1. **线程池的创建：**  
   在Python中，你可以使用`concurrent.futures.ThreadPoolExecutor`来创建线程池。以下是一个简单的创建线程池的例子：

   ```python
from concurrent.futures import ThreadPoolExecutor
 
# 创建一个拥有4个线程的线程池
with ThreadPoolExecutor(max_workers=4) as executor:
    # 在这里执行线程池中的任务
    # ...
```

2. **任务提交：**

   future 模式，更加强大 submit每次传的是一个！！！ 所以需要可以时用时加 不用像map一样 一次放完，结果有两种遍历方式看代码 注意如果用as_completed顺序是不定的

   你可以通过`submit`方法将任务提交给线程池。`submit`方法返回一个`Future`对象，你可以用来跟踪任务的状态和获取结果。

   ```python
with ThreadPoolExecutor(max_workers=4) as executor:
    future = executor.submit(my_function, arg1, arg2)
    # 在线程池中提交一个任务，线程池中如果有空闲线程，则分配一个线程去执行，执行完毕后再将线程交还给线程池；如果没有空闲线程，则等待。可以通过 future.result() 获取任务的结果
```

   批量提交

   ```python
with ThreadPoolExecutor() as pool:
    futures = [ pool.submit(craw, url) for url in urls ]
 
for future in futures: # 第一种 按照url的顺序 依次获取future future.result() 
    print(future.result())
for future in as_completed(futures): # 第二种 不管哪个任务 先进行完了 就会先进行返回
    print(future.result())
 
```

3. **批量提交任务：**  
   你还可以使用`map`方法一次性提交多个任务，类似于`map`函数, 注意map的结果和入参是顺序对应的

   ```python
with ThreadPoolExecutor(max_workers=4) as executor:
    results = executor.map(my_function, [arg1, arg2, arg3])
    # results 是一个迭代器，包含每个任务的结果
```

4. **Future对象：**  
   `Future`对象代表一个异步操作的结果。你可以通过它来检查任务是否完成、获取结果、取消任务等。

   ```python
with ThreadPoolExecutor(max_workers=4) as executor:
    future = executor.submit(my_function, arg1, arg2)
    # 检查任务是否完成
    if future.done():
        result = future.result()
```

5. **异常处理：**  
   通过`add_done_callback`方法，你可以指定一个回调函数，在任务完成时执行。这可以用于处理任务中的异常。

   ```python
def callback(future):
    try:
        result = future.result()
    except Exception as e:
        print(f"Exception: {e}")
 
with ThreadPoolExecutor(max_workers=4) as executor:
    future = executor.submit(my_function, arg1, arg2)
    future.add_done_callback(callback)
```

   **`callback` 函数接受的 `future` 参数是通过 `add_done_callback` 方法自动传递的。这个方法会在异步任务完成时被调用，将 `concurrent.futures.Future` 对象作为参数传递给注册的回调函数。所以，`callback` 函数的参数 `future` 实际上是对应异步任务的 `Future` 对象，它包含了异步任务的状态和结果信息。通过 `future.result()`，可以获取异步任务的执行结果，即下载的响应对象，进行后续的处理。**
6. **回调函数：**

   当你使用 `future.add_done_callback(callback)` 时，你告诉程序：“当这个 `future` 完成时，请调用 `callback` 函数。”

   然而，有时候你的 `callback` 函数可能需要除了 `future` 之外的额外信息或参数。这时，你可以通过以下方式传递额外的参数：

   - 使用lambda函数：

   你可以创建一个小型的匿名函数（`lambda` 函数），这个函数接受 `future` 作为参数，然后调用你的 `callback` 函数并传递额外的参数。这是一种简洁的方式。

   ```python
extra_param = "some_value"
future.add_done_callback(lambda future: callback(future, extra_param))
```

   - 使用functools.partial：

   如果你觉得 `lambda` 函数语法有点冗长，你可以使用 `functools.partial`，它可以创建一个新的函数，固定住部分参数。在这里，我们将 `callback` 与额外的参数绑定在一起，然后将这个新的函数传递给 `add_done_callback`。

   ```python
from functools import partial
 
extra_param = "some_value"
callback_with_params = partial(callback, extra_param)
future.add_done_callback(callback_with_params)
```

   这两种方法都能达到同样的目的，即在 `callback` 函数被调用时，确保额外的参数也能被传递给它。选择其中一种方法通常取决于你个人的偏好和代码的风格。

   > **PS: 第二种方法按关键词参数传递** 🤬 后面写代码时发现的小问题， 不然callback_with_params会把extra_param当作future  
   >  即`callback_with_params = partial(callback, param = extra_param)` 固定多个参数也是如此
7. **关闭线程池：**  
   在使用完线程池后，最好通过`shutdown`方法来关闭它。这将确保所有的线程都被正确终止。  
   当然如果使用上下文管理方式不用

   ```python
def task(video_url):
print("开始执行任务", video_url)
time.sleep(5)
pool = ThreadPoolExecutor(10)
url_list = ["www.xxxx-{}.com".format(i) for i in range(300)]
for url in url_list:
    pool.submit(task, url)
print("执行中...")
pool.shutdown(True)  # 等待线程池中的任务执行完毕后，在继续执行
print('继续往下走')
```

请注意，尽管线程池可以提高并发性，但在某些情况下，由于Python的全局解释器锁（GIL），线程并不能充分利用多核处理器。如果你需要更好的并行性能，可以考虑使用`concurrent.futures.ProcessPoolExecutor`，它使用多个进程而不是线程。

示例

```python
import requests
from lxml import etree
from concurrent.futures import ThreadPoolExecutor
 
urls = [
    f'https://www.cnblogs.com/#p{page}'
    for page in range(1, 51)
]
 
 
def craw(url):
    response = requests.get(url)
    response.encoding = 'utf-8'
    page_text = response.text
    return page_text
 
 
def parse(html):
    tree = etree.HTML(html)
    names = tree.xpath('//*[@id="post_list"]/article/section/div/a/text()')
    for name in names:
        fout.write(str(name) + '\n')
 
 
#
# start = time.time()
# with ThreadPoolExecutor(max_workers=10) as pool:
#     craw_futures = [pool.submit(craw, url) for url in urls]
# end = time.time()
# print(f'craw完成- {end - start} s')
#
# start = time.time()
# with ThreadPoolExecutor(max_workers=10) as executor:
#     fout = open('./q1.txt', 'w', encoding='utf-8')
#     for craw_future in craw_futures:
#         html = craw_future.result()
#         parse_future = executor.submit(parse, html)
# end = time.time()
# print(f'parse完成- {end-start} s')
 
# 增加回调函数
def parse(craw_future):
    html = craw_future.result()
    tree = etree.HTML(html)
    names = tree.xpath('//*[@id="post_list"]/article/section/div/a/text()')
    for name in names:
        fout.write(str(name) + '\n')
 
 
start = time.time()
with ThreadPoolExecutor(max_workers=10) as pool:
    fout = open('./q1.txt', 'w', encoding='utf-8')
    craw_futures = [pool.submit(craw, url) for url in urls]
    for craw_future in craw_futures:
        craw_future.add_done_callback(parse)
fout.close()
end = time.time()
print(f'全部完成- {end - start} s')
```
