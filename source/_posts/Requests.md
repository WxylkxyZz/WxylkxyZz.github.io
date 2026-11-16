---
title: "Requests"
date: 2023-12-30 16:24:22
categories:
  - "Python"
tags:
  - "requests"
aging: true
aging_days: 30
---

# requests系列小知识点

<!--more-->

1. `url`参数编码和解码

```python
from urllib.parse import quote, unquote
 
encoded_string = "问题小记"
decoded_string = quote(encoded_string)
print(decoded_string)
 
encoded_string = "%E9%97%AE%E9%A2%98%E5%B0%8F%E8%AE%B0"
decoded_string = unquote(encoded_string)
print(decoded_string)
```

## 请求发送

### 请求头

| Host | (主机和端口号) |
| --- | --- |
| Connection | (链接类型) |
| Upgrade-Insecure-Requests | (升级为HTTPS请求) |
| User-Agent | (浏览器名称) |
| Accept | (传输文件类型) |
| Referer | (页面跳转处) |
| Accept-Encoding | （文件编解码格式） |
| Cookie | （Cookie） |
| x-requested-with | XMLHttpRequest (表示该请求是Ajax异步请求) |

### 请求方法

作用：让协议清楚的知道，客户端发送了一个什么类型的‘动作指令’

| 方 法 | 描 述 |
| :--- | --- |
| GET | 请求页面，并返回页面内容 |
| HEAD | 类似于 GET 请求，只不过返回的响应中没有具体的内容，用于获取报头 |
| POST | 大多用于提交表单或上传文件，数据包含在请求体中 |
| PUT | 从客户端向服务器传送的数据取代指定文档中的内容 |
| DELETE | 请求服务器删除指定的页面 |
| CONNECT | 把服务器当作跳板，让服务器代替客户端访问其他网页 |
| OPTIONS | 允许客户端查看服务器的性能 |
| TRACE | 回显服务器收到的请求，主要用于测试或诊断 |

### get

1. **url (必需参数):**
   - 用于指定请求的目标 URL。

```python
import requests
 
url = "https://www.example.com"
response = requests.get(url)
```

2. **params:**
   - 用于传递 URL 查询参数。可以是一个字典或一个字符串。

```python
payload = {'key1': 'value1', 'key2': 'value2'}
response = requests.get(url, params=payload)
```

3. **headers:**
   - 用于设置 HTTP 请求头，其中包含关于请求的信息，如用户代理、接受类型等。

```python
headers = {'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.3'}
response = requests.get(url, headers=headers)
```

4. **cookies:**
   - 用于传递 cookie。

```python
cookies = {'cookie_name': 'cookie_value'}
response = requests.get(url, cookies=cookies)
```

5. **auth:**
   - 用于设置 HTTP 认证信息，可以是元组 (username, password)。

```python
auth = ('username', 'password')
response = requests.get(url, auth=auth)
```

6. **timeout:**
   - 设置请求超时时间，以秒为单位。

```python
response = requests.get(url, timeout=5)
```

7. **allow_redirects:**
   - 指定是否允许重定向，默认为 `True`。

```python
response = requests.get(url, allow_redirects=False)
```

8. **proxies:**
   - 用于设置代理。

```python
proxies = {'http': 'http://proxy.example.com', 'https': 'https://proxy.example.com'}
response = requests.get(url, proxies=proxies)
```

9. **stream:**
   - 如果设置为 `True`，则会立即下载响应内容的一部分，并且可以通过迭代器逐步获取剩余的内容。

```python
response = requests.get(url, stream=True)
for chunk in response.iter_content(chunk_size=128):
    print(chunk)
```

10. **verify:**
    - 用于控制是否进行 SSL 证书验证。默认为 `True`，可以设置为 `False` 关闭验证，或者指定一个证书路径。

```python
response = requests.get(url, verify='/path/to/certfile.pem')
```

11. **cert:**
    - 用于指定客户端证书文件路径，通常是一个包含证书和私钥的元组。

