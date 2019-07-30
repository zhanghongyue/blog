---
title: 原来谷歌是这样构建 Docker 镜像的!
date: 2017-08-04 16:55:10
tags: CSDN迁移
---
 版权声明：本文为博主原创文章，未经博主允许不得转载。 https://blog.csdn.net/mengyidan/article/details/80130913   
  > 来自微信公众号：[JFrog杰蛙DevOps](https://mp.weixin.qq.com/s?__biz=MzIwMzc4NDY4Mw==&amp;mid=2247484583&amp;idx=1&amp;sn=1a647bf8c8f3e5ea4efc91a67b2499ff&amp;chksm=96cb6c23a1bce5352eb5920a8efa75e803c93508bb1ca2f4802e37bb9da5d476c7539bb0954b&amp;mpshare=1&amp;scene=1&amp;srcid=0727cAsKdKU5q0p7VOM90OF6#rd)   
>  作者：王青   
>  Matthew Moore 是谷歌公司的资深工程师，专注于谷歌 Docker 技术的应用，他是在谷歌最早落地 Docker 技术团队的核心成员。本次演讲他为大家介绍了谷歌使用 Docker 技术的心得。
> 
>  
**虚拟机的问题**   
![图片描述](https://img-blog.csdn.net/20170804165030462?)  
 最初，大家都使用虚拟机作为软件的运行环境，对外提供服务。为了在虚拟机上运行你的 Service，你不得不运行一大堆程序：


  * 系统进程 
  * SSH 
  * 安装 
  * Agent 
  * 安装 
  * Bash 
  * 安装一大堆 libs 其实，你仅仅只是想让你的 Service 运行起来，但你不得不维护一个40GB的虚拟机。

**然后你开始试用 Docker**   
![图片描述](https://img-blog.csdn.net/20170804165212171?)  
 开始试用 Docker，你毫不犹豫选择了和之前虚拟机一样的镜像：Ubuntu 1404， 将之前的虚机的内容复制到了 Docker 镜像，安装了一堆软件，最后发现你的 Docker 镜像有8GB。


## 谷歌的 Docker 镜像构建实践

**找到最小的基础镜像**   
 Alpine Linux 是基于musl 和 BusyBox 的操作系统，目的是为了为用户提供更高效的资源使用效率。它的特性是体积小，最小的 Alpine Linux 体积可以只有5MB。谷歌某些团队使用 Alpine Linux 作为 Docker Build 的基础镜像。

**目的：仅仅为了运行 Service**   
![图片描述](https://img-blog.csdn.net/20170804165401574?)  
 谷歌认为，为了运行一个 Service，并不需要将那些无关联的包，程序打包到容器里，换句话说，Docker 镜像里只留下需要用到的，其他的都删除，从而得到一个最小的镜像。这需要考虑以下几点：


  1. 程序编译后的二进制文件（从 Artifactory 获取） 
  3. 程序的所有依赖（从 Artifactory 获取） 
  5. 程序语言的运行时（libc，JRE，node，…） 
  7. 任何程序和 Kernel 之间的中间件 其实这一切的信息，构建工具都已经知道。   
**谷歌的构建工具 Bazel**   
 介绍 Bazel 之前，先介绍下谷歌的开发模式，对于服务器端代码库，谷歌的开发流程如下：


  1. 所有的服务器端代码库都在一个巨大的版本控制系统里 
  3. 每个人都用 Bazel 构建软件 
  5. 不同的组负责源码树的不同部分，所有的组件都是作为BUILD 目标来用 
  7. 分支主要是用来管理发布，所以每个人都在最新版本上开发软件 Bazel（[https://bazel.build/](https://bazel.build/)）是 Google 内部用来构建自己的服务器端软件的工具。目前变成谷歌公司贡献的一个开源项目，目的是帮助开发者将软件的构建和测试变得更快，更可靠。   
![图片描述](https://img-blog.csdn.net/20170804165702638?)  
 从上图可以看到，Bazel 有 WORDSPACE 的概念，WORDSPACE 文件用来准备 Docker 镜像构建所依赖的所有材料和来源。BUILD 文件用来告诉 Bazel 这个镜像应该使用什么命令进行构建，以及如何构建，如何测试。

使用 Bazel 的声明式语言: WORKSPACE 和 BUILD，开发者可以用文件描述整个构建和部署的环境。谷歌使用 Bazel 进行 Docker 的构建已经很多年，它为谷歌带来以下收益：


  1. 支持跨平台构建，分布式缓存，优化依赖解析，并行构建，增量构建。 
  3. 支持多语言（Java, C++, Android, iOS, Go等等）。 
  5. 跨平台。 
  7. 水平扩展和自定义扩展。 Bazel 是以下理念的奠基石：由于 Bazel 需要所有的依赖都被完整地指定，我们可以预测改动影响了哪些程序和测试，并在提交前执行他们。

**谷歌提供的 Distroless 镜像构建文件**   
![图片描述](https://img-blog.csdn.net/20170804165831983?)  
 Distroless ([https://github.com/GoogleCloudPlatform/distroless](https://github.com/GoogleCloudPlatform/distroless)) 是谷歌内部使用的镜像构建文件，包括 Java 镜像，Node，Python 等镜像构建文件，Distroless 仅仅只包含运行服务所需要的最小镜像，不包含包管理工具，shell 命令行等其他功能。

为什么你需要这些镜像？这些镜像是谷歌和其他大公司的最佳实践的产物，经过了漏洞扫描，镜像会持续更新，保持安全性。

如何使用？Distroless 提供的构建镜像的 BUILD 文件，通过 Bazel 可以直接进行构建。   
**总结**   
 谷歌为大家介绍了内部如何解决镜像过大的问题，以及进行大规模并发构建，测试所用到的构建工具 Bazel，并且开源了 Docker 镜像构建文件 Distroless 项目。如果你认为你的镜像也存在体积太大的问题，可以参考谷歌的实践，体验他们的工具。

参考文献：   
[https://www.bazel.build/](https://www.bazel.build/)   
[https://github.com/GoogleCloudPlatform/distroless](https://github.com/GoogleCloudPlatform/distroless)


--------
[**Docker最佳实战**](http://edu.csdn.net/huiyiCourse/series_detail/55?utm_source=home2)   
 随着Docker技术被越来越多的人所认可，其应用的范围也越来越广泛。该课程集中于Docker实践应用，不会过多纠结于细细微的理论分析，而是涵盖Docker最基本和常用的功能和命令，让学员在最短的时间内了解如何上手使用Docker，配合精心设计的范例，以及范例代码的演化过程，学员可以可以直观地看到Docker在实际项目中的应用，可能碰到的问题，如何解决，以及如何改进项目的开发过程。   
[![图片描述](https://img-blog.csdn.net/20170731154345830?)](http://edu.csdn.net/huiyiCourse/series_detail/55?utm_source=home2)

**目前群已满100人，添加微信号：csdn01，备注“Docker”即可入群！**

   
  