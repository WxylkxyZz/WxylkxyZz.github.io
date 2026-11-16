---
title: "Vps搭建系列"
date: 2023-04-13 21:50:55
categories:
  - "随笔"
tags:
  - "VPS"
aging: true
aging_days: 30
---

总有一些时候，你想要一台自己的 VPS 。

<!--more-->

### 网络通信基本过程

略

### GFW防火墙

GFW防火墙是怎么阻断我们和谷歌的连接？  
如何绕过防火墙的拦截？  
DNS污染是怎么回事？搭建节点之前必须知道的知识

#### DNS污染(DNS劫持)

这里假设GFW没有拦截你的DNS请求

- 访问google
- 查找本地host缓存
  - 找到: 向直接构造请求(先假设找不到)
  - 没找到: 向dns服务器发送请求(假设GFW没有拦截你的dns请求) -> 跳跳跳跳到DNS服务器返回给你google的ip地址 -> 返回数据 -> 走到GFW解析到你的应用层意图 想访问google 修改你的数据 -> 返回给你一个假的ip -> pass

#### TCP重置攻击

假设你直接在host中设置了 google的ip跟域名的映射 不需要在进行上面的dns请求  
goole可以有多个服务器也就是多个ip地址

- 访问google
- 查找本地host缓存
  - 找到
    - 访问google -> 建立tcp连接
      - GFW只要解析到网络层就可以看到你请求的ip地址, 如果在黑名单里这个ip地址 -> 直接pass掉你这数据包/伪装成谷歌的服务器 给你返回错误的数据 —> tcp连接失败
      - 如果不在黑名单 -> 成功建立tcp连接 -> 开始给google的ip请求首页数据 -> GFW解析到你的应用层数据，请求google.com的首页数据, google.com域名在黑名单 -> 直接pass掉这个数据包