```python
response = requests.get(url, cert=('/path/to/client.cert', '/path/to/client.key'))
```

12. **params和data:**
    - `params` 用于设置 URL 查询参数，而 `data` 用于设置请求体中的数据，适用于 POST 请求。

```python
payload = {'key1': 'value1', 'key2': 'value2'}
response = requests.get(url, params=payload)
 
# 或者在 POST 请求中使用 data 参数
data = {'key1': 'value1', 'key2': 'value2'}
response = requests.post(url, data=data)
```

13. **hooks:**
    - 用于自定义回调钩子，可以在请求的不同阶段执行自定义的逻辑，比如在发送请求之前或接收响应之后。

```python
def custom_hook(response, *args, __kwargs):
    print("Custom hook executed")
 
hooks = {'response': custom_hook}
response = requests.get(url, hooks=hooks)
```

14. **params和headers中的特殊字符处理:**
    - 如果 URL 中包含特殊字符，可以使用 `quote` 函数进行编码。

```python
from urllib.parse import quote
 
url = "https://www.example.com/search"
search_query = "hello world"
url_with_query = url + "?q=" + quote(search_query)
response = requests.get(url_with_query)
```

这些是一些常见和不太常见但有用的参数。在实际使用中，根据具体的需求，可以查阅 [requests 官方文档](https://docs.python-requests.org/en/latest/api/#requests.get) 获取更多详细信息。

### post

`requests.post()` 是用于发送 HTTP POST 请求的函数，与 `requests.get()` 类似，它也支持一系列参数来定制请求。以下是一些常见的 `requests.post()` 的参数及其说明：

1. **url (必需参数):**
   - 用于指定请求的目标 URL。

```python
import requests
 
url = "https://www.example.com"
response = requests.post(url)
```

2. **data:**
   - 用于传递请求体中的数据，通常用于表单提交。

```python
payload = {'key1': 'value1', 'key2': 'value2'}
response = requests.post(url, data=payload)
```

3. **json:**
   - 用于传递 JSON 数据，自动设置请求头的 Content-Type 为 application/json。

```python
import json
 
data = {'key1': 'value1', 'key2': 'value2'}
response = requests.post(url, json=data)
```

4. **headers:**
   - 用于设置 HTTP 请求头，其中包含关于请求的信息，如用户代理、接受类型等。

```python
headers = {'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.3'}
response = requests.post(url, headers=headers)
```

5. **cookies:**
   - 用于传递 cookie。

```python
cookies = {'cookie_name': 'cookie_value'}
response = requests.post(url, cookies=cookies)
```

6. **auth:**
   - 用于设置 HTTP 认证信息，可以是元组 (username, password)。

```python
auth = ('username', 'password')
response = requests.post(url, auth=auth)
```

7. **timeout:**
   - 设置请求超时时间，以秒为单位。

```python
response = requests.post(url, timeout=5)
```

8. **allow_redirects:**
   - 指定是否允许重定向，默认为 `True`。

```python
response = requests.post(url, allow_redirects=False)
```

9. **files:**
   - 用于上传文件，可以是一个包含文件名和文件内容的字典。

```python
files = {'file': ('filename.txt', open('filename.txt', 'rb'))}
response = requests.post(url, files=files)
```

10. **proxies:**
    - 用于设置代理。

```python
proxies = {'http': 'http://proxy.example.com', 'https': 'https://proxy.example.com'}
response = requests.post(url, proxies=proxies)
```

11. **stream:**
    - 如果设置为 `True`，则会立即下载响应内容的一部分，并且可以通过迭代器逐步获取剩余的内容。

```python
response = requests.post(url, stream=True)
for chunk in response.iter_content(chunk_size=128):
    print(chunk)
```

12. **verify:**
    - 用于控制是否进行 SSL 证书验证。默认为 `True`，可以设置为 `False` 关闭验证，或者指定一个证书路径。

```python
response = requests.post(url, verify='/path/to/certfile.pem')
```

13. **cert:**
    - 用于指定客户端证书文件路径，通常是一个包含证书和私钥的元组。

```python
response = requests.post(url, cert=('/path/to/client.cert', '/path/to/client.key'))
```

14. **params和headers中的特殊字符处理:**
    - 如果 URL 中包含特殊字符，可以使用 `quote` 函数进行编码。

```python
from urllib.parse import quote
 
url = "https://www.example.com/search"
search_query = "hello world"
url_with_query = url + "?q=" + quote(search_query)
response = requests.post(url_with_query)
```

这些是一些常见和不太常见但有用的参数。在实际使用中，根据具体的需求，可以查阅 [requests 官方文档](https://docs.python-requests.org/en/latest/api/#requests.post) 获取更多详细信息。

## 响应信息

| 属性/方法 | 描述 |
| --- | --- |
| apparent_encoding | 编码方式 |
| close() | 关闭与服务器的连接 |
| content | 返回响应的内容，以字节为单位 |
| cookies | 返回一个 CookieJar 对象，包含了从服务器发回的 cookie |
| elapsed | 返回一个 timedelta 对象，包含了从发送请求到响应到达之间经过的时间量，可以用于测试响应速度。比如 r.elapsed.microseconds 表示响应到达需要多少微秒。 |
| encoding | 解码 r.text 的编码方式 |
| headers | 返回响应头，字典格式 |
| history | 返回包含请求历史的响应对象列表（url） |
| is_permanent_redirect | 如果响应是永久重定向的 url，则返回 True，否则返回 False |
| is_redirect | 如果响应被重定向，则返回 True，否则返回 False |
| iter_content() | 迭代响应 |
| iter_lines() | 迭代响应的行 |
| json() | 返回结果的 JSON 对象 (结果需要以 JSON 格式编写的，否则会引发错误) |
| links | 返回响应的解析头链接 |
| next | 返回重定向链中下一个请求的 PreparedRequest 对象 |
| ok | 检查 “status_code” 的值，如果小于400，则返回 True，如果不小于 400，则返回 False |
| raise_for_status() | 如果发生错误，方法返回一个 HTTPError 对象 |
| reason | 响应状态的描述，比如 “Not Found” 或 “OK” |
| request | 返回请求此响应的请求对象 |
| status_code | 返回 http 的状态码，比如 404 和 200（200 是 OK，404 是 Not Found） |
| text | 返回响应的内容，unicode 类型数据 |
| url | 返回响应的 URL |

```plaintext
响应，由服务端返回给客户端，可以分为三部分：响应状态码（Response Status Code）、响应头 （Response Headers）和响应体（Response Body）。
```

1. 响应状态码

   响应状态码表示服务器的响应状态，如 200 代表服务器正常响应，404 代表页面未找到，500 代表服务器内部发生错误。在爬虫中，我们可以根据状态码来判断服务器响应状态，如状态码为 200，则证明成功返回数据，再进行进一步的处理，否则直接忽略。
2. 常见的状态码及其原因

   | 状态码 | 说 明 | 详 情 |
   | :--- | :---: | :---: |
   | 100 | 继续 | 请求者应当继续提出请求。服务器已收到请求的一部分，正在等待其余部分 |
   | 101 | 切换协议 | 请求者已要求服务器切换协议，服务器已确认并准备切换 |
   | 200 | 成功 | 服务器已成功处理了请求 |
   | 201 | 已创建 | 请求成功并且服务器创建了新的资源 |
   | 202 | 已接受 | 服务器已接受请求，但尚未处理 |
   | 203 | 非授权信息 | 服务器已成功处理了请求，但返回的信息可能来自另一个源 |
   | 204 | 无内容 | 服务器成功处理了请求，但没有返回任何内容 |
   | 205 | 重置内容 | 服务器成功处理了请求，内容被重置 |
   | 206 | 部分内容 | 服务器成功处理了部分请求 |
   | 300 | 多种选择 | 针对请求，服务器可执行多种操作 |
   | 301 | 永久移动 | 请求的网页已永久移动到新位置，即永久重定向 |
   | 302 | 临时移动 | 请求的网页暂时跳转到其他页面，即暂时重定向 |
   | 303 | 查看其他位置 | 如果原来的请求是 POST，重定向目标文档应该通过 GET 提取 |
   | 304 | 未修改 | 此次请求返回的网页未修改，继续使用上次的资源 |
   | 305 | 使用代理 | 请求者应该使用代理访问该网页 |
   | 307 | 临时重定向 | 请求的资源临时从其他位置响应 |
   | 400 | 错误请求 | 服务器无法解析该请求 |
   | 401 | 未授权 | 请求没有进行身份验证或验证未通过 |
   | 403 | 禁止访问 | 服务器拒绝此请求 |
   | 404 | 未找到 | 服务器找不到请求的网页 |
   | 405 | 方法禁用 | 服务器禁用了请求中指定的方法 |
   | 406 | 不接受 | 无法使用请求的内容响应请求的网页 |
   | 407 | 需要代理授权 | 请求者需要使用代理授权 |
   | 408 | 请求超时 | 服务器请求超时 |
   | 409 | 冲突 | 服务器在完成请求时发生冲突 |
   | 410 | 已删除 | 请求的资源已永久删除 |
   | 411 | 需要有效长度 | 服务器不接受不含有效内容长度标头字段的请求 |
   | 412 | 未满足前提条件 | 服务器未满足请求者在请求中设置的其中一个前提条件 |
   | 413 | 请求实体过大 | 请求实体过大，超出服务器的处理能力 |
   | 414 | 请求 URI 过长 | 请求网址过长，服务器无法处理 |
   | 415 | 不支持类型 | 请求格式不被请求页面支持 |
   | 416 | 请求范围不符 | 页面无法提供请求的范围 |
   | 417 | 未满足期望值 | 服务器未满足期望请求标头字段的要求 |
   | 500 | 服务器内部错误 | 服务器遇到错误，无法完成请求 |
   | 501 | 未实现 | 服务器不具备完成请求的功能 |
   | 502 | 错误网关 | 服务器作为网关或代理，从上游服务器收到无效响应 |
   | 503 | 服务不可用 | 服务器目前无法使用 |
   | 504 | 网关超时 | 服务器作为网关或代理，但是没有及时从上游服务器收到请求 |
   | 505 | HTTP 版本不支持 | 服务器不支持请求中所用的 HTTP 协议版本 |
3. 响应头

   响应头包含了服务器对请求的应答信息，如 Content-Type、Server、Set-Cookie 等。下面简要说明一

   些常用的头信息。

   - Date：标识响应产生的时间。
   - Last-Modified：指定资源的最后修改时间。
   - Content-Encoding：指定响应内容的编码。
   - Server：包含服务器的信息，比如名称、版本号等。
   - Content-Type：文档类型，指定返回的数据类型是什么，如 text/html 代表返回 HTML 文档，
   - application/x-javascript 则代表返回 JavaScript 文件，image/jpeg 则代表返回图片。
   - Set-Cookie：设置 Cookies。响应头中的 Set-Cookie 告诉浏览器需要将此内容放在 Cookies 中，下次请求携带 Cookies 请求。
   - Expires：指定响应的过期时间，可以使代理服务器或浏览器将加载的内容更新到缓存中。如果再次访问时，就可以直接从缓存中加载，降低服务器负载，缩短加载时间。

   ​
4. 响应体

   最重要的当属响应体的内容了。响应的正文数据都在响应体中，比如请求网页时，它的响应体就是网页的 HTML 代码；请求一张图片时，它的响应体就是图片的二进制数据。我们做爬虫请求网页后，要解析的内容就是响应体

   ![image]()

   ​

   ![image]()

   在浏览器开发者工具中点击 预览，就可以看到网页的源代码，也就是响应体的内容，它是解析的目标。在做爬虫时，我们主要通过响应体得到网页的源代码、JSON 数据等，然后从中做相应内容的提取。

   ​
