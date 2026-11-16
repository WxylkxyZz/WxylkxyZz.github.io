---
title: "Trap"
date: 2023-12-30 16:29:01
categories:
  - "Python"
tags:
  - "Python"
aging: true
aging_days: 30
---

## 记录一些补充知识

<!--more-->

### 函数

#### 函数的参数

##### 默认参数:

1. 必选参数在前， 默认参数在后，否则Python的解释器会报错（思考一下为什么默认参数不能放在必选参数前面） -> 照应结尾 结尾也有这个问题
2. 函数有多个参数时，把变化大的参数放前面，变化小的参数放后面。变化小的参数就可以作为默认参数。
3. 调用的时候, 可以按顺序提供默认参数, 当不按顺序提供部分默认参数时, 需要把参数名写上

**坑**

```python
def add_end(L=[]):
    L.append('END')
    return L
 
add_end([1, 2, 3])
# print
[1, 2, 3, 'END']
 
add_end()
# print
['END']
 
# 前面都正常， 再次调用发现不对
add_end()
# print
['END', 'END']
 
```

原因解释

> Python函数在定义的时候，默认参数L的值就被计算出来了，即[]，因为默认参数L也是一个变量，它指向对象[]，每次调用该函数，如果改变了L的内容，则下次调用时，默认参数的内容就变了，不再是函数定义时的[]了。

定义默认参数要牢记一点：**默认参数必须指向不变对象！** eg: `str、None` 类型

##### 可变位置参数（*args）

```python
def func(*numbers):
    sum = 0
    for n in numbers:
        sum = sum + n * n
    return sum
```

> 在函数定义中，`*numbers` 使用了星号 `*` 表示参数 `numbers` 是一个可变位置参数，它允许接受 **任意数量（0个或任意个）** 的位置参数，并将它们封装成一个 **元组**  
> 具体来说，在你的函数定义中，`*numbers` 接受任意数量的参数，将它们作为一个元组传递给 `numbers`。在函数体内，你可以通过迭代 `numbers` 来访问传递的所有参数。

如果你已经有一个包含参数的列表或元组，并希望将它们作为可变参数传递给一个函数，你可以使用 `*` 操作符进行拆包。

如果已经有一个list或者tuple，要调用一个可变参数怎么办？可以这样做：

```python
nums = [1, 2, 3]
>>> func(nums[0], nums[1], nums[2])
14
```

这样写显然是太费劲了 所以可以用下面操作

在调用函数时，可以使用 `*` 操作符将列表或元组中的元素解包，并传递给可变参数。下面是一个示例：

```python
def calc(*numbers):
    total = sum(n * n for n in numbers)
    return total
 
# 通过列表调用可变参数函数
numbers_list = [1, 2, 3]
result_list = calc(*numbers_list)
print(result_list)
 
# 通过元组调用可变参数函数
numbers_tuple = (4, 5, 6)
result_tuple = calc(*numbers_tuple)
print(result_tuple)
```

在这个例子中，`calc(*numbers_list)` 将列表中的元素解包，相当于调用 `calc(1, 2, 3)`。同样，`calc(*numbers_tuple)` 将元组中的元素解包，相当于调用 `calc(4, 5, 6)`。

使用 `*` 操作符可以方便地将列表或元组中的元素传递给可变参数，而无需手动指定每个参数。

关键词参数也是同样道理， 加俩`*`即可

##### 可变关键字参数（**kwargs）

跟可变参数差不多啦，关键字参数允许你传入0个或任意个含参数名的参数，这些关键字参数在函数内部自动组装为一个dict.

关键字参数是在函数调用时通过指定参数名来传递值的一种方式。在函数定义中，可以使用 `**kwargs`（或其他名称，通常约定俗成使用 `**kwargs`）来接收关键字参数。在函数体内，`kwargs` 将是一个包含传递的关键字参数的字典。

下面是一个简单的示例，演示如何使用关键字参数：

```python
def print_info(name, age, **kwargs):
    print(f"Name: {name}")
    print(f"Age: {age}")
 
    # 打印关键字参数
    for key, value in kwargs.items():
        print(f"{key}: {value}")
 
# 使用关键字参数调用函数
print_info(name="John", age=30, city="New York", occupation="Engineer")
```

在这个例子中，`print_info` 函数有两个普通参数 `name` 和 `age`，以及一个可变的关键字参数 `**kwargs`。通过在函数调用时使用关键字参数，我们可以传递额外的键值对作为关键字参数。

调用 `print_info(name="John", age=30, city="New York", occupation="Engineer")` 将输出：

