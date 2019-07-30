---
title: Docker 最初的2小时(Docker从入门到入门)
date: 2017-07-31 15:40:57
tags: CSDN迁移
---
 版权声明：本文为博主原创文章，未经博主允许不得转载。 https://blog.csdn.net/mengyidan/article/details/80130797   
  > 作者：宋宝华   
>  版权声明：本文为博主原创文章，未经博主允许不得转载。
> 
>  
最初的2小时，你会爱上Docker，对原理和使用流程有个最基本的理解，避免满世界无头苍蝇式找资料。本人反对暴风骤雨式多管齐下狂轰滥炸的学习方式，提倡迭代学习法，就是先知道怎么玩，有个感性认识，再深入学习高级用法，深层原理，一轮轮迭代。坚决反对一上来就搞几百页厚的东西把人脑子弄乱。

**Docker是什么？**

KVM, Virtualbox, Vmware是虚拟出机器，让每个实例看到一个单独的机器；而Docker是虚拟出操作系统，实现应用之间的隔离，让各个应用觉得自己有一个自己的操作系统，而且彼此之间隔离。假设没有Docker，然后有进程1和进程2，它们的运行将类似下图，进程1和进程2共享kernel，它们是同一OS下2个进程，因此必须拥有不同PID，但是又共享网卡，共享IP地址，看到一样的根文件系统（不chroot的情况下）等，可以用Linux IPC手段进程间通信。   
![图片描述](https://img-blog.csdn.net/20170731151122166?)  
 有Docker的情况下，假设进程1和进程2运行于不同的容器，那么进程1和进程2都觉得自己和对方没有半毛钱关系，都觉得自己拥有自己的根文件系统，自己的网卡等，然后进程1和进程2的PID还可以一样，比如假设2个都是100。但是，此100非彼100。   
![图片描述](https://img-blog.csdn.net/20170731151246947?)  
 Virtualbox等虚拟机的思路则完全不一样，如果进程1和进程2运行于不同的虚拟机，则操作系统都是双份的，它们感觉自己在不同的虚拟电脑上面跑。   
![图片描述](https://img-blog.csdn.net/20170731151341020?)  
 由于可见，Docker达到了类似虚拟机的效果，但是又没有虚拟机的开销，它虚拟的层次更加高。Docker不虚拟机器，仅仅虚拟应用的运行环境。

**为什么Docker也可以“虚拟化”？**

虚拟化，本质上一种虚幻，给你一种幻觉，让你觉得拥有的很多甚至拥有全世界，哪怕你实际是一只蝼蚁。

经过本人多年研究，虚拟化的技术分为2种，一种是虚拟一个世界，第二个是虚拟一个氛围。

比如我们在现实生活里面是个屌丝，但是在虚拟人生的游戏里面，我们可以是王思聪++，集美貌智慧财富正义于一生。虚拟人生的游戏，构建一个整个的新世界，这个世界，人人有房住，天下没有贼。那么这个就是硬件都变了，你的内核都变了。这个是Virtualbox，KVM这种虚拟出一个新世界的思路。

虚拟一个氛围，是Docker的做法。例如贵公司的Linux部门以前只有3,4个工程师，然后有一个manager，后来有30个人了，你就可以分什么内核组、驱动组、应用组等更多的组，然后又多出几个manager。这种组，类似于名称空间，在每一个单独名称空间的manager，都觉得自己是个manager，于是他会爽那么一点点。

最开始是这样的：   
![图片描述](https://img-blog.csdn.net/20170731151440027?)  
 后来是这样的：   
![图片描述](https://img-blog.csdn.net/20170731151606114?)  
 如果这样还不够，还可以搞声卡驱动组manager，网卡驱动组manager，反正可以不停地搞。大家在各自的Container里面占山为王。

Docker就是这样的名称空间让各自在同样的Linux平台上面各自暗爽，装到你自己的容器里面爽。

**安装Docker**

如果是Windows主机，可以下载docker-toolbox一路安装，安装过程中如果提示什么错，可以把360等类似软件关闭。Windows安装好Docker后，使用Docker Quickstart Terminal运行。   
![图片描述](https://img-blog.csdn.net/20170731151711324?)  
 如果是Ubuntu，可以按照[https://docs.docker.com/engine/installation/linux/ubuntu/](https://docs.docker.com/engine/installation/linux/ubuntu/)网页进行安装。最简单的Ubuntu 16.04就是命令：sudo apt-get update&& apt-get install docker。

Ubuntu安装Docker后，可以把当前用户加到Docker用户组以便当前用户也有权限操作Docker client和host之间的通信socket(之后请重启Docker相关服务)：


```
sudo usermod -aG docker $USER
```
为了装逼需要，我们在Docker Hub网页注册一个用户名，我注册的用户名是21cnbao。这样以后，就可以自己提交自己的image了。   
![图片描述](https://img-blog.csdn.net/20170731151859405?)  
**Docker的架构**

Docker中可能涉及到3个机器或者更多机器，一个运行Docker命令的client， 一个包含images并以容器(container)形式运行image的主机，一个Docker的images仓库。client与Docker host上面的Docker daemon通信。当然Docker client和host可以运行于一台机器（我们做实验的时候是一台），默认的Docker仓库是Docker Hub。   
![图片描述](https://img-blog.csdn.net/20170731152017779?)  
 一般的流程中,client发pull命令从仓库把image拉到Docker host，然后通过run命令指挥image到host上面弄一个container来跑这个image。

当然也可以是相反的流程，client 通过build命令在host上面创建一个自己的image，然后通过push命令把image推到仓库。之后这个image可以被别的人或者自己pull。

image到底是个什么鬼？

Docker镜像是一个特殊的文件系统，提供容器运行时所需的程序、库、资源、配置等文件外，还包含了一些为运行时准备的一些配置参数（如匿名卷、环境变量、用户等）。image为特定目的而生，比如弄了个nginx的image后，这个image就把nginx的东西包罗万象了，无论是张三、王五、六麻子还是七癞子，无论它是什么电脑，什么操作系统，只要支持Docker，它把这个nginx的image下载下来后，拿Docker run命令就可以弄容器跑nginx了。这样，用户就不用装nginx以及它依赖的一切包了（通常装一个软件弄依赖也能把你弄地烦躁死了）。这样看起来，Docker实在是居家旅行，杀人越货之必备良器也！

镜像构建时，会一层层叠加，前一层是后一层的基础。   
![图片描述](https://img-blog.csdn.net/20170731152347514?)  
 每一层构建完就不会再发生改变，后一层上的任何改变只发生在自己这一层。比如，删除前一层文件的操作，实际不是真的删除前一层的文件，而是仅在当前层标记为该文件已删除。所以这个很类似Git里面这一次提交相对于上一次提交的diff：


```
$git diff  
 layout/book_index_template.html                    |    8 ++-  
 text/05_Installing_Git/0_Source.markdown           |  14 ++++++  
 text/05_Installing_Git/1_Linux.markdown            |  17 +++++++  
 text/05_Installing_Git/2_Mac_104.markdown          |  11 +++++  
 text/05_Installing_Git/3_Mac_105.markdown          |   8 ++++  
 text/05_Installing_Git/4_Windows.markdown          |   7 +++  
 .../1_Getting_a_Git_Repo.markdown                  |    7 +++-  
 .../0_Comparing_Commits_Git_Diff.markdown        |   45 +++++++++++++++++++-  
 .../0_Hosting_Git_gitweb_repoorcz_github.markdown |   4 +-  
 9files changed, 115 insertions(+), 6 deletions(-)  
```
这些叠加的最后一层就是container，所以你在container里面改了文件，其实不会进image。

**一次完整的docker实作**   
 说了那么多后，我们必须亲自动手玩了。下面把pull，run，build，push都玩一次，破除神秘感。一个典型的运行流程如下：

1.client用pull命令从仓库把image拉到docker host

docker pull的格式是：   
 docker pull[选项] [Docker Registry地址] <仓库名>:<标签名>   
 默认地址是 DockerHub。 仓库名：这里的仓库名是两段式名称，既 / ，“/”前面一般是用户名。对于 Docker Hub，如果不给出用户名，则默认为 library ，也就是官方镜像。   
 下载 Ubuntu14.04的image（以Ubuntu为例）：


```
baohua@ubuntu:~$docker pull ubuntu:14.04  
14.04:Pulling from library/ubuntu  
c60055a51d74:Downloading [>                                                 ] 539.8 kB/65.69 MB  
755da0cdb7d2:Download complete  
969d017f67e6:Download complete  
37c9a9113595:Download complete  
a3d9f8479786:Download complete                                                                  
…  
```
运行docker images命令看看下载的images：


```
$docker images  
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE  
ubuntu            ml 14.04              b969ab9f929b        4 weeksago         188 MB  
```

  1. 在docker host上面运行Ubuntu 14.04于containers   
      我们现在运行Ubuntu14.04中的bash shell，因为docker运行image于容器时，需要指定主进程（本例的主进程为bash）。   
      在终端1上面运行 
```
docker run -it --rm ubuntu:14.04 bash  
```
在终端2上面运行


```
docker run -it --rm ubuntu:14.04 bash  
```
这样我们就运行了ubuntu14.04这个image的2次实例（得到2个容器）, Linux下面的ps命令是看进程的，docker下面就是看image的实例容器了。


```
$ docker ps  
CONTAINER ID       IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES  
e3a913872698       ubuntu:14.04       "bash"              11seconds ago      Up 10 seconds                           wizardly_elion  
db1c25753e97       ubuntu:14.04       "bash"              21seconds ago      Up 21 seconds                           adoring_shannon
```
image和container之间的关系类似程序与进程之间的关系，一个静若处子，一个动如脱兔。比如程序QQ，运行一次就是1个QQ进程，再运行一个QQ就是第2个QQ进程。同样道理，一个image也可以运行多份container。

3.构建自己的image

现在想在Ubuntu 14.04中增加vim和gcc，构建一个增量image，因为目前的Ubuntu image里面没有这样的命令：


```
root@e3a913872698:/# vim  
bash: vim: command not found  
```
于是在Ubuntu 14.04这个image基础上面，叠加一层，然后把它提交到docker hub的21cnbao的仓库。   
 我们需要在客户端电脑上面创建一个Dockerfile文件（该文件用于描述image），以实现在现有的Ubuntu 14.04上面做增量的目的。


```
$ mkdir myubuntu  
$ cd myubuntu/  
$ touch Dockerfile  
```
用vim编辑Dockerfile，添加如下内容：


```
# ubuntu 14.04 with vim and gcc  
FROM ubuntu:14.04  
MAINTAINER Barry Song<21cnbao@gmail.com>  
RUN apt-get update && apt-getinstall –y vim gcc  
```
RUN 指令的含义是在指定在源image内执行一条命令，本例更新APT 缓存，并且安装vim和 gcc以形成一个增量image。   
 下面build这个image：


```
$ docker build -t 21cnbao/myubuntu:14.04 .  
time="2017-02-21T06:48:07+08:00"level=info msg="Unable to use system certificate pool: crypto/x509: systemroot pool is not available on Windows"  
Sending build context to Docker daemon2.048 kB  
Step 1/3 : FROM ubuntu:14.04  
 ---> b969ab9f929b  
Step 2/3 : MAINTAINER Barry Song<21cnbao@gmail.com>  
 ---> Running in f1449746b58c  
 ---> 5dacd7a6ee5d  
Removing intermediate containerf1449746b58c  
Step 3/3 : RUN apt-get update &&apt-get install vim gcc  
 ---> Running in b1469caf3509  
Ign http://archive.ubuntu.com trustyInRelease  
Get:1 http://archive.ubuntu.comtrusty-updates InRelease [65.9 kB]  
Get:2 http://archive.ubuntu.comtrusty-security InRelease [65.9 kB]  
Get:3 http://archive.ubuntu.com trustyRelease.gpg [933 B]  
Get:4 http://archive.ubuntu.com trustyRelease [58.5 kB]  
Get:5 http://archive.ubuntu.comtrusty-updates/main Sources [485 kB]  
…  
```
下面运行21cnbao/myubuntu 14.04这个镜像：


```
docker run -it --rm 21cnbao/myubuntu:14.04 bash  
```
发现gcc和vim都有了：

4.通过docker push把image提交到仓库

在docker hub上面创建一个仓库myubuntu，该仓库创建后，全名将为21cnbao/myubuntu。   
![图片描述](https://img-blog.csdn.net/20170731153255153?)  
 下面push这个image到docker hub，之前我们需要登录到docker hub：


```
$ docker login --username=21cnbao --email=21cnbao@gmail.com  
Flag--email has been deprecated, will be removed in 1.14.  
Password:  
Login Succeeded  
```
下面开始push：


```
$ docker push 21cnbao/myubuntu  
time="2017-02-21T07:17:59+08:00"level=info msg="Unable to use system certificate pool: crypto/x509: systemroot pool is not available on Windows"  
The pushrefers to a repository [docker.io/21cnbao/myubuntu]  
87157b68b121:Pushing [>                                                 ] 1.109 MB/134.7 MB  
c9fc7024b484:Pushing [==================================================>] 3.072 kB  
ca893d4b83a6:Pushing [==================================================>] 4.608 kB  
153bd22a8e96:Pushing 7.168 kB  
83b575865dd1:Pushing [==================================================>] 209.9 kB  
918b1e79e358:Waiting  
…  
```
通过docker hub进哥的仓库看一眼，发现大功告成了。   
![图片描述](https://img-blog.csdn.net/20170731153544289?)  
 2小时结束，相信你已经爱上Docker。相爱容易相处难，痛苦才刚刚开始。人生若只如初见，何事秋风悲画扇。等闲变却故人心，却道故人心易变。

后面如果有时间，再完成一个《Docker 相处的8小时》。


--------
[**Docker最佳实战**](http://edu.csdn.net/huiyiCourse/series_detail/55?utm_source=home2)   
 随着Docker技术被越来越多的人所认可，其应用的范围也越来越广泛。该课程集中于Docker实践应用，不会过多纠结于细细微的理论分析，而是涵盖Docker最基本和常用的功能和命令，让学员在最短的时间内了解如何上手使用Docker，配合精心设计的范例，以及范例代码的演化过程，学员可以可以直观地看到Docker在实际项目中的应用，可能碰到的问题，如何解决，以及如何改进项目的开发过程。   
[![图片描述](https://img-blog.csdn.net/20170731154345830?)](http://edu.csdn.net/huiyiCourse/series_detail/55?utm_source=home2)

**目前群已满100人，添加微信号：csdn01，备注“Docker”即可入群！**

   
  