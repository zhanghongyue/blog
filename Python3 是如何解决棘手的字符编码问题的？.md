---
title: Python3 是如何解决棘手的字符编码问题的？
date: 2017-03-29 10:32:54
tags: CSDN迁移
---
 版权声明：本文为博主原创文章，未经博主允许不得转载。 https://blog.csdn.net/mengyidan/article/details/80128782   
  > 本文来自微信公众号Python之禅（VTtalk）   
>  作者：刘志军
> 
>  


![图片描述](https://img-blog.csdn.net/20170329100905635?)  
题图：unsplash.com

Python3 最重要的一项改进之一就是解决了 Python2 中字符串与字符编码遗留下来的这个大坑。Python 编码为什么那么蛋疼？已经介绍过 Python2 字符串设计上的一些缺陷：   
 - 使用 ASCII 码作为默认编码方式，对中文处理很不友好。   
 - 把字符串牵强地分为 unicode 和 str 两种类型，误导开发者

当然这并不算Bug，只要处理的时候多留心也可以避免这些坑。但在 Python3两个问题都很好的解决了。

首先，Python3把系统默认编码设置为UTF-8


```
>>> import sys
>>> sys.getdefaultencoding()
'utf-8'
>>>
```
然后，文本字符和二进制数据区分得更清晰，分别用str和bytes表示。文本字符全部用str类型表示，str能表示Unicode字符集中所有字符，而二进制字节数据用一种全新的数据类型，用bytes来表示。

**str**


```
>>> a = "a"
>>> a
'a'
>>> type(a)
<class 'str'>

>>> b = "禅"
>>> b
'禅'
>>> type(b)
<class 'str'>
```
**bytes**

Python3 中，在字符引号前加‘b’，明确表示这是一个 bytes 类型的对象，实际上它就是一组二进制字节序列组成的数据，bytes 类型可以是 ASCII范围内的字符和其它十六进制形式的字符数据，但不能用中文等非ASCII字符表示。


```
>>> c = b'a'
>>> c
b'a'
>>> type(c)
<class 'bytes'>

>>> d = b'\xe7\xa6\x85'
>>> d
b'\xe7\xa6\x85'
>>> type(d)
<class 'bytes'>
>>>

>>> e = b'禅'
  File "<stdin>", line 1
SyntaxError: bytes can only contain ASCII literal characters.
```
bytes类型提供的操作和str一样，支持分片、索引、基本数值运算等操作。但是str与bytes类型的数据不能执行+操作，尽管在py2中是可行的。


```
>>> b"a"+b"c"
b'ac'
>>> b"a"*2
b'aa'
>>> b"abcdef\xd6"[1:]
b'bcdef\xd6'
>>> b"abcdef\xd6"[-1]
214

>>> b"a" + "b"
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: can't concat bytes to str
```
python2与python3字节与字符的对应关系   
![图片描述](https://img-blog.csdn.net/20170329102754271?)  
**encode 与 decode**

str与bytes之间的转换可以用encode和从decode方法。   
![图片描述](https://img-blog.csdn.net/20170329102851929?)  
 encode负责字符到字节的编码转换。默认使用UTF-8编码准换。


```
>>> s = "Python之禅"
>>> s.encode()
b'Python\xe4\xb9\x8b\xe7\xa6\x85'
>>> s.encode("gbk")
b'Python\xd6\xae\xec\xf8'
```
decode负责字节到字符的解码转换，通用使用UTF-8编码格式进行转换。


```
>>> b'Python\xe4\xb9\x8b\xe7\xa6\x85'.decode()
'Python之禅'
>>> b'Python\xd6\xae\xec\xf8'.decode("gbk")
'Python之禅'
```

--------
**攒课第二期如期而至，现在报名即可享受专属优惠！**   
![图片描述](https://img-blog.csdn.net/20170329103216080?)

   
  