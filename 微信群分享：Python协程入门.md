---
title: 微信群分享：Python协程入门
date: 2017-03-24 15:13:56
tags: CSDN迁移
---
 版权声明：本文为博主原创文章，未经博主允许不得转载。 https://blog.csdn.net/mengyidan/article/details/80128729   
  Python语言是由Guido van Rossum大牛在1989年发明，它是当今世界最受欢迎的计算机编程语言之一，也是一门“学了有用、学了能用、学会能久用”的计算生态语言。

为此，CSDN作为国内最大的IT中文社区，特向广大Python爱好者开设了Python学习班，帮助大家在学习的道路上少走弯路，事半功倍。3月16号晚上8点，我们特邀请知名Python技术专家陈舸老师在班级里举行分享活动。


> 陈舸，8年开发经验，曾就职华为、烽火通信，目前创业中。技术涉猎广泛，嵌入式开发，Linux，Python，iOS，Web均有涉及。《Python Cookbook第三版》译者，《Linux/Unix系统编程手册 下卷》以及《算法精解 C语言描述》合作译者。
> 
>  
以下为昨晚的分享内容：   
 大家好，今天给大家介绍Python中的协程(coroutine)，让大家对协程能有一个基本的认识。本文将从迭代器、生成器的基础讲起，通过生成器实现协程，最后将简单介绍Python3.5中新增关键字async/await对协程的支持。本次分享中的代码示例如不加特别说明将兼容Python2和Python3。使用Python2的同学可以通过from **future** import print_function来导入print函数。


# 迭代器(iterator)

大家都知道Python里有一个for语句。我们可以用for来循环迭代一个序列。Python中可迭代的对象有很多，比如我们所熟悉的：

**迭代列表**


```
for x in [1,2,3,4,5]:
    print(x)
```
**迭代字典**


```
languages = {'Java':'James Gosling', 'Python':'Guido van Rossum'}
for lang, author in languages.items():
    print("{0} create {1}".format(author, lang))
```
**迭代文本**


```
for line in open("logfile.log"):
    print(line)
```
**迭代字符串**


```
for char in "hello world":
    print(char)
```
为什么可以迭代许多不同的对象呢？因为Python中存在着一个迭代协议。我们再来看看这个例子：


```
>>> items = [1, 2, 3]
>>> it = iter(items)
>>> it.next()
1
>>> it.next()
2
>>> it.next()
3
>>> it.next()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
```
这一次我们没有通过for来迭代列表items，而是先通过Python的内置函数iter将列表items转换成一个迭代器，然后不断调用迭代器的next()方法来得到序列中的值，当超出可迭代范围时，就抛出了StopIteration异常。

因此对于如下的for循环


```
alert("Hellofor x in iterableObj:
    print(x) 
```
Python解释器是这样为我们处理的:


```
_iter = iter(iterableObj)
while True:
    try:
        x = _iter.next()
    except StopIteration:
        break
    print(x)
```
所以，Python的迭代协议就是要求对象的**iter**()方法返回一个特殊的迭代器对象，并且该对象必须实现**next**()方法，并使用StopIteration异常来通知迭代的完成。满足上述要求的对象，我们就认为它是迭代器对象。这样，在for语句中迭代时，Python会自动为我们调用迭代器的next()方法。

也就是说，我们自定义的对象如果也想用在for语句中来迭代的话，只需要满足迭代协议的要求，实现**iter**()和**next**()方法，并在**next**()中捕获StopIteration异常就可以了。好啦，那我们就根据上述要求，自己实现一个迭代器对象吧。   
**自定义可迭代对象, 迭代器版**


```
class LowerLetters(object):
    def __init__(self):
        self.current = 'a'

    def __next__(self):
        if self.current > 'z':
            raise StopIteration

        result = self.current
        self.current = chr(ord(result)+1)
        return result

def __iter__(self):
    ''' 只需要返回self即可 '''
        return self

letters = LowerLetters()
for i in letters:
    print(i)  # 输出a-z的小写字母
```

# 生成器(generator)

刚刚说了迭代器，我们再来看看Python中的生成器。先看看怎么定义一个Python的生成器。


