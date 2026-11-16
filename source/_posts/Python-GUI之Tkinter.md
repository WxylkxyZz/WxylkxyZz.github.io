---
title: "Python GUI之Tkinter"
date: 2023-04-27 15:17:57
updated: 2023-04-29 10:39:27
categories:
  - "随笔"
tags:
  - "Hexo"
  - "Keep"
aging: true
aging_days: 30
---

- Tkinter是什么？

Tkinter是Python标准GUI工具包的接口，用于窗口视窗设计。它是Python自带的GUI界面，可以编辑，实现直观的功能。

<!--more-->

对于有GUI编程经验的人来说，Tkinter十分简单。Python的GUI库很多，但选择Tkinter最为简单，因为它是自带库，无需下载安装，并且Python作为脚本语言，在开发小工具时需要具备界面，并且Tkinter足够胜任。本文主要概述和实践Tkinter编程，为GUI编程新手树立基本概念，并且展示如何实现小的图形窗口功能。

(以下内容转载)

> 对于Tkinter编程，可以用两个比喻来理解：
>
> - 第一个，作画。我们都见过美术生写生的情景，先支一个画架，放上画板，蒙上画布，构思内容，用铅笔画草图，组织结构和比例，调色板调色，最后画笔勾勒。相应的，对应到tkinter编程，那么我们的显示屏就是支起来的画架，根窗体就是画板，在tkinter中则是Toplevel，画布就是tkinter中的容器（Frame），画板上可以放很多张画布（Convas），tkinter中的容器中也可以放很多个容器，绘画中的构图布局则是tkinter中的布局管理器（几何管理器），绘画的内容就是tkinter中的一个个小组件，一幅画由许多元素构成，而我们的GUI界面，就是有一个个组件拼装起来的，它们就是widget。
> - 第二个，我们小时候都玩过积木，只要发挥创意，相同的积木可以堆出各种造型。tkinter的组件也可以看做一个个积木，形状或许不同，其本质都是一样的，就是一个积木，不管它长什么样子，它始终就是积木！所以这些小组件都有许多共性，另外，个人认为，学习界面编程，最重要的不是一开始学习每个积木的样子，不是学习每个组件怎么用，而是这些组件该怎么放。初始学习中，怎么放远远比怎么用重要的多。网上有大量的文章资料，基本全是介绍组件怎么用的，对于怎么放，也就是tkinter中的布局管理器，都是一笔带过，这对初学者有点本末倒置，或许绝大部分是转载的原因吧，极少是自己真正写的。组件怎么用不是最迫切的，用到的时候再去了解也不迟，边用边学反而更好。因此我将专门写一章，详细介绍布局管理器的使用。

## Tkinter 控件详细介绍

![image]( "主要控件")

### 1.Tkinter 模块元素简要说明

> The Button Widget  
> The Canvas Widget  
> The Checkbutton Widget  
> The Entry Widget  
> The Frame Widget  
> The Label Widget  
> The LabelFrame Widget  
> The Listbox Widget  
> The Menu Widget  
> The Menubutton Widget  
> The Message Widget  
> The OptionMenu Widget  
> The PanedWindow Widget  
> The Radiobutton Widget  
> The Scale Widget  
> The Scrollbar Widget  
> The Spinbox Widget  
> The Text Widget  
> The Toplevel Widget  
> Basic Widget Methods  
> Toplevel Window Methods

### 2.常用窗口部件及简要说明

`Tkinter`支持`16`个核心的窗口部件，这个`16`个核心窗口部件类简要描述如下：

