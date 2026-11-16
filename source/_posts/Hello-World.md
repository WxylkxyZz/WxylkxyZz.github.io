---
title: "Hello World"
date: 2022-07-14 13:21:27
updated: 2023-01-07 15:53:16
categories:
  - "随笔"
tags:
  - "主题"
aging: true
aging_days: 30
---

记录Keep主题使用中积累的问题

<!--more-->

## 文章置顶

实现文章置顶，需要在文章 .md 文件中添加 sticky 属性，sticky 值越大，在主页中置顶的文章越靠前。

## 文章截取

实现对文章自由截取，并在首页完整显示出来

`<!-- more -->` 加入文章即可

## 关闭文章版权信息

copyright_info参数 默认true 只有设置为false时生效

## 关闭文章TOC目录结构

toc参数 默认true 只有设置为false时生效

## 关闭文章评论

comment参数 默认true 只有设置为false时生效

## 配置文章时效提示

- aging 表示是否开启该文章的时效提示
- aging_days 设置时效提示的天数

## 数学公式

mathjax参数, 设置为true生效  
例如:

## 测试集成GitHub Actions

测试 成功不成功！！！！！！！！！！！！！！！！！！！！！！

git add -A  
git commit -m “Create”  
git push -u origin master  
git add -A  
git commit -m “Updata”  
git pull  
测试删除.

## 测试代码

```python
print("测试代码")
async def fun()
    await asyncio.sleep(10)
```

## 未完待续……
