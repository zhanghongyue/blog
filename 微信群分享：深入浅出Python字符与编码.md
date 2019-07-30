---
title: 微信群分享：深入浅出Python字符与编码
date: 2017-03-22 15:25:25
tags: CSDN迁移
---
 版权声明：本文为博主原创文章，未经博主允许不得转载。 https://blog.csdn.net/mengyidan/article/details/80128690   
  Python语言是由Guido van Rossum大牛在1989年发明，它是当今世界最受欢迎的计算机编程语言之一，也是一门“学了有用、学了能用、学会能久用”的计算生态语言。

为此，CSDN作为国内最大的IT中文社区，特向广大Python爱好者开设了Python学习班，帮助大家在学习的道路上少走弯路，事半功倍。3月21号晚上8点，我们特邀请知名Python技术专家刘志军老师在班级里举行分享活动。


> 刘志军，6年开发经验，曾就职中兴、博雅互动。擅长Web技术架构，对爬虫、数据挖掘领域兴趣浓厚。目前在一家大型医药集团从事数据分析工作。微信公众号：Python之禅（vttalk）
> 
>  
以下为昨晚的分享内容：   
**为什么要聊这个话题呢？**   
 据说，每个做 Python 开发的都被字符编码的问题搞晕过，最常见的错误就是 UnicodeEncodeError、UnicodeDecodeError，你好像知道怎么解决，遗憾的是，错误又出现在其它地方，问题总是重蹈覆辙，str 到 unicode 之间的转换用 decode 还是 encode 方法还特不好记，老是混淆，问题究竟出在哪里？

为了弄清楚这个问题，我决定从 python 字符串的构成以及字符编码的细节上进行深入浅出的分析。   
**字节与字符**   
 计算机存储的一切数据，文本字符、图片、视频、音频、软件都是由一串01的字节序列构成的，一个字节等于8个比特位。

而字符就是一个符号，比如一个汉字、一个英文字母、一个数字、一个标点都可以称为一个字符。

字节方便存储和网络传输，而字符用于显示，方便阅读。例如字符 “p” 存储到硬盘是一串二进制数据 01110000，占用一个字节的长度。   
**编码与解码**   
 我们用编辑器打开的文本，看到的一个个字符，最终保存在磁盘的时候都是以二进制字节序列形式存起来的。那么从字符到字节的转换过程就叫做编码（encode），反过来叫做解码（decode），两者是一个可逆的过程。编码是为了存储传输，解码是为了方便显示阅读。

例如字符 “p” 经过编码处理保存到硬盘是一串二进制字节序列 01110000 ，占用一个字节的长度。字符 “禅” 有可能是以 “11100111 10100110 10000101” 占用3个字节的长度存储，为什么说是有可能呢？这个放到后面再说。

Python 的编码为什么那么蛋疼？当然，这不能怪开发者。

这是因为 Python2 使用 ASCII 字符编码作为默认编码方式，而 ASCII 不能处理中文。

为什么不用 UTf­8 呢？因为 Guido 老爹为 Python 编写第一行代码是在1989年的冬天，1991年2月正式开源发布了第一个版本，而 Unicode 是1991年10月发布的，也就是说 Python 这门语言创立的时候 UTF­8 还没诞生，这是其一。

这因为如此，Python 把字符串的类型搞成两种，unicode 和 str ，以至于开发者都在这里载了跟头，这是其二。

python3 彻底把字符串的实现重新改造了，只保留一种类型，这是后话，以后再说。   
**str与unicode**   
 Python2 把字符串分为两种类型，分别是 unicode 和 str。str 本质上是一串二进制字节序列，下面的示例代码可以看出 str 类型的 “禅” 打印出来是十六进制的 \xec\xf8 ，对应的二进制字节序列就是 ‘11101100 11111000’。


```
>>> s = '禅'

>>> s

'\xec\xf8'

>>> type(s) <type 'str'>
```
而unicode 类型的 u”禅” 对应的 unicode 符号是 u’\u7985’