| 编号 | 控件 | 作用 |
| :---: | :---: | :--- |
| 1 | Label | `标签控件` -> 显示文本跟位图 |
| 2 | Button | `按钮控件` -> 在程序中显示按钮,用来执行一个命令或别的操作 |
| 3 | Entry | `输入控件` -> 用于显示简单的文本内容 |
| 4 | Text | `文本控件` -> 用于显示多行文本 格式化文本显示,允许你用不同的样式和属性来显示和编辑文本,同时支持内嵌图象和窗口 |
| 5 | Listbox | `列表框控件` -> 显示供选方案的一个列表。listbox能够被配置来得到radiobutton或checklist的行为 |
| 6 | RadioButton | `单选按钮控件` -> 代表一个变量，它可以有多个值中的一个。点击它将为这个变量设置值，并且清除与这同一变量相关的其它radiobutton |
| 7 | Checkbutton | `多选框控件` -> 代表一个变量，它有两个不同的值。点击这个按钮将会在这两个值间切换。 |
| 8 | Scale | `范围控件` -> 显示一个数值刻度，允许你通过滑块来设置一数字值。 |
| 9 | Canvas | `画布控件` -> 用来绘制图表和图，创建图形编辑器，实现定制窗口部件。 |
| 10 | Menu | `菜单控件` -> 菜单条,用来实现下拉和弹出式菜单。 |
| 11 | Frame | `框架控件` -> 在屏幕上显示一个矩形区域，多用来作为容器 / 一个容器窗口部件。帧可以有边框和背景，当创建一个应用程序或dialog(对话)版面时，帧被用来组织其它的窗口部件。 |
| 12 | messageBox | ‘窗口控件’ -> 消息框，用于显示你应用程序的消息框 |
| 13 |  |  |
| 14 |  |  |
| 15 |  |  |
| 16 |  |  |

## 实践

### 1.窗口及Label部件

> 我们要学习使用上面提到的这些控件首先要创建一个主窗口，就像作画一样，先要架好架子和画板，然后才能在上面放画纸和各种绘画元素，创建好主窗口才能在上面放置各种控件元素。而创建过程是很简单的，如下：

```python
import tkinter as tk

# 1. 实例化一个Obj，建立窗口window
window = tk.Tk()

# 2. 给窗口起名字
window.title("My first window")

# 3. 设定窗口大小(长x宽) 这里的乘是小x
window.geometry('300x300')

# 4. 给图形界面设定标签

l1 = tk.Label(window, text="打标签", bg='yellow', font=('Arial', 12), width=10, height=2)
# bg:背景，font:字体，width:长，height:高，这里的长和高是字符的长和高，比如height=2,就是标签有2个字符这么高

# 5. 放置标签
l1.pack()
# Label内容content区域放置位置，自动调节尺寸. 放置lable的方法有：1）l.pack(); 2)l.place();


# 6. 主窗口循环显示
window.mainloop()
# 注意，loop因为是循环的意思，window.mainloop就会让window不断的刷新，如果没有mainloop,就是一个静态的window,传入进去的值就不会有循环，mainloop就相当于一个很大的while循环，有个while，每点击一次就会更新一次，所以我们必须要有循环
# 所有的窗口文件都必须有类似的mainloop函数，mainloop是窗口文件的关键的关键。

```

测试效果:

![LableEeffect]( "测试效果")

### 2.Button窗口部件

**简单说明：**

> Button（按钮）部件是一个标准的Tkinter窗口部件，用来实现各种按钮。按钮能够包含文本或图象，并且你能够将按钮与一个Python函数或方法相关联。当这个按钮被按下时，Tkinter自动调用相关联的函数或方法。
>
> - 按钮仅能显示一种字体，但是这个文本可以跨行。另外，这个文本中的一个字母可以有下划线，例如标明一个快捷键。默认情况，Tab键用于将焦点移动到一个按钮部件。
> - 什么时候用按钮部件?  
>   简言之，按钮部件用来让用户说“马上给我执行这个任务”，通常我们用显示在按钮上的文本或图象来提示。按钮通常用在工具条中或应用程序窗口中，并且用来接收或忽略输入在对话框中的数据。关于按钮和输入的数据的配合，可以参看Checkbutton和Radiobutton部件。
> - 如何创建?  
>   普通的按钮很容易被创建，仅仅指定按钮的内容（文本、位图、图象）和一个当按钮被按下时的回调函数即可：  
>   b = tk.Button(window, text=”hit me”, command=hit_me)
> - 没有回调函数的按钮是没有用的，当你按下这个按钮时它什么也不做。你可能在开发一个应用程序的时候想实现这种按钮，比如为了不干扰你的beta版的测试者：  
>   b = tk.Button(window, text=”Help”, command=DISABLED)