```python
Name: John
Age: 30
city: New York
occupation: Engineer
```

请注意，在函数体内，`**kwargs` 被视为一个字典，可以通过迭代它的键值对来访问关键字参数的信息。这样的设计允许函数接受任意数量的关键字参数。

同样的你已经有一个dict了怎么调用关键字参数呢 如下：

如果已经有一个字典，你可以使用解包操作符 `**` 来将字典中的键值对作为关键字参数传递给函数。这种方式允许你使用字典的内容调用带有关键字参数的函数。

以下是一个示例：

```python
def print_info(name, age):
    print(f"Name: {name}")
    print(f"Age: {age}")
 
# 已经有一个字典
info_dict = {"name": "John", "age": 30}
 
# 使用解包操作符 ** 将字典作为关键字参数传递给函数
print_info(**info_dict)
```

在这个例子中，`**info_dict` 将字典 `info_dict` 中的键值对解包为关键字参数，并传递给 `print_info` 函数。这样，就可以使用字典中的内容调用带有关键字参数的函数。

请注意，字典中的键必须与函数定义中的参数名相匹配，否则会引发 `TypeError`。此外，使用解包操作符时，确保字典中的每个键都有对应的值。

错误示范:

如果字典中有多余的键值对，而函数的参数中没有对应的参数名，那么这些多余的键值对将被忽略，不会引发错误。这是因为解包操作符 `**` 只会将字典中与函数参数名匹配的键值对传递给函数。

以下是一个示例，演示了字典中有多余键值对时的情况：

```python
def print_info(name, age, **kwargs):
    print(f"Name: {name}")
    print(f"Age: {age}")
 
# 已经有一个字典，包含多余的键值对
info_dict = {"name": "John", "age": 30, "city": "New York"}
 
# 使用解包操作符 ** 将字典作为关键字参数传递给函数
# 多余的键值对 "city": "New York" 会被忽略
print_info(**info_dict)
 
# print
Name: John
Age: 30
```

在这个例子中，`info_dict` 包含多余的键值对 “city”: “New York”，但由于函数 `print_info` 只接受 `name` 和 `age` 两个参数，多余的键值对会被忽略。函数调用不会引发错误，且只会使用匹配的参数进行打印。  
这种方式使得你可以在字典中包含多余的信息，而无需担心会破坏函数调用。只有与函数参数名匹配的键值对会被传递给函数。

##### 命名关键字参数

要限制关键字参数的名字，就可以用命名关键字参数

在 Python 中，命名关键字参数是一种函数参数的类型，它允许你明确指定某些关键字参数必须使用关键字传递，而不能作为位置参数传递。这样可以提高函数调用的可读性和可维护性。

在函数定义中，通过在参数列表中 **使用单个星号 `*`（用于表示位置参数的结束）后面的参数来声明命名关键字参数** 。这些参数需要在函数调用时明确使用关键字传递。

以下是一个简单的示例：

```python
def greet(name, age, *, city, occupation):
    print(f"Name: {name}")
    print(f"Age: {age}")
    print(f"City: {city}")
    print(f"Occupation: {occupation}")
 
# 使用命名关键字参数调用函数
greet("John", 30, city="New York", occupation="Engineer")
```

在这个例子中，`city` 和 `occupation` 被声明为命名关键字参数，因为它们出现在 `*` 后面。当调用 `greet` 函数时，__必须使用关键字来指定这两个参数的值__。

注意，如果你尝试使用位置参数传递命名关键字参数，将会引发 `TypeError`。

```python
# 以下调用将引发 TypeError
# greet("John", 30, "New York", "Engineer")
```

命名关键字参数的使用可以帮助防止在函数调用时出现歧义，特别是在函数具有大量参数时。这样的做法使得函数接口更加清晰，并提高了代码的可读性。

如果函数定义中已经有了一个可变参数（例如，带有 `*args`），你可以在其后声明命名关键字参数。可变参数之后的参数都需要使用关键字传递。

以下是一个示例：

```python
def process_data(name, age, *args, city="New York", occupation):
    print(f"name: {name}")
    print(f"age: {age}")
    print("Positional arguments:", args)
    print(f"City: {city}")
    print(f"Occupation: {occupation}")
 
# 使用可变参数和命名关键字参数调用函数
process_data('wxylkayz', 21, 1,2, 3, occupation="Engineer")
```

在这个例子中，`*args` 表示可变位置参数，可以接受任意数量的位置参数。而 `city` 和 `occupation` 则是命名关键字参数，需要使用关键字传递。 ps:此处`city`指定了默认参数可以不写