```
def countdown(n):
print("Counting down from", n)
while n > 0:
    yield n
    n -= 1
```
看起来和普通的Python函数并无什么区别，都是用def来定义函数而已啊。只是while循环中的那个yield好像不太熟，而且函数里没有出现过return。先不管那么多，我们调用一下上面这个函数看看。


```
>>> x = countdown(10)     # 注意，并没有打印出任何内容
>>> x
<generator object at 0x58490>
>>>
```
奇怪了，明明函数定义里有一个print打印啊，调用它居然没有打印信息出来，这说明函数就没有开始执行嘛。没错，查看x我们发现它是一个generator对象，也就是生成器对象。函数countdown调用之后，只是返回了一个生成器对象而已，函数中的语句并没有立刻开始执行。那怎么样才能让函数开始执行呢？我们在迭代器中讲过的next()又要登场了。

**注意**，Python2中生成器对象的next方法在Python3中更改为**next**   
 这里以Python3为示例，用Python2的同学需要用next()


```
x.__next__()
Counting down from 10
10
>>>
>>> x.__next__()
9
>>> x.__next__()
8
...
>>> x.__next__()
1
>>> x.__next__()
Traceback (most recent call last):
File "<stdin>", line 1, in ?
StopIteration
>>>
```
通过对生成器对象x调用**next**()方法，我们发现countdown函数体中的语句终于开始执行了，而且还伴随着一个现象，那就是每次执行next()，函数就在yield语句处返回一个值，然后就停住不动了，直到下一次调用**next**()时才会又继续执行下去，如此往复,直到函数返回。

刚刚我们看到抛出StopIteration异常了，此时函数countdown已经返回了。这里是不是有似曾相似的感觉？之前讲的迭代器里，也有StopIteration异常，也出现了next。那到底迭代器和生成器有什么区别呢？

如果我们查看生成器x所包含的方法：


```
>>> dir(x)
['__class__', '__del__', '__delattr__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__iter__', '__le__', '__lt__', '__name__', '__ne__', '__new__', '__next__', '__qualname__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', 'close', 'gi_code', 'gi_frame', 'gi_running', 'gi_yieldfrom', 'send', 'throw']
>>> '__iter__' in dir(x)
True
>>> '__next__' in dir(x)
True
```
我们发现生成器对象中也包含有**iter**()和**next**()方法! 那么根据Python中的迭代协议，可以得知生成器其实也是一种迭代器。既然它们本质上都是迭代器，那生成器的好处体现在哪里了呢？还记得我们在讲迭代器时实现的那个自定义类LowerLetters吗？为了满足Python迭代协议，我们分别实现了**iter**()和**next**()方法，并在**next**()中处理StopIteration异常。可是你再看看我们的生成器函数countdown，我们搞了那么多东西吗？没有！因为这里Python自动帮我们搞定了迭代协议，简单多了。让我们用生成器重新定义一个LowerLetter。

**自定义可迭代对象，生成器版**


```
def lowerLetter():
lowerLetter.current = 'a'
while lowerLetter.current <= 'z':
    result = lowerLetter.current
    lowerLetter.current = chr(ord(result)+1)
    yield result   #用yield产生值

for i in lowerLetter():
...     print(i)  # 输出a-z
```
可以看到，相比迭代器版本，生成器版本的实现简洁多了，一个magic方法都不涉及，你甚至都不需要写一个类。生成器可以用来简化实现迭代器对象。

迭代器和生成器都有一种特质，它们都可迭代（iterable），但是只可以迭代一轮，一旦迭代结束，所有产生的值都不会保存，如果你要再次迭代，那么需要重新调用迭代器/生成器一次。这和Python内置的列表等数据结构有很大不同，我们知道，一个list你是可以反复迭代多次的。


```
>>> x = countdown(10)
>>> for v in x:
...     print(v)
... 
Counting down from 10
10
...
1
>>> for v in x:
...     print(v)
... 
>>> #再次迭代不会产生任何值了
```
看到了吗，第一轮for结束后，再次迭代就不会产生任何值了，因为已经到StopIteration了。中间产生的值都是按需生成，每次由Python帮我们调用next()得到，不会保存起来。这么做的好处是体现了惰性求值，即，需要的时候再计算，而不是像list那样不管你要不要，我一次性全扔内存里。当迭代的序列较大时，生成器/迭代器相比list会显著减少内存的占用。而且，有时候如果无法预先知道要迭代的上限时，这时就只能用迭代器/生成器来解决了。

