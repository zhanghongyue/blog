---
title: 你应该知道的 5 个 Docker 工具
date: 2017-07-26 15:51:18
tags: CSDN迁移
---
 版权声明：本文为博主原创文章，未经博主允许不得转载。 https://blog.csdn.net/mengyidan/article/details/80130737   
  > 本文来自：[可译网](https://coyee.com/article/12236-5-docker-utilities-you-should-know?utm_source=tuicool&amp;utm_medium=referral)
> 
>  
你可以在网上找到大量炫酷的Docker 工具，并且大部分是开源的，可以通过Github访问。在过去的两年里，我开始在开发项目中大量使用Docker。当你开始使用Docker，你会发现它比你想象的还要适合于更多的使用场景。你也许希望Docker能够做的更多，当然你不会失望。

Docker社区非常活跃，每天都会推出大量有用的工具。要想持续追踪社区中发生的各项创新其实非常困难。为了帮助你，我收集了一些每天在日常工作中使用、令人感兴趣并且十分有用的Docker工具。这些工具消除了大量手工工作，让我更有生产力。   
 让我们来看看这些在我的容器化之旅中非常有用的工具吧。


  1. [watchtower](https://github.com/v2tec/watchtower): 自动更新Docker容器 瞭望塔（Watchtower）能够监控正在运行的容器，观察自容器启动以来对镜像的任何修改。当瞭望塔检测到一个镜像发生改变，它会自动使用新镜像进行重启容器。我在本地开发环境使用该工具，以便我能够使用最新构建的镜像。

瞭望塔本身也被打包为一个Docker镜像，你可以使用运行其他容器的方式运行该工具。要启动瞭望塔，你可以使用以下命令:   
![图片描述](https://img-blog.csdn.net/20170726152117001?)  
 在上述命令中，我们启动了瞭望塔容器，并挂载了文件 /var/run/docker.sock 。这是必须的，以便该工具能够与Docker后台API进行交互。此处使用选项间隔(interval)为30秒。该参数定义了工具轮询的间隔。瞭望塔支持的更多选项见文档.

现在启动一个瞭望塔能够监控的容器：   
![图片描述](https://img-blog.csdn.net/20170726152209073?)  
 此时，瞭望塔将对friendlyhello容器进行监控。当我将新镜像提交到Docker Hub时，瞭望塔在下次运行时将会检测到已有一个新的镜像。它会优雅的停止容器，并使用新镜像自动启动新容器。它将一些参数传递给run命令，换句话说，容器将使用4000:80发布端口。

默认情况下， 瞭望塔会轮询Docker Hub官网查找更新的镜像。如欲使工具轮询私有Docker仓库，需要配置以下环境变量：REPO_USER 和 REPO_PASS。

要先了解更多，推荐阅读[瞭望塔文档](https://github.com/v2tec/watchtower/blob/master/README.md)。   
 2. [docker-gc](https://github.com/spotify/docker-gc): 容器和镜像垃圾收集

docker-gc工具能够帮助你清理Docker宿主机，删除不再需要的容器和镜像。它会移除存在超过1小时的所有容器。同时，它会删除不属于任何遗留容器的镜像。

你可以把docker-gc当做脚本或容器使用。这里，我们作为容器运行docker-gc。使用docker-gc查找所有可以删除的容器和镜像，命令如下：   
![图片描述](https://img-blog.csdn.net/20170726152834552?)  
 上述命令中，我们加载了Docker套接字文件，以便docker-gc能够与Docker API交互。我们传递一个环境变量DRY_RUN=1来查找将被删除的容器和镜像。如果不提供该参数，docker-gc会删除所有容器和镜像。最好事先确认docker-gc要删除的内容。上述命令输出示意如下：   
![图片描述](https://img-blog.csdn.net/20170726154107934?)  
 如果你同意docker-gc清空方案， 可以不使用DRY_RUN再次运行docker-gc执行清空操作。   
![图片描述](https://img-blog.csdn.net/20170726154156809?)  
 上述命令的输出可以告诉您 docker-gc 删除的所有镜像和容器。

docker-gc还支持其他一些选项，建议阅读docker-gc文档以了解更多。   
 3. [docker-slim](https://github.com/docker-slim/docker-slim): 面向容器的神奇减肥药丸

如果你担心您Docker镜像的大小，docker-slim可以打消您的一切顾虑。

docker-slim工具使用静态和动态分析方法来为你臃肿的镜像瘦身。要使用docker-slim，可以从Github下载Linux或者Mac的二进制安装包. 一旦你成功下载，并加入到你的系统PATH变量中。

为了举例，我参考Docker官方文档创建了一个Docker镜像 friendlyhello ， 镜像大小为194MB（如下所示）：   
![图片描述](https://img-blog.csdn.net/20170726154306932?)  
 在这个简单的应用中，我们已经下载了194MB的数据。让我们使用docker-slim工具来看看能够瘦身多少吧。   
![图片描述](https://img-blog.csdn.net/20170726154519237?)  
 docker-slim工具将执行一系列的检查和装配步骤，最终创建一个瘦身版本。让我们来看看搜身版本的大小：   
![图片描述](https://img-blog.csdn.net/20170726154700213?)  
 如上图所示，影像大小被减少到24.9 MB。你可以启动该容器，以同样的方式运行。docker-slim工具支持Java, Python, Ruby和Node.js应用。

自己试试看能够瘦身多少。在我的个人项目中，我发现在大多数情况下都能够正常工作。您可以从 docker-slim文档学到更多。


  1. [rocker](https://github.com/grammarly/rocker): 超越 Dockerfile 限制 大多数使用Docker的开发者都使用Dockerfile构建镜像。Dockerfile是一种声明方式，在命令行定义所有的命令，以编配一个镜像。

Rocker 为Dockerfile指令集增加了新的指令。Rocker由Grammarly创建，以解决采用Dockerfile格式遇到的一些问题。Grammarly团队写了一个 博客，深度剖析和解释了创建它的原因。我建议您阅读它以更好的理解Rocker。在博文中，他们主要强调了2个问题：

Docker镜像的大小.   
 缓慢的构建速度.

该博文也提到了Rocker增加的几个新指令。参考Rocker文档 以学习所有Rocker支持的指令。

MOUNT 用于共享不同构建的卷，以便能够被依赖管理工具重用。   
 FROM 指令在Dockerfile也存在。Rocker使得可以添加多个FROM指令。 这意味着您能够从单个Rockerfile中创建多个镜像。第一批指令用于构建产品所有的依赖；第二批指令用于构建产品；这能够极大的降低镜像大小。   
 TAG 用于在构建的不同阶段标识镜像，这意味着您不必手动为每个镜像打标签。   
 PUSH 用于将镜像推送到镜像仓库。   
 ATTACH 允许您能够交互的运行一个中间步骤，主要用于调试。

要使用Rocker，首先必须安装。对Mac用户来说，使用brew安装命令非常简单：   
![图片描述](https://img-blog.csdn.net/20170726154816395?)  
 一旦完成安装，您就可以使用Rocker传递Rockerfile来构建镜像：   
![图片描述](https://img-blog.csdn.net/20170726154854529?)  
 要构建镜像并推送到Docker Hub，可以运行以下命令：   
![图片描述](https://img-blog.csdn.net/20170726154931762?)  
 Rocker有很多功能，要知道更多请参考文档。


  1. [ctop](https://github.com/bcicen/ctop): 容器的类Top界面 ctop是我最近使用的一个工具，它能够提供多个容器的实时指标视图。如果您是一个Mac用户，可以使用brew安装：   
![图片描述](https://img-blog.csdn.net/20170726155023141?)  
 一旦完成安装，你可以启动ctop。它只需要配置DOCKER_HOST环境变量。   
 要查看所有容器的状态，可以直接运行 ctop 命令。   
![图片描述](https://img-blog.csdn.net/20170726155059830?)  
 仅查看当前正在运行的容器的状态，可以使用 ctop -a 命令。

ctop是一个简单并且非常有用的工具，能够帮助您了解主机上运行的容器。可以阅读 ctop文档了解更多ctop的知识。

这些都是我发现的非常有用的Docker工具。您是否也在日常工作中用到其他的Docker工具呢？如果是这样的话，请在下面的评论区留言以让我们知晓。


> 3小时掌握Docker最佳实战，该课程集中于Docker实践应用，不会过多纠结于细细微的理论分析，而是涵盖Docker最基本和常用的功能和命令，让学员在最短的时间内了解如何上手使用Docker，配合精心设计的范例，以及范例代码的演化过程，学员可以可以直观地看到Docker在实际项目中的应用，可能碰到的问题，如何解决，以及如何改进项目的开发过程。
> 
>  
**现在报名可以享受专属优惠，报名地址：[http://edu.csdn.net/huiyiCourse/series_detail/55?ref=home&loc=r0](http://edu.csdn.net/huiyiCourse/series_detail/55?ref=home&amp;loc=r0)**

   
  