```
>>>u = u"禅"

>>>u

u'\u7985'

>>> type(u) <type 'unicode'>
```
我们要把 unicode 符号保存文件或者传输到网络就需要经过编码处理转换成 str 类型，于是python 提供了 encode 方法，从 unicode 转换到 str，反之亦然。   
![图片描述](https://img-blog.csdn.net/20170322153013003?)

encode


```

>>>u = u"禅"

>>>u u'\u7985'

>>> u.encode("utf­8") '\xe7\xa6\x85'
```
decode


```
>>>s = "禅"

>>>s.decode("utf­8") u'\u7985'

>>>
```
不少初学者怎么也记不住 str 与 unicode 之间的转换用 encode 还是 decode，如果你记住了 str 本质上其实是一串二进制数据，而 unicode 是字符（符号），编码（encode）就是把字符（符号）转换为二进制数据的过程，因此 unicode 到 str 的转换要用 encode 方法，反过来就是用 decode 方法。

清楚了str与unicode之间的转换关系之后，我们来看看什么时候会出现UnicodeEncodeError、UnicodeDecodeError错误。   
**UnicodeEncodeError**   
 UnicodeEncodeError 发生在 unicode 字符串转换成 str 字节序列的时候，来看一个例子，把一串 unicode 字符串保存到文件。


```
# ­*­ coding:utf­8 ­*­

def main():

name = u'Python之禅'

f = open("output.txt", "w") f.write(name)
```
错误日志


> UnicodeEncodeError: ‘ascii’ codec can’t encode characters in position 6­7: ordinal not in range(128)   
>  为什么会出现 UnicodeEncodeError？
> 
>  
因为调用 write 方法时，Python 会先判断字符串是什么类型，如果是 str，就直接写入文件，不需要编码，因为 str 类型的字符串本身就是一串二进制的字节序列了。

如果字符串是 unicode 类型，那么它会先调用 encode 方法把 unicode 字符串转换成二进制形式的 str 类型，才保存到文件，而 encode 方法会使用 python 默认的 ascii 码来编码，相当于：


```
>>> u"Python之禅".encode("ascii")
```
但是，我们知道 ASCII 字符集中只包含了128个拉丁字母，不包括中文字符，因此出现了 ‘ascii’ codec can’t encode characters 的错误。要正确地 encode ，就必须指定一个包含了中文字符的字符集，比如：UTF­8、GBK。


```
>>>u"Python之禅".encode("utf­8") 'Python\xe4\xb9\x8b\xe7\xa6\x85'

>>>u"Python之禅".encode("gbk") 'Python\xd6\xae\xec\xf8'
```
所以要把 unicode 字符串正确地写入文件，就应该预先把字符串进行 UTF­8 或 GBK 编码转换。


```
def main():

name = u'Python之禅'

name = name.encode('utf­8')

with open("output.txt", "w") as f:

f.write(name)

```
当然，把 unicode 字符串正确地写入文件不止一种方式，但原理是一样的，这里不再介绍，把字符串写入数据库，传输到网络都是同样的原理。   
**UnicodeDecodeError**   
 UnicodeDecodeError 发生在 str 类型的字节序列解码成 unicode 类型的字符串时。


```
>>>a = u"禅"

>>>a u'\u7985'

>>>b = a.encode("utf­8")

>>>b

'\xe7\xa6\x85'

>>> b.decode("gbk")

Traceback (most recent call last): File "<stdin>", line 1, in <module>

UnicodeDecodeError: 'gbk' codec can't decode byte 0x85 in position 2: incomplete multibyte sequence
```
把一个经过 UTF­8 编码后生成的字节序列 ‘\xe7\xa6\x85’ 再用 GBK 解码转换成 unicode 字符串时，出现UnicodeDecodeError，因为（对于中文字符）GBK 编码只占用两个字节，而 UTF­8 占用3个字节，用 GBK 转换时，还多出一个字节，因此它没法解析。避免 UnicodeDecodeError 的关键是保持编码和解码时用的编码类型一致。

这也回答了文章开头说的字符 “禅”，保存到文件中有可能占3个字节，有可能占2个字节，具体处决于 encode 的时候指定的编码格式是什么。

再举一个UnicodeDecodeError的例子


```
 x = u"Python"

>>> y = "之禅"

>>> x + y

Traceback (most recent call last): File "<stdin>", line 1, in <module>

UnicodeDecodeError: 'ascii' codec can't decode byte 0xe4 in position 0: ordinal not in range(128)

>>>
```
str 与 unicode 字符串执行 + 操作是，Python 会把 str 类型的字节序列隐式地转换成（解码）成和 x 一样的 unicode 类型，但Python是使用默认的 ascii 编码来转换的，而 ASCII 中不包含中文，所以报错了。


```
>>> y.decode('ascii')

Traceback (most recent call last): File "<stdin>", line 1, in <module>

UnicodeDecodeError: 'ascii' codec can't decode byte 0xe4 in position 0: ordinal not in range(128)
```
正确地方式应该是显示地把y用UTF­8或者GBK进行解码。


```

>>>x = u"Python"

>>>y = "之禅"

>>>y = y.decode("utf­8")

>>>x + y u'Python\u4e4b\u7985'
```
以上内容都是基于 Python2 来讲的，关于 Python3 的字符和编码将会另开一个主题来说，保持关注。


--------
**CSDN Python学习班组织的【攒课】活动正在火热进行中，欢迎Python爱好者报名参与，点击==>>[Python爬虫入门与实践](http://www.bagevent.com/event/517724)**

   
  