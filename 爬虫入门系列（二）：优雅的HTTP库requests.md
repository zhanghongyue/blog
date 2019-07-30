---
title: 爬虫入门系列（二）：优雅的HTTP库requests
date: 2017-04-20 11:22:18
tags: CSDN迁移
---
 版权声明：本文为博主原创文章，未经博主允许不得转载。 https://blog.csdn.net/mengyidan/article/details/80129111   
  > 本文来自微信公众号Python之禅（VTalk），作者刘志军，已获作者授权，请勿二次转载！
> 
>  
读这篇文章前，强烈建议先阅读一下 [爬虫入门系列（一）：快速理解HTTP协议]() 。

Python 提供了很多模块来支持HTTP协议的网络编程，urllib、urllib2、urllib3、httplib、httplib2，都是和 HTTP 相关的模块，看名字觉得很反人类，更糟糕的是这些模块在Python2与Python3中有很大的差异，如果业务代码要同时兼容2和3，写起来会让人崩溃。

幸运地是，繁荣的Python社区给开发者带来了一个非常惊艳的HTTP库 requests，一个真正给人用的HTTP库。它是GitHUb关注数最多的 Python 项目之一，requests的作者是Kenneth Reitz大神。

requests实现了HTTP协议中绝大部分功能，它提供的功能包括 Keep-Alive、连接池、Cookie持久化、内容自动解压、HTTP代理、SSL认证、连接超时、Session等很多特性，最重要的是它同时兼容python2和 python3。

**快速入门**   
 requests 的安装可以直接使用pip方法：pip install requests


```
>>> import requests
# GET 请求
>>> response = requests.get("https://foofish.net")
```
请求返回Response对象，Response对象是对HTTP协议中服务端返回给浏览器的响应数据的封装，响应的中的主要元素包括：状态码、原因短语、响应首部、响应体等等，这些属性都封装在Response对象中。


```
# 状态码
>>> response.status_code
200

# 原因短语
>>> response.reason
'OK'

# 响应首部
>>> for name,value in response.headers.items():
...     print("%s:%s" % (name, value))
...
Content-Encoding:gzip
Server:nginx/1.10.2
Date:Thu, 06 Apr 2017 16:28:01 GMT

# 响应内容
>>> response.content

'<html><body>此处省略一万字...</body></html>

```
requests 除了支持 GET 请求外，还支持 HTTP 规范中的其它所有方法，包括 POST、PUT、DELTET、HEADT、OPTIONS方法。


```
>>> r = requests.post('http://httpbin.org/post', data = {'key':'value'})
>>> r = requests.put('http://httpbin.org/put', data = {'key':'value'})
>>> r = requests.delete('http://httpbin.org/delete')
>>> r = requests.head('http://httpbin.org/get')
>>> r = requests.options('http://httpbin.org/get')
```
**构建请求查询参数**

