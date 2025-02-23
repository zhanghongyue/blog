---
title: 小团队中人人都要会用点Docker
date: 2017-07-28 16:39:23
tags: CSDN迁移
---
 版权声明：本文为博主原创文章，未经博主允许不得转载。 https://blog.csdn.net/mengyidan/article/details/80130782   
  > 来自[Docker微信公众号](http://mp.weixin.qq.com/s/21r4ghsGVFAJf6aRUSTd_Q)   
>  作者：李强
> 
>  
![图片描述](https://img-blog.csdn.net/20170728163505011?)  
 小团队一般泛指规模不超过10人的Team，麻雀虽小，五脏俱全，通常也包含了多种人员角色，产品经理、UI/UE、运营、前端开发人员、后端开发人员、运维人员、测试人员等，有时候也需要一个人身兼多种角色，在互联网的江湖中，很多成功的案例都印证了小团队的战斗力，小团队的生存之道在于能够更加高效的输出结果，提升团队的作业效率，而在小团队中推广Docker的使用，往往可以起到事半功倍的效果。

Docker从诞生至今，已经走过了3年，随着版本的更新迭代功能也越来越稳定，从最初只支持Linux端，到现在已经可以支持Mac和Windows，Docker的学习成本和使用难度在逐步的降低，可以预见在不远的将来，Docker将成为所有IT从业人员必备的基础技能。为什么人人都要会用点Docker？

**1. 为了践行DevOps**   
 DevOps的理念越来越深入人心，但是具体怎么去落地，怎么去实施，现在还处于探索阶段，大公司的成功案例基本上不具有可复制性。小团队中要想践行DevOps，可以先从最细小的基础设施搭建上作为切入点，在开发的过程中往往也会用到各种中间件环境，例如：Kafka、RabbitMQ、Redis等，在没有Docker之前，这些中间件的搭建与配置需要占用大量的时间，大部分情况下都要交由专人员负责搭建，在使用Docker之后，中间件的搭建容易了许多，只要阅读官方提供的镜像说明，开发人员就可以在自己的电脑或者服务器上搭建任何需要的中间件。

**2. 为了一致的运行环境**   
 开发过程中一个常见的问题是环境一致性问题。由于开发环境、测试环境、生产环境不一致,导致有些 bug 并没有在开发过程中被发现。Docker提供了除内核外完整的运行时环境,确保了应用运行环境的一致性,从而不会再出现 “这段代码，在我机器上没问题啊”。

**3. 为了更高效的进行技术调研**   
 有时候为了一些功能的开发，需要进行前期的技术调研与试做，使用Docker可以做到拆箱既用，排除了各种环境不一致问题带来的干扰，例如某些library需要一些特定的Linux环境，在开发人员自己的机器中完整复制这些特定的环境几乎不可能，而使用Docker只需要pull对应的系统镜像，任意的调整和安装所需要的库就可以完成调研基础环境的准备，节省了大量的时间。

**4. 为了节省资源**   
 小团队的“小”可能也意味着服务器资源的紧张，无法满足所有人对机器资源的需求，当每个人都会使用Docker之后，不再需要为开发提供专门的开发机器，只需要提供基础的运行环境镜像，开发人员可以随时根据需要将这些基础开发环境pull到本地，在自己的开发机器中运行和使用，这样就避免了不必要的资源消耗。

**5. 为了可以CI和CD**   
 对开发和运维（DevOps）人员来说,最希望的就是一次创建或配置,可以在任意地方正常运行。当使用Docker之后，编译发布的不再是单独的运行实例，而是一个个独立的Image，这些Image可以快速的被发布到不同的环境中，进行持续的测试，使得整个产品的交付周期被大幅度的缩减。DockerFile不再由运维Team负责提供，运维Team只负责提供统一的基础镜像，开发人员根据项目需要来编写DockerFile，从而打通了项目交付的最后一公里。

**6. 为了能够更快速的调试问题**   
 在采用微服务、分布式的系统架构后，增加了调试问题的难度，在引入Docker之前，一个线上问题如果想要在开发环境中进行再现与调试，往往会受到关联的服务、基础设施中间件等环境制约，几乎无法等价的平移与复制，在使用Docker之后，开发和测试人员就可以使用已发布的镜像文件，快速的在本地复制问题发生的环境，使得线上问题在本地进行问题再现与调试所消耗的时间大幅度的降低。


--------

## [3小时掌握Docker最佳实战](http://edu.csdn.net/huiyiCourse/series_detail/55?utm_source=home2)

随着Docker技术被越来越多的人所认可，其应用的范围也越来越广泛。该课程集中于Docker实践应用，不会过多纠结于细细微的理论分析，而是涵盖Docker最基本和常用的功能和命令，让学员在最短的时间内了解如何上手使用Docker，配合精心设计的范例，以及范例代码的演化过程，学员可以可以直观地看到Docker在实际项目中的应用，可能碰到的问题，如何解决，以及如何改进项目的开发过程。   
[![图片描述](https://img-blog.csdn.net/20170728165015091?)](http://edu.csdn.net/huiyiCourse/series_detail/55?utm_source=home2)

   
  