我们需要区分可迭代对象（iterable）和迭代器（iterator）。迭代器一定是可迭代对象，但可迭代对象不一定是迭代器，list就是最好的例子。list可迭代，但它不包含next方法，因此根据Python迭代协议，它不是迭代器。可以通过下列方法来判断对象是否是迭代器。


```
>>> from collections import Iterator
>>> isinstance(lowerLetter(), Iterator)
True
>>> isinstance([], Iterator)
False
>>> isinstance({}, Iterator)
False
```

# 协程（coroutine）

好了，前面扯了那么多，终于要到正题了。那么什么是协程呢？从名字上来看，协表示协作，协程就是互相协作的例程，也正对应了其英文称谓cooperative routine。 据Donald Knuth所说，其实协程的概念早在1958年就由Melvin Conway提出了，而第一本介绍协程的出版物则在1963年出现。它到底是什么？协程和我们前面提到的迭代器、生成器有什么关系吗？

还记得前面讲生成器时给出的例子吧，我们用到了yield这个关键字。函数中出现了yield，使得函数不再是普通的函数，而变成了生成器。yield不但可以产生值，还会使得生成器保存执行上下文后暂停执行，然后在下一次next()时从上次暂停的地方继续接着执行。正是由于这一特点，使得Python中的生成器具有了协程的部分特征（可自我暂停，稍后再恢复执行）。而自从Python2.5开始，通过PEP 342 – Coroutines via Enhanced Generators的引入，Python终于可以通过生成器来实现协程了。

Python2.5中为生成器对象增加了send()和close()方法，并且支持了yield表达式。这有什么用处呢？我们还是先看一个简单的例子。

简单的字符串匹配, 若发送过来的字符串中包含pattern则打印出来


```
def grep(pattern):
print("Looking for %s" % pattern)
while True:
    line = (yield)  # yield表达式. 注意和之前例子中yield的写法做比较
    if pattern in line:
        print(line)
```
OK，我们来分析一下上面的代码。首先，因为出现了yield，grep不再是普通的函数了，调用它将产生一个生成器对象，这和我们之前讲过的没有区别。


```
>>> g = grep("python")
>>> g
<generator object grep at 0x106620cd0>
>>> 
```
由于g是生成器对象，函数体不会立刻执行。需要先调用一次**next**()，Python2中则是调用next()。


```
>>> g.__next__()
Looking for python
```
现在函数体开始执行了，然后在yield处暂停，这也和我们之前讨论的行为是一致的。   
 我们再来看看line = (yield)，这个时候yield写在了=的右边，使其成为了一个yield表达式。可以理解为通过yield得到了某个值然后赋值给了line。那这个值是什么呢？又是如何通过yield得到这个值的呢？

这里就是send()方法开始显现威力的时候了。我们可以对生成器对象g调用send方法，发送数据给它，而发送的数据就通过yield得到并赋值给了line。


```
>>> g.send('I like python')
I like python  #匹配了pattern, 打印出来
>>> g.send('this is a test')
>>> # 没有匹配pattern, 无打印
```
OK，我们再对grep的整个执行流程来一次梳理。g = grep(“python”)产生了一个生成器对象，此时函数体没有开始执行。我们先对g调用一次**next**()或者通过g.send(None)来启动生成器，这时函数体开始执行，到line = (yield)这一句时暂停执行。接下来，通过g.send(“I like python”)发送数据给g，函数体又恢复了执行，此时发送过来的字符串”I like python”就通过yield赋值给了line，然后继续执行后面的判断逻辑，发现匹配到了pattern，打印出了字符串。然后继续while循环，又遇到了yield，此时函数的执行再次暂停。如此反复通过send，函数体就不断的yield出新值处理一下，然后再次在yield处暂停。