***代码如下***

```python
window = tk.Tk()
window.title("button learn")
window.geometry('300x300')
var = tk.StringVar()
# 将label标签的内容设置为字符类型，用var来接收hit_me函数的传出内容用以显示在标签上
l1 = tk.Label(window, textvariable=var, bg='green', fg='red', font=("Arial", 12), width=30, height=2)
l1.pack()
# 定义一个函数功能（内容自己自由编写），供点击Button按键时调用，调用命令参数command=函数名
on_hit = False


def hit_me():
    global on_hit
    if on_hit == False:
        on_hit = True
        var.set('you hit me')
    else:
        on_hit = False
        var.set('')


b = tk.Button(window, text="hit me", font=("Arial", 12), width=30, height=2, command=hit_me)
b.pack()

window.mainloop()
```

效果图如下:  
![buttonEffect]( "测试效果")

点击后

![buttonEffect]( "测试效果")

### 3.Entry窗口部件

简单说明：

> Entry是tkinter类中提供的的一个单行文本输入域，用来输入显示一行文本，收集键盘输入(类似 HTML 中的 text)。
>
> - 什么时候用?  
>   需要用户输入用户信息时，比如我们平时使用软件、登录网页时，用户交互界面让我们登录账户信息等时候可以用到。

***代码***

```python
window = tk.Tk()
window.title("Entry learn")
window.geometry('300x300')

lambda_login = tk.Label(window, text='模拟登录', width=20, height=1)
lambda_login.pack()
# 在图形界面上设定输入框控件entry并放置控件
account = tk.Entry(window, show=None, font=("Arial", 12))  # show设为None显示成明文形式
account.pack()
password = tk.Entry(window, show='*', font=("Arial", 12))
password.pack()


def check_button():
    """
    当登录按钮被点击时触发此函数
    :return:
    """
    name = account.get()
    pwd = password.get()
    msg = f'用户名:{name}\n密码:{pwd}'
    messagebox.showinfo(title='登录提示', message=msg)


button = tk.Button(window, text='登录', width=15, height='2', command=check_button)
button.pack()

window.mainloop()

```

效果图如下  
![EntryEffert]( "测试效果")

### 4.Text窗口部件

简单说明：

> Text是tkinter类中提供的的一个多行文本区域，显示多行文本，可用来收集(或显示)用户输入的文字(类似 HTML 中的 textarea)，格式化文本显示，允许你用不同的样式和属性来显示和编辑文本，同时支持内嵌图象和窗口。  
> (Entry 只能输入一行文字，如果想要输入多行文字，需要使用Text文本控件)
>
> - 什么时候用?  
>   在需要显示编辑用户、产品多行信息时，比如显示用户详细描述文字，产品简介等等，支持随时编辑。

***代码***

```python
# Text

window = tk.Tk()
window.title("Text learn")
window.geometry("300x300")
e = tk.Entry(window, show=None)
e.pack()


def insert_point():
    var = e.get()
    t.insert("insert", var)


def insert_end():
    var = e.get()
    t.insert("end", var)


b1 = tk.Button(window, text="insert point", width=10, height=2, command=insert_point)
b1.pack()
b2 = tk.Button(window, text="insert end", width=10, height=2, command=insert_point)
b2.pack()

t = tk.Text(window, height=3)
t.pack()

window.mainloop()

```