简而言之: 如果不用关键字传递参数，Python解释器把前两个参数视为位置参数，后两个参数传给`*args`，因为缺少命名关键字参数导致报错。

注意，在调用函数时，如果你使用了可变参数，**必须确保在关键字参数之前传递所有的位置参数** ，否则将引发 `SyntaxError`。

```python
# 以下调用将引发 SyntaxError
# process_data(city="New York", occupation="Engineer", 1, 2, 3)
```

总的来说，如果函数定义中已经有了一个可变参数，你可以在其后声明命名关键字参数，并在函数调用时使用关键字传递。

##### 参数组合

摘选自 [廖雪峰Python](https://www.liaoxuefeng.com/wiki/1016959663602400/1017261630425888)  
在Python中定义函数，可以用必选参数、默认参数、可变参数、关键字参数和命名关键字参数，这5种参数都可以组合使用。

但是请注意，参数定义的 **顺序** 必须是：必选参数、默认参数、可变参数、命名关键字参数和关键字参数。

比如定义一个函数，包含上述若干种参数：

```python
def f1(a, b, c=0, *args, **kw):
    print('a =', a, 'b =', b, 'c =', c, 'args =', args, 'kw =', kw)
 
def f2(a, b, c=0, *, d, **kw):
    print('a =', a, 'b =', b, 'c =', c, 'd =', d, 'kw =', kw)
 
>>> f1(1, 2)
a = 1 b = 2 c = 0 args = () kw = {}
 
>>> f1(1, 2, c=3)
a = 1 b = 2 c = 3 args = () kw = {}
 
>>> f1(1, 2, 3, 'a', 'b')
a = 1 b = 2 c = 3 args = ('a', 'b') kw = {}
 
>>> f1(1, 2, 3, 'a', 'b', x=99)
a = 1 b = 2 c = 3 args = ('a', 'b') kw = {'x': 99}
 
>>> f2(1, 2, d=99, ext=None)
a = 1 b = 2 c = 0 d = 99 kw = {'ext': None}
```

最神奇的是通过一个tuple和dict，你也可以调用上述函数：

```python
>>> args = (1, 2, 3, 4)
>>> kw = {'d': 99, 'x': '#'}
>>> f1(*args, **kw)
a = 1 b = 2 c = 3 args = (4,) kw = {'d': 99, 'x': '#'}
 
>>> args = (1, 2, 3)
>>> kw = {'d': 88, 'x': '#'}
>>> f2(*args, **kw)
a = 1 b = 2 c = 3 d = 88 kw = {'x': '#'}
```

ps: 想一下以下可否成功调用

```python
args = (1, 2, 3)
kw = {'z': 88, 'x': '#'}
f2(*args, **kw)
```

> 我的理解就是解包后看作顺序填充  
> 所以，对于任意函数，都可以通过类似func(*args, **kw)的形式调用它，无论它的参数是如何定义的。  
> ps: 虽然可以组合多达5种参数，但不要同时使用太多的组合，否则函数接口的可理解性很差。

#### 闭包

闭包（Closure）是一种函数对象，它包含了函数定义体中引用、但是不在定义体中定义的非全局变量。换句话说，闭包允许函数引用在其外部定义的非全局变量。

在理解闭包之前，我们先来看一个简单的例子：

```python
def outer_function(x):
    def inner_function(y):
        return x + y
    return inner_function
 
closure = outer_function(10)
result = closure(5)
print(result)  # 输出 15
```

在这个例子中，`outer_function` 返回了 `inner_function`，并且 `inner_function` 中引用了 `outer_function` 中的变量 `x`。当我们调用 `outer_function(10)` 时，它返回了 `inner_function`，这个返回的函数就是一个闭包。之后，我们可以使用 `closure(5)` 来调用闭包，它会使用 `x=10` 的值，最终返回 15。

闭包有几个重要的特点：

1. **引用外部变量：** 闭包允许函数访问在其外部定义的非全局变量。在上面的例子中，`inner_function` 访问了 `outer_function` 中的变量 `x`。
2. **保持状态：** 闭包能够保持其作用域中的状态。在示例中，每次调用 `closure(5)` 时，它都记住了 `x` 的值为 10。
3. **延迟执行：** 通过闭包，我们可以将函数的执行延迟到以后的时间。在上述例子中，我们并没有立即执行 `inner_function`，而是将它赋给了 `closure`，稍后再调用。

使用闭包可以有效地隐藏函数的一些实现细节，同时允许在不同的调用之间保持状态。这在某些编程场景中非常有用。