发现什么了吗？这里的grep实际上就和我们的主程序通过send调用形成了一种协作式的处理流程。主程序通过调用g.send()唤起grep函数体的执行，grep函数体处理完后又在yield处自己暂停执行，等待主程序再次通过send唤起自己，有一种你方唱罢我登场的感觉。这正体现出了协程的特点：任务的执行流可自我挂起，其他程序又可以唤起任务，让它继续执行。   
 如果主程序调用了g.close()会如何呢？简单，那样的话grep协程就彻底退出了，不再是挂起。


# 协程的管道式处理

我们再来看一个稍复杂一点的例子。在这个例子里我们会说明生成器和协程的区别。在给出具体的代码前，我们先定义一个帮助函数。


```
def coroutine(func):
def start(*args, **kwargs):
    cr = func(*args, **kwargs)
    cr.send(None)  # 自动帮我们启动协程, 让其在yield处挂起
    return cr
return start
```
有经验的同学应该能意识到，这个函数是用来做装饰器的。还记得之前我们定义好生成器之后，要让生成器的函数体得到执行必须要先调用send(None)或者**next**()吗？这是为了先启动生成器让它在yield处挂起。而这个启动的步骤有可能会忘记做，那么我们就用装饰器来帮我们自动处理这个启动的步骤。

接下来定义两个函数。


```
import time
def follow(thefile, target):
thefile.seek(0, 2)  # go to the end of the file
while True:
    line = thefile.readline()  # 数据源，这里得到文本行
    if not line:
        time.sleep(0.1)
        continue
    target.send(line)           # 发送给协程target

@coroutine
def printer():
while True:
    line = (yield)
    print(line)
```
把上面两个函数组合起来使用