创建文本框时，可以指定高度，如果你输入的内容超过了这个高度，它会自动向上滚动，和Entry不同，想要获取文本框里的所有输入的内容，必须用这样的语句才行  
`text = t.get("0.0", "end")`

效果图如下  
![TextEffect]( "测试效果")

### 5.Listbox窗口部件

简单说明：

> Text是tkinter类中提供的的列表框部件，显示供选方案的一个列表。listbox能够被配置来得到radiobutton或checklist的行为。
>
> - 什么时候用?  
>   在有一个很多内容选项组成的列表提供用户选择时会用到。

***代码***

```python
window = tk.Tk()
window.title("ListBox learn")
window.geometry("300x300")

list_items = tk.StringVar()
list_items.set(('python', 'Jave', 'C语言'))

lb = tk.Listbox(window, listvariable=list_items)
lb.pack()

no_select = "没有选中任何选项"


def check_button():
    select = lb.curselection()  # 获取被选中的可选项的索引
    print(len(select))
    if len(select) == 0:
        label_text.set(no_select)
    else:
        text = lb.get(select)
        label_text.set(f"你已选择{text}")


button = tk.Button(window, text="显示所选", width='15', height=1, command=check_button)
button.pack()

label_text = tk.StringVar()
label_text.set(no_select)
label = tk.Label(window, width=15, height=2, textvariable=label_text)
label.pack()

window.mainloop()
```

效果图如下:

![ListBoxEffect]( "测试效果")

### 6.Radiobutton窗口部件

简单说明：

ListBox可以提供若干可选项供用户选择，此外，RadioButton也可以实现相同的功能.

> Radiobutton：代表一个变量，它可以有多个值中的一个。点击它将为这个变量设置值，并且清除与这同一变量相关的其它radiobutton。
>
> - 什么时候用?  
>   在有一个很多内容选项组成的选项列表提供用户选择时会用到，用户一次只能选择其中一个，不能多选。

- 本节通过var对象获取了被选中的单选按钮的值，然后使用label的config方法修改标签上的显示文字，现在你又学会一种修改标签文字内容的方法

***代码***

```python
window = tk.Tk()
window.title("RadoiButton Learn")
window.geometry("300x300")

var = tk.StringVar()  # 定义一个var用来将radiobutton的值和Label的值联系在一起
label = tk.Label(window, width=15, height=2, bg='green', text="")
label.pack()


def select():
    label.config(text=f"你选择了{var.get()}")


r1 = tk.Radiobutton(window, text='Python', variable=var, value='python', command=select)
r1.pack()

r2 = tk.Radiobutton(window, text='Java', variable=var, value='Java', command=select)
r2.pack()

r3 = tk.Radiobutton(window, text='C语言', variable=var, value='C语言', command=select)
r3.pack()

window.mainloop()

```

效果图如下:

![RadioButtonEffect]( "测试效果")

### 7.Checkbutton窗口部件

前面学习了ListBox,RadioButton,他们都提供了让用户做选择的功能，Checkbutton同样可以提供这样的选择功能，而且，它是 **多选** 的

简单说明：

> Checkbutton：代表一个变量，它有两个不同的值。点击这个按钮将会在这两个值间切换，选择和取消选择。
>
> - 什么时候用?  
>   在有一个很多内容选项组成的选项列表提供用户选择时会用到，用户一次可以选择多个。

***代码***

```python
# CheckButton
window = tk.Tk()
window.title("CheckButton Learn")
window.geometry("300x300")

label = tk.Label(window, width=30, height=2, bg='green', text='')
label.pack()

var1 = tk.IntVar()
var2 = tk.IntVar()
var3 = tk.IntVar()


def select():
    select_list = []
    if var1.get() == 1:
        select_list.append('Python')
    if var2.get() == 1:
        select_list.append('Java')
    if var3.get() == 1:
        select_list.append('C语言')

    text = f"你选择了{','.join(select_list)}"
    label.config(text=text)


c1 = tk.Checkbutton(window, text='Python', variable=var1, onvalue=1, offvalue=0, command=select)
c2 = tk.Checkbutton(window, text='Java', variable=var2, onvalue=1, offvalue=0, command=select)
c3 = tk.Checkbutton(window, text='C语言', variable=var3, onvalue=1, offvalue=0, command=select)
c1.pack()
c2.pack()
c3.pack()
window.mainloop()

```