很多URL都带有很长一串参数，我们称这些参数为URL的查询参数，用”?”附加在URL链接后面，多个参数之间用”&”隔开，比如：[http://fav.foofish.net/?p=4&s=20](http://fav.foofish.net/?p=4&amp;s=20) ，现在你可以用字典来构建查询参数：


```
>>> args = {"p": 4, "s": 20}
>>> response = requests.get("http://fav.foofish.net", params = args)
>>> response.url
'http://fav.foofish.net/?p=4&s=2'
```
**构建请求首部 Headers**

requests可以很简单地指定请求首部字段Headers，比如有时要指定 User-Agent伪装成浏览器发送请求，以此来蒙骗服务器。直接传递一个字典对象给参数headers即可。


```
>>> r = requests.get(url, headers={'user-agent': 'Mozilla/5.0'})
```
**构建POST请求数据**

requests 可以非常灵活地构建 POST 请求需要的数据，如果服务器要求发送的数据是表单数据，则可以指定关键字参数 data，如果要求传递 json 格式字符串参数，则可以使用json关键字参数，参数的值都可以字典的形式传过去。

作为表单数据传输给服务器


```
>>> payload = {'key1': 'value1', 'key2': 'value2'}
>>> r = requests.post("http://httpbin.org/post", data=payload)
```
作为json格式的字符串格式传输给服务器。


```
>>> import json
>>> url = 'http://httpbin.org/post'
>>> payload = {'some': 'data'}
>>> r = requests.post(url, json=payload)
```
**Response中的响应体**

HTTP返回的响应消息中很重要的一部分内容是响应体，响应体在 requests 中处理非常灵活，与响应体相关的属性有：content、text、json()。

content 是 byte 类型，适合直接将内容保存到文件系统或者传输到网络中


```
>>> r = requests.get("https://pic1.zhimg.com/v2-2e92ebadb4a967829dcd7d05908ccab0_b.jpg")
>>> type(r.content)
<class 'bytes'>
# 另存为 test.jpg
>>> with open("test.jpg", "wb") as f:
...     f.write(r.content)
```
text是str类型，比如一个普通的HTML页面，需要对文本进一步分析时，使用text。


```
>>> r = requests.get("https://foofish.net/understand-http.html")
>>> type(r.text)
<class 'str'>
>>> re.compile('xxx').findall(r.text)
```
如果使用第三方开放平台或者API接口爬取数据时，返回的内容是json格式的数据时，那么可以直接使用json()方法返回一个经过json.loads()处理后的对象。


```
>>> r = requests.get('https://www.v2ex.com/api/topics/hot.json')
>>> r.json()
[{'id': 352833, 'title': '在长沙，父母同住...
```
**代理设置**

当爬虫频繁地对服务器进行抓取内容时，很容易被服务器屏蔽掉，因此要想继续顺利的进行爬取数据，使用代理是明智的选择。如果你想爬取墙外的数据，同样设置代理可以解决问题，requests 完美支持代理。


```
import requests

proxies = {
  'http': 'http://10.10.1.10:3128',
  'https': 'http://10.10.1.10:1080',
}

requests.get('http://example.org', proxies=proxies)
```
**超时设置**

requests 发送请求时，默认请求下线程一直阻塞，直到有响应返回才处理后面的逻辑。如果遇到服务器没有响应的情况时，问题就变得很严重了，它将导致整个应用程序一直处于阻塞状态而没法处理其他请求。


```
>>> import requests
>>> r = requests.get("http://www.google.coma")
...一直阻塞中
```
正确的方式的是给每个请求显示地指定一个超时时间。


```
>>> r = requests.get("http://www.google.coma", timeout=5)
5秒后报错
Traceback (most recent call last):
socket.timeout: timed out
```
**Session**

在[爬虫入门系列（一）：快速理解HTTP协议]()中介绍过HTTP协议是一中无状态的协议，为了维持客户端与服务器之间的通信状态，使用 Cookie 技术使之保持双方的通信状态。

有些网页是需要登录才能进行爬虫操作的，而登录的原理就是浏览器首次通过用户名密码登录之后，服务器给客户端发送一个随机的Cookie，下次浏览器请求其它页面时，就把刚才的 cookie 随着请求一起发送给服务器，这样服务器就知道该用户已经是登录用户。


```
import requests
# 构建会话
session  = requests.Session()
#　登录url
session.post(login_url, data={username, password})
#　登录后才能访问的url
r = session.get(home_url)
session.close()
```
构建一个session会话之后，客户端第一次发起请求登录账户，服务器自动把cookie信息保存在session对象中，发起第二次请求时requests 自动把session中的cookie信息发送给服务器，使之保持通信状态。

**项目实战**

最后是一个实战项目，如何用 requests 实现知乎自动登录并给用户发私信，我会在下一篇文章中进行讲解。

延伸阅读：


  * [Python实现知乎自动登录：](https://foofish.net/python-auto-login-zhihu.html) 
--------
在5月4日，我们特邀请刘志军老师开设Python爬虫入门实践提高篇，主要围绕：   
 1. 爬虫的基本原理   
 2. 爬虫的学习路线   
 3. 如何入门爬虫   
 4. 反反爬虫的常见策略   
 5. 爬虫框架Scrapy介绍   
 6. 如何进行大规模爬虫直播   
 六大主题展开，带你玩转Python爬虫，点击报名：[http://www.bagevent.com/event/517724](http://www.bagevent.com/event/517724)   
![图片描述](https://img-blog.csdn.net/20170420112334588?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbWVuZ3lpZGFu/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

   
  