```
f = open("somefile.txt")
follow(f, printer())
```
![图片描述](https://img-blog.csdn.net/20170324150046005?)  
 用一个示意图来表示上面代码的流程。实际上我们将follow和printer组成了一个管道。follow是管道的源头，它负责产生数据，然后通过send把数据发给了协程printer做处理。   
 还可以在管道中增加协程吗？当然可以，我们就把前面的grep拿过来改改，再放在管道中。现在变成了这样：


```
@coroutine
def grep(pattern,target):
    while True:
        line = (yield)          # 从数据源得到数据
        if pattern in line:
            target.send(line)   # 自己处理完再发给下一个协程继续处理



f = open("access-log")
follow(f, grep('python', printer()))
```
![图片描述](https://img-blog.csdn.net/20170324150153099?)  
 这里follow仍当做管道的源头，是数据源，驱动整个管道的运行。grep协程在这里起到了过滤的作用，查看follow发过来的数据中是否包含有字符串python，若匹配到了就把数据发给下一个协程printer打印出来，然后自己在yield处挂起等待下一次follow的send调用。这里的follow(),grep()和printer()一起协同工作，实现了查看log文件中是否包含有特定字符串的功能。当然你还可以继续扩展这个程序完成更多的功能。

看到这里，熟悉Unix/Linux的同学应该倍感亲切啊。这和Unix/Linux的哲学很相似：提供一组简单的工具，每个工具只处理一种任务，但你可以通过各种不同的组合将它们连在一起处理更复杂的任务。其实这个简单的例子也可以用更一般化的迭代来处理：


```
def do_process_in_one_func(thefile, pattern):
thefile.seek(0, 2)  # go to the end of the file
while True:
    line = thefile.readline()
    if not line:
        time.sleep(0.1)
        continue
    else:
        if pattern in line:
            print(line)
```
我们稍微修改了一下例子，把所有的处理逻辑都放在一个函数里，通过迭代文件的每一行来达到相同的目的。只是这样一来就把读取文件行、过滤、打印三种不同的任务都写在了一起，这其实不利于任务的划分，而且这个函数也没法再和其他的工具一起协作了，丧失了灵活性。也就是说，协程其实可以帮助我们划分程序模块，使得每个任务变得简单单一，同时也能够和其他的程序协作，提高复用性。越是复杂的程序，这么做就越有利。

下面该说说生成器和协程的区别了。到这里估计很多同学把生成器和协程搞混了，这两货也确实很相似，因为都包含有yield。生成器我们可以看做是生产者，它负责产生数据，通常是用来做迭代使用的。而协程是消费者，通过yield接收其他程序send给它的数据然后处理。那协程有没有可能也是生产者呢？有可能哦，上面例子里的grep就有双重身份，它既处理（消费）管道上游发来的数据，而处理完之后又通过调用下游协程的send方法把处理过的数据发给下游（生产）。因此协程一定会消费，如果看不到消费而只有生产，那么是生成器，否则就是协程。好在Python 3.5引入的新关键字async/await，彻底解放了我们，这个我们稍后再谈。


# 用协程来模拟任务,实现用户态线程

由于协程具有自我挂起（不能被其他协程抢占控制权，除非自己放弃）稍后再恢复执行的特质，我们可以利用协程来模拟任务，进而实现用户态线程。为什么说是用户态线程呢？因为协程的调度执行完全在用户空间，操作系统内核不感知。也就是说，协程的调度需要由我们自己来控制。相比系统级的线程和进程，协程占用的资源极少，任务的切换也不需要内核来调度，省去了上下文切换的开销。这样的特性使得单机创建大量协程成为可能（百万级）。下面的例子我们就来实现一个简单的用户态调度器，并用协程来模拟操作系统中的任务。


```
from queue import Queue
```
**模拟任务**


```
class Task(object):
taskid = 0
def __init__(self, target):
    Task.taskid += 1
    self.tid = Task.taskid
    self.target = target
    self.sendval = None

def run(self):
    return self.target.send(self.sendval)
```
**调度器类**, mainloop方法实现了简单的eventloop


```
class Scheduler(object):
def __init__(self):
    self.ready = Queue()
    self.taskmap = {}

def new(self, target):
    newtask = Task(target)
    self.taskmap[newtask.tid] = newtask
    self.schedule(newtask)
    return newtask.tid

def exit(self, task):
    print("Task %d terminated" % task.tid)
    del self.taskmap[task.tid]

def schedule(self, task):
    self.ready.put(task)

def mainloop(self):
    while self.taskmap:
        task = self.ready.get()
        try:
            result = task.run()
            if isinstance(result, SystemCall):
                result.task = task
                result.sched = self
                result.handle()
                continue
        except StopIteration:
            self.exit(task)
            continue
        self.schedult(task)
```
**系统调用基类**


```
class SystemCall(object): 
def handle(self):
    pass
```
获取任务id的系统调用


```
class GetTid(SystemCall):
def handle(self):
    self.task.sendval = self.task.tid
    self.sched.schedule(self.task)
```
定义两个协程, 将作为我们的任务由调度器调度执行


```
def foo():
mytid = yield GetTid()
print("I am foo", mytid)
yield

def bar():
mytid = yield GetTid()
print("I am bar", mytid)
yield

if __name__ == '__main__':
sched = Scheduler()
```
循环一百万次, 共创建两百万个任务


```
for task in range(1000000):
    sched.new(foo())
    sched.new(bar())
sched.mainloop()
```
上面这个程序一共创建了两百万个任务，通过我们简单实现的事件循环不断交错调度执行（完全没有用到线程，当然除了程序自身的主线程之外）。程序大部分时间花在创建任务上了，真正执行的时候是非常快的。看到这里，有的同学会问了，如果要用协程，那岂不是还要我们自己写调度程序，而实现一个功能完备的事件循环并不是人人都能轻松完成的啊？由操作系统调度系统原生的线程、进程不是更方便吗？没错，的确是这样，由于协程完全处于用户态，要做到互相交错执行，的确需要把OS的调度功能移到用户态来完成，好在许多优秀的库（如gevent，以及Python3.4中加入标准库的asynio）已经帮我们完成了这些任务，真正要用的时候并不需要我们自己完成，这里仅仅只是一个简单的例子用以说明。学从难处学，用从易处用。

总结一下，目前业界用来处理高并发的方案一般有两种：   
 1.以Node.js为代表的异步回调方案。Python的Twisted网络库也是同样的思路。这种方案利用事件循环、非阻塞IO和异步回调机制。简单来说就是，每当遇到IO或者其他耗时的操作时，注册一个回调到事件循环中，这时程序接着干其他的事情，当IO完成后由事件循环回调我们之前注册的callback。这种方式让程序尽可能的执行，而不需要我们自己创建其他线程。   
 这种方式的缺点是容易遇到callback hell，回调套回调。因为所有的阻塞操作都必须是异步的，否则只要有一环阻塞，系统就卡死了。另外就是异步的方式有些违反人类的思维习惯，人类还是习惯用同步的方式来思考。当然，针对这些问题，现在也已经有了比较好的解决方法，这里就不多说了。

2.协程。协程似乎天生就适合于处理这类问题（IO密集型，程序大部分时间在等待IO变得可用，但实际处理的任务相对简单，并不会占用大量CPU资源）。协程是运行在用户态的轻量级线程，资源占用极少，因此单机创建大量的协程成为了可能。而且协程的任务切换完全在用户空间解决，无需操作系统内核干预，大量减少了因为任务切换而产生的调度开销。

本文仅介绍Python的协程，关于异步IO以及高并发编程等主题，这里就不适合深入讨论了。


# Python 3.5对协程的支持

看到这里的同学应该已经对Python的协程有了较清晰的认识了，但可能还是有一件事觉得不爽。Python语言一向号称简洁优美，代码可读性高，可是刚刚讲了这么多，Python的协程功能居然从2.5版本后才可以通过生成器变相实现出来，有时候必须读代码才能搞清楚到底是生成器还是协程（其实在语言层面上Python并不认识什么协程，我们只是用生成器实现了编程概念中的协程。可以说，在Python3.5之前，语言原生不支持协程，我们前面看到的只是利用生成器实现了协程的功能）。如果能像Go语言定义goroutine（可简单理解为Go语言对协程的实现）那样简单就好了，不然怎么也对不起Python简洁优美易读的美誉啊。Python 3.5中新增的关键字async/await解决了这个“痛点”。   
 Python3.5中定义协程的方式


```
async def native_coroutine():
await awaitableObj
```
现在好了，凡是由async def定义的函数，在Python 3.5中将被认为是原生协程（虽然在Python解释器中仍然是利用生成器来实现的）。


```
>>> async def native_coro():
...     pass
... 
>>> a = native_coro()
>>> a #a现在是一个coroutine对象了！
<coroutine object native_coro at 0x109893678>
>>> 
```
而由async def定义的协程中将不再支持使用yield了，否则会被认为是语法错误。取而代之的是await，await表达式用于获得协程执行的结果。只是await只可以接受awaitable对象。具体的语法细节和规则请参考PEP 492：Coroutines with async and await syntax，   
 这里也有一篇中文翻译，读一读可以了解到Python社区为什么要做出这样的改变，我这里就不再赘述了，官方文档永远是最好的学习资料。


# 参考资料

[Sequences and   
 Coroutines](http://wla.berkeley.edu/~cs61a/fa11/lectures/streams.html)   
[A Curious Course on Coroutines and   
 Concurrency](http://www.dabeaz.com/coroutines/)   
[维基百科-Coroutine](https://en.wikipedia.org/wiki/Coroutine) [How the   
 heck does async await work in   
 Python3.5](https://snarky.ca/how-the-heck-does-async-await-work-in-python-3-5/)   
[PEP 492 – Coroutines with async and await   
 syntax](https://www.python.org/dev/peps/pep-0492/) [PEP   
 492中文翻译](http://www.cnblogs.com/animalize/p/4738941.html)   
[并发之痛Thread,Goroutine,Actor](http://jolestar.com/parallel-programming-model-thread-goroutine-actor/)


--------
欢迎学习Python语言，热爱交流技术的同学加入我们的CSDN Python学习班。入群请扫下方群二维码。

目前群已满，请扫描下面的小助手账号，备注：Python 申请入群   
![图片描述](https://img-blog.csdn.net/20170322152404038?)

让我们一起来攒一堂课吧！   
[【攒课】Python数据分析实战：泰坦尼克号之灾与机器学习算法>>点击报名](http://www.bagevent.com/event/463797)！   
![图片描述](https://img-blog.csdn.net/20170324173544034?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbWVuZ3lpZGFu/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

   
  