效果图如下:

![CheckButtonEffect]( "测试效果")

### 8.Scale窗口部件

简单说明：

> Scale： 尺度（拉动条），允许你通过滑块来设置一数字值。
>
> - 什么时候用?  
>   在需要用户给出评价等级，或者给出一个评价分数，或者拉动滑动条提供一个具体的数值等等。

***代码***

```python
# Scale
window = tk.Tk()
window.title("Scale Learn")
window.geometry('300x300')

label = tk.Label(window, width=20, height=2, bg='green', text="请滑动选择🙂")
label.pack()


def select(var):
    label.config(text=f'你选择的是{var}')


scale = tk.Scale(window, label="可选范围", from_=0, to=10, orient=tk.HORIZONTAL, length=400, tickinterval=2,
                 resolution=0.1, command=select)
# 从0到10,刻度为2，orient设置横向显示，length长度
scale.pack()
window.mainloop()

```

效果图如下:

![ScaleEffert]( "测试效果")

### 9.Canvas窗口部件

你可以在Canvas 画各种图形，也可以展示图片，同时，你还可以移动他们

> 简单说明：　　  
> Canvas：画布，提供绘图功能(直线、椭圆、多边形、矩形) 可以包含图形或位图，用来绘制图表和图，创建图形编辑器，实现定制窗口部件。
>
> - 什么时候用?  
>   在比如像用户交互界面等，需要提供设计的图标、图形、logo等信息是可以用到画布。

1. 画一条对角线  
   ***代码***

   ```python
window = tk.Tk()
window.title("Canvas Learn_画布")
window.geometry("500x400")

canvas = tk.Canvas(window, bg='pink', height=300, width=150)
canvas.pack()
line = canvas.create_line(0, 0, 150, 300)
window.mainloop()
```

效果图如下:  
![CanvasEffect1]( "测试效果")

**&:**

- 使用create_line创建一条直线时，需要指定4个坐标，分别是x1, y1, x2, y2, (x1, y1) 是起始点坐标，(x2, y2)是结束点坐标。
- 在所有图形界面中, 左上角的坐标值是(0, 0), 以此为原点开展坐标系！👉X轴,👇y轴

2. 画出圆形，长方形  
   ***代码***

   ```python
window = tk.Tk()
window.title("Canvas Learn 2")
window.geometry("500x400")
canvas = tk.Canvas(window, bg='pink', height=300, width=200)
canvas.pack()
oval = canvas.create_oval(75, 75, 150, 150, fill='red')
rect = canvas.create_rectangle(75, 175, 125, 250, fill='Purple')
window.mainloop()
```

   效果图如下:

![CanvasEffect1]( "测试效果")

**&:**

- create_rectangle方法里，前两个参数决定了长方形的左上角坐标，后两个参数决定了长方形的右下角坐标。
- create_oval方法并不是只能用来画圆形，椭圆形也可以，这完全取决于传入的参数  
  如下  
  ![create_oval]( "测试效果")

3. 图形移动  
   ***代码***

   ```python
window = tk.Tk()
window.title("Canvas Learn 3")
window.geometry("500x400")
canvas = tk.Canvas(window, width=300, height=300, bg='pink')
canvas.pack()
oval = canvas.create_oval(100, 100, 150, 150, fill='Purple')


def movie():
    canvas.move(oval, 50, 25)


button = tk.Button(window, text="点此移动⚪", command=movie)
button.pack()
window.mainloop()
```

   效果图如下:

![CanvasEffect3]( "测试效果")

4. 展示图片  
   ***代码***

   ```python
window = tk.Tk()
window.title("Canvas Learn 4")
window.geometry("600x400")

canvas = tk.Canvas(window, height=330, width=510, bg="pink")
canvas.pack()
image_file = ImageTk.PhotoImage(file='./8a81ecd8fdd266b3221da325875c0ea8.gif')
image = canvas.create_image(10, 10, anchor='nw', image=image_file)
window.mainloop()
```

   效果图如下:  
   ![CanvasEffert4]( "测试效果")

**&:**

- tkinter自己的PhotoImage只支持gif格式的图片，因此，我们需要使用PIL的ImageTk，这样，所有格式的图片都可以显示
- 10：图片的x坐标，表示图片左上角的位置在canvas上距离左边界的距离。
- 10：图片的y坐标，表示图片左上角的位置在canvas上距离上边界的距离。
- anchor=’nw’：指定锚点的位置，这里是左上角，即北西角。
- image=image_file：指定图片的内容，图片文件必须是一个有效的图片文件对象（比如PNG、JPEG、GIF等格式的图片）。在这个例子中，image_file是之前打开并加载的一个图片文件对象。create_image()会载入这个图片，并在canvas中显示出来。
- 需要注意的是，create_image()函数返回一个整数对象，这个对象代表了在canvas上创建的图片的标识符，你可以使用这个标识符来操作这个图片对象。例如，你可以使用move()函数将图片移到新的位置，使用delete()函数删除这个图片对象等。同时，如果你想要在canvas中显示不止一张图片，你可以多次调用create_image()函数来创建多个图片对象，每个对象都会返回一个不同的标识符，这样你就可以对它们进行独立的控制和操作。
- 图片锚定点位置参数图  
  ![Anchor]( "Anchor")

### 10.Menu窗口部件

简单说明：

> Menu：菜单条，用来实现下拉和弹出式菜单，点下菜单后弹出的一个选项列表,用户可以从中选择
>
> - 什么时候用?  
>   在比如像软件或网页交互界面等，需要提供菜单选项功能提供用户选择菜单选项功能时用到。

***代码***

```python
# Menu

window = tk.Tk()
window.title("Menu Learn")
window.geometry("600x400")
label = tk.Label(window, text='$', height='300', width='400', bg='pink')
label.pack()

counter = 0


def do_job():
    global counter
    label.config(text=f"do -> {str(counter)}")
    counter += 1


menubar = tk.Menu(window)  # 先创建菜单栏！

# 已经创建好了菜单栏！ 开始填充

# 创建一个File菜单项（默认不下拉，下拉内容包括New，Open，Save，Exit功能项） 此时menubar是画板
file_menu = tk.Menu(menubar, tearoff=0)
menubar.add_cascade(label='File', menu=file_menu)
file_menu.add_command(label='New', command=do_job)
file_menu.add_command(label='Open', command=do_job)
file_menu.add_command(label='Save', command=do_job)
# 添加一条分割线
file_menu.add_separator()
file_menu.add_command(label='Exit', command=window.quit())  # 用tkinter里面自带的quit()函数

# 创建Edit菜单项
edit_menu = tk.Menu(menubar, tearoff=0)  # 此时menubar是画板
menubar.add_cascade(label='Edit', menu=edit_menu)
edit_menu.add_command(label='Cut', command=do_job)
edit_menu.add_command(label='Copy', command=do_job)
edit_menu.add_command(label='Paste', command=do_job)

# 创建二级菜单 此处想象画板 此时画板成了一级菜单
submenu = tk.Menu(file_menu, tearoff=0)
file_menu.add_cascade(label='Import', menu=submenu, underline=0)
submenu.add_command(label='Submenu_1', command=do_job)

# 创建菜单栏完成后，配置让菜单栏menubar显示出来
window.config(menu=menubar)
# 主窗口循环显示
window.mainloop()
```

效果图如下:

![MenuEffert]( "测试效果")

### 11.Frame窗口部件

简单说明：

> Frame：框架，用来承载放置其他GUI元素，就是一个容器，是一个在 Windows 上分离小区域的部件, 它能将 Windows 分成不同的区,然后存放不同的其他部件. 同时一个 Frame 上也能再分成两个 Frame, Frame 可以认为是一种容器.
>
> - 什么时候用?  
>   在比如像软件或网页交互界面等，有不同的界面逻辑层级和功能区域划分时可以用到，让交互界面逻辑更加清晰。

> 前面每一讲介绍一个控件，所举的例子都没有考虑界面的布局，所有控件都挤在一起，本篇所将的frame控件，就是被用来布局使用的，一个frame就是一个矩形框，在一个window上，你可以排放多个frame，一个frame上又可以摆放多个frame，这样就可以将一个界面划分出多个区域，frame也是容器，前面讲的那些控件都可以放在frame上面。

***代码***

```python
# Frame
window = tk.Tk()
window.title('Frame Learn')
window.geometry('300x300')
# 和前面部件分开创建和放置不同，其实可以创建和放置一步完成!!!
tk.Label(window, bg='pink', text='On the window', font=('Arial', 16)).pack()

frame = tk.Frame(window)
frame.pack()
# 此刻以frame为画板
frame_l = tk.Frame(frame)
frame_r = tk.Frame(frame)
frame_l.pack(side='left')
frame_r.pack(side='right')

# 创建三组标签
tk.Label(frame_l, text='on the frame_l1', bg='green').pack()
tk.Label(frame_l, text='on the frame_l2', bg='green').pack()
tk.Label(frame_l, text='on the frame_l3', bg='green').pack()
tk.Label(frame_r, text='on the frame_r1', bg='red').pack()
tk.Label(frame_r, text='on the frame_r2', bg='red').pack()
tk.Label(frame_r, text='on the frame_r3', bg='red').pack()

window.mainloop()
```

效果图如下:

![FrameEffert]( "测试效果")

### 12.messageBox窗口部件

简单说明：

> messageBox：消息框，用于显示你应用程序的消息框。(Python2中为tkMessagebox)，其实这里的messageBox就是我们平时看到的弹窗。 我们首先需要定义一个触发功能，来触发这个弹窗，这里我们就放上以前学过的button按钮，通过触发功能，调用messagebox吧，点击button按钮就会弹出提示对话框。下面给出messagebox提示信息的几种形式：

```python
tkinter.messagebox.showinfo(title='Hi', message='你好！')            # 提示信息对话窗
tkinter.messagebox.showwarning(title='Hi', message='有警告！')       # 提出警告对话窗
tkinter.messagebox.showerror(title='Hi', message='出错了！')         # 提出错误对话窗
print(tkinter.messagebox.askquestion(title='Hi', message='你好！'))  # 询问选择对话窗return 'yes', 'no'
print(tkinter.messagebox.askyesno(title='Hi', message='你好！'))     # return 'True', 'False'
print(tkinter.messagebox.askokcancel(title='Hi', message='你好！'))  # return 'True', 'False'
```

> 什么时候用?  
> 在比如像软件或网页交互界面等，有不同的界面逻辑层级和功能区域划分时可以用到，让交互界面逻辑更加清晰。

***代码***

```python
# messagebox

window = tk.Tk()
window.title("Messagebox Learn")
window.geometry("600x400")


def hit():
    # tk.messagebox.showinfo(title='Info', message='show Info : \nxxx')
    # tk.messagebox.showinfo(title='Warn', message='show Warn : \nxxx')
    # tk.messagebox.showinfo(title='Error', message='show Error: \nxxx')
    tk.messagebox.askyesno(title='Ask question', message='傻子!')
    tk.messagebox.askyesno(title='Yes or No', message='you are bendan')
    tk.messagebox.askokcancel(title='True or False', message='you are bendan')


button = tk.Button(window, text='try hit me', bg='pink', command=hit)
button.pack()
window.mainloop()
```

效果如下:

![MessageBoxEffert]( "测试效果")

**&:**

注意：上面的print()不是必须的， print只是在控制台打印return的值

### 13.布局 - pack/grid/place

简要说明

> 让控件出现在你期望的位置上，有三个方法，一个比一个精确

#### Pack：The Pack Geometry Manager

pack 可以指定上下左右

***代码***

```python
window = tk.Tk()
window.title("pgp Learn")
window.geometry("600x400")

tk.Label(window, text='N', fg='pink', font=('Arial', 20)).pack(side='top')
tk.Label(window, text='S', fg='pink', font=('Arial', 20)).pack(side='bottom')
tk.Label(window, text='W', fg='pink', font=('Arial', 20)).pack(side='left')
tk.Label(window, text='E', fg='pink', font=('Arial', 20)).pack(side='right')
window.mainloop()
```

效果图如下:

![PackEffert]( "测试效果")

#### Grid：The Grid Geometry Manager

grid 是方格, 所以所有的内容会被放在这些规律的方格中。如下

```python
for i in range(3):
    for j in range(3):
        tk.Label(window, text=1).grid(row=i, column=j, padx=10, pady=10, ipadx=10, ipady=10)
```

以上的代码就是创建一个三行三列的表格，其实 grid 就是用表格的形式定位的。这里的参数 row 为行，colum 为列，padx 就是单元格左右间距，pady 就是单元格上下间距，ipadx是单元格内部元素与单元格的左右间距，ipady是单元格内部元素与单元格的上下间距。

参数解释:

- row=i：设置 Label 所在的行号为 i，第一行为 0。
- column=j：设置 Label 所在的列号为 j，第一列为 0。
- padx=10：设置 Label 的内部水平留白大小为 10 point。
- pady=10：设置 Label 的内部垂直留白大小为 10 point。
- ipadx=10：设置 Label 相对于其父容器水平方向上额外的 padding 。也就是在 padx 的基础上再添加额外的 padding。
- ipady=10：设置 Label 相对于其父容器垂直方向上额外的 padding。也就是在 pady 的基础上再添加额外的 padding。

***代码***

```python
window = tk.Tk()
window.title("pgp Learn")
window.geometry("600x400")

for i in range(1, 6):
    for j in range(1, 6):
        tk.Label(window, text=1).grid(row=i, column=j, padx=10, pady=10, ipadx=10, ipady=10)
window.mainloop()
```

效果图如下:  
![GridEffert]( "测试效果")

#### Place：The Place Geometry Manager

简要说明

> 在3个设置空间位置的方法中，place是最灵活的，因为它直接指定了空间出现的位置  
> 如此处给的(50, 100)，就是将这个部件放在坐标为(x=50, y=100)的这个位置, 后面的参数 anchor=’nw’，就是前面所讲的锚定点是西北角。例如：

```python
tk.Label(window, text='Pl', font=('Arial', 20), ).place(x=50, y=100, anchor='nw')
```

***代码***

```python
window = tk.Tk()
window.title("pgp Learn")
window.geometry("600x400")
tk.Label(window, text='Place', fg='pink', font=('Arial', 20)).place(x=50, y=50, anchor='nw')
window.mainloop()
```

效果图如下:

![PlaceEffert]( "测试效果")

本文学习主要来源以下文章

1. [Python GUI之tkinter窗口视窗教程大集合（看这篇就够了）](https://sunhwee.com/posts/80fa3a85.html#toc-heading-3)
2. [Python GUI之tkinter教程](http://www.coolpython.net/tk/tk_primary/index.html)
