---
title: 值得拥有 不容错过的Hive精华汇总
date: 2016-11-30 13:29:38
tags: CSDN迁移
---
 版权声明：本文为博主原创文章，未经博主允许不得转载。 https://blog.csdn.net/mengyidan/article/details/80127101   
  【编者按】Hive作为Hadoop家族的重要一员，具有学习成本低，开发者可通过类SQL语句快速实现简单的MapReduce统计，不必开发专门的MapReduce应用。在攒库中，Hive也不负众望，得到了非常高的票数。为此，CSDN知识库特邀社区专家蒋守壮（博客：[http://blog.csdn.net/jiangshouzhuang](http://blog.csdn.net/jiangshouzhuang)）绘制了Hive技术图谱，帮助广大开发者更加系统、全面的学习Hive技术。

[Hive知识库发布，速来关注！](http://lib.csdn.net/base/hive)

**我要成为[Hive专家团](http://lib.csdn.net)一员，筛选优质内容>>猛戳这里：[http://lib.csdn.net/experts/invite](http://lib.csdn.net/experts/invite)**



![这里写图片描述](https://img-blog.csdn.net/20161130110653271)  
[点击下载Hive高清技能图谱](http://lib.csdn.net/base/hive/structure)

**Hive知识库精华内容一瞥**

**[基于Hadoop的数据仓库Hive基础知识](http://lib.csdn.net/article/58/45304?knId=1627)**

本文从Hive概述、Hive系统架构、Hive工作原理、Hive HA基本原理、Impala五大方面详细讲解了Hive，对于每个准备学习和使用Hive的开发者，都应该读一读。

**[使用Hadoop和Hive获取机器数据](http://lib.csdn.net/article/hive/49906)**

在探索数据存储和供给的基本机制之前，开发者需要考虑存储何种信息，如何存储它，以及打算存储多长时间。本文就Hadoop和Hive如何存储及获取数据进行了全面分析。

**[自定义Hive SQL Job分析工具](http://lib.csdn.net/article/hive/43213)**

Hive最强大的地方是可以将SQL语句最终转化为MapReduce作业进行处理。但是，对于超级长的Hive SQL语句，开发者想要分析哪个子段所花费的查询时间，就很难了。本文围绕该问题出发，自定义Hive SQL Job分析工具，提高Hive查询性能。

**[建立HBase的集群和HDInsight在Hadoop中使用Hive来查询它们](http://lib.csdn.net/article/58/6722?knId=1627)**

本文将教你如何使用Hive Hadoop的HBase的表创建和查询HDInsight。

**[Hive性能优化实战分享]()**

Hive作为Hadoop家族的重要一员，具有学习成本低，开发者可通过类SQL语句快速实现简单的MapReduce统计，不必开发专门的MapReduce应用。本文分享了不少Hive优化技巧，值得一读。

**[Hive五种数据导入方式介绍](http://lib.csdn.net/article/58/45631?knId=1641)**

本文主要围绕以下六方面进行详细说明：1）Hive五种数据导入方式有哪些？2）导入表命令中有无LOCAL关键字的区别？3）使用OVERWRITE或INTO关键字的作用及区别？4）INPATH路径的限制？5）什么是动态分区插入？6）动态分区插入需要做哪些设置？

**[MapReduce Hive Hbase项目优化](http://lib.csdn.net/article/58/44659?knId=1627)**

充分的利用机器的性能，更快的完成MapReduce程序的计算任务。甚至是在有限的机器条件下，能够支持运行足够多的MapReduce程序。

**[Hive SQL的编译过程](http://lib.csdn.net/article/58/44757?knId=1627)**

本文详细讲解了Hive如何将SQL编译为MapReduce，主要分为哪六个阶段，以及Hive SQL编译过程的设计，非常干货的一篇文章。

**[Parquet与ORC性能测试报告](http://lib.csdn.net/article/58/42008?knId=1638)**

本文使用Hive对三种不同的文件存储格式——Text、ORC和Parquet进行了对比测试，通过这三种文件存储格式的测试对比，ORC文件存储格式无论是在空间存储、导数据速度还是查询速度上表现的都较好一些，并且ORC可以一定程度上支持ACID操作。

**[Hive RCFile高效存储结构](http://lib.csdn.net/article/58/43651?knId=1637)**

本文介绍了Facebook公司数据分析系统中的RCFile存储结构，该结构集行存储和列存储的优点于一身，在MapReduce环境下的大规模数据分析中扮演重要角色。

**>>有关Hive的更多干货、视频、书籍，可以访问Hive知识库。值得一提的是，除了官方图谱，小伙伴们还可以创建自己的专属知识库哦，前往搭建：[http://lib.csdn.net/my/feed](http://lib.csdn.net/my/feed)**

更多内容请关注[CSDN知识库](http://lib.csdn.net)。该产品汇集了领域专家们精心绘制的各重点技术领域的知识图谱，及由特邀编辑（领域专家）从海量数据中层层筛选出的精华内容和资源（学习视频、实践Demo、图书）。知识图谱可以帮助开发者全局把控该技术领域，而精选内容更能让开发者从技术细节加深了解该领域的每个核心技术点。扫描以下二维码，获取更多CSDN知识库内容。   


![](https://img-blog.csdn.net/20160712174725555)

**知识库用户微信交流群**

为了方便大家沟通交流，我们特开通了知识库微信群，请您多多抛砖建言，从而使知识库的内容和使用更顺畅。同时，我们也会定期组织一些精彩的技术分享活动回馈大家。针对CSDN知识库，您有任何意见或建议，也欢迎随时邮件baiyz#csdn.net。

欢迎添加“知识库微信交流群”群主微信「mengyidan1988,或扫以下二维码」,申请入群，注明「知识图谱交流」，后邀请入群。



![图片描述](https://img-blog.csdn.net/20160930110402091)

作为开发者，追逐技术的发展不断学习，这是我们一生的使命。

我们不是在学技术，就是在学技术的路上，希望我们共同打造出最好的知识图谱，相互提携，快速通关。

   
  