---
title: 知言 代立冬：大家都在关注Hbase到底是怎样的？
date: 2016-11-03 17:25:45
tags: CSDN迁移
---
 版权声明：本文为博主原创文章，未经博主允许不得转载。 https://blog.csdn.net/mengyidan/article/details/80126328   
  【编者按】Hbase作为Hadoop家族的重要一员，其不同于一般的关系数据库，它是一个适合于非结构化数据存储的数据库。另一个不同的是，它基于列的而不是基于行的模式。

为了帮助大家更加系统全面地学习Hbase知识，我们CSDN知识库特邀请了知名的社区专家**代立冬**老师，绘制了Hbase知识图谱，根据知识点挑选优质内容呈现给大家


> 代立冬，CSDN知识库特邀编辑，CSDN社区专家。信柏信息科技有限公司架构师，专注于数据平台的规划、集群调优、数据安全、数据仓库构建以及线上trouble shooting，个人博客 [blog.csdn.net/odailidong](blog.csdn.net/odailidong)
> 
>  
**Hbase知识库，抢先关注:[http://lib.csdn.net/base/hbase](http://lib.csdn.net/base/hbase)**



![这里写图片描述](https://img-blog.csdn.net/20161103151433463)   
代立冬

**CSDN知识库：你是如何与计算机结缘，踏上软件开发之路的，能否跟我们分享一下。**

**代立冬：**我走上计算机这条路主要源于大学室友熏陶，我本身是学数学专业的，按常理毕业后应该当个教师。而我们宿舍6个室友有一半学数学，一半学计算机。经常看室友们写点小程序，觉得挺厉害，有时还听他们吹牛皮：他们班一同学写了个校园论坛云云，长期耳濡目染后，自然走上了这条道。有一段时间还常跑去听他们老师讲专业课。后来在网上找到一些商城的开源代码，有空就折腾折腾，不时添加点需求实现下，当然当时的主流还是做网站开发，SSH框架大行其道，不像现在择业方向这样广。

**CSDN知识库：HBase作为NoSQL数据库，与其他NoSQL产品有哪些区别和优势？**

**代立冬：**这里拿NoSQL主要的两个代表作品做下对比（HBase和MongoDB），两者主要的区别：


  1. MongoDB是文档型数据库，整个数据都存在磁盘中，HBase是列式数据库，存储时按column family保存到对应的hdfs文件中; 
  3. MongoDB支持二级索引，而HBase本身不支持二级索引； 
  5. MongoDB的update是update-in-place，也就是原地更新，而HBase的修改和添加都使用put，实际上HBase内部对已经存在的rowkey数据，只是将这一数据用不同的版本号保存下来而已，HBase默认的保存版本的数量是3； 
  7. HBase根据文件的大小来控制region的分裂，在HBase内部，存储时，数据按照Row key的字典序(byte order)排序存储；MongoDB根据负载来决定shards的分裂； 
  9. MongoDB和HBase都支持mapreduce，不过MongoDB的mapreduce支持不够强大，调试也较麻烦； 
  11. MongoDB的读效率比写高，HBase默认适合写多读少的情况。 以我们的线上使用情况，对rowkey分布均匀的情况，HBase集群的写速度单台regionserver达到3~4w/秒，单key查询一般10ms内返回(如果使用SSD硬盘会更快)。由于我们线上每天写入70多亿条数据，当时MongoDB还没有收购WiredTiger，当时MongoDB的写入锁是个很大问题，所以我们技术选型就选的HBase。

**CSDN知识库：如何提升HBase的读写性能？**

**代立冬：**首先我们需要知道影响读写性能有什么因素：

**写速度关键因素**


  * table region分布均衡; 
  * 单台region server的region数; 
  * hbase.regionserver.handler.count; 
  * hbase.regionserver.global.memstore.upperLimit; 
  * hbase.hregion.memstore.block.multiplier; 
  * hbase.hstore.blockingStoreFiles; 
  * hbase.hregion.max.filesize; **读速度关键因素**


  * 单台Region Server上的Region数; 
  * StoreFile数; 
  * bloomfilter; 
  * in-memory flag; 
  * blockcache设置; 
  * hfile.block.cache.size; 然后具体性能优化可分为服务端和客户端，服务端和客户端我列几个代表性的(详细请参见[http://blog.csdn.net/odailidong/article/details/41794403](http://blog.csdn.net/odailidong/article/details/41794403))

**服务端：**


  * hbase.hregion.max.filesize：默认是10G，如果任何一个column familiy里的StoreFile超过这个值, 那么这个Region会一分为二，因为Region分裂会有短暂的Region下线时间(通常在5s以内)，建议手动定时分裂，可以设置为60G； 
  * hbase.hregion.majorcompaction：hbase的Region主合并的间隔时间，默认为1天，建议设置为0，禁止自动的major主合并，major合并会把一个store下所有的storefile重写为一个storefile文件，还会把有删除标识的数据删除，业务低峰期major合并； 
  * hbase.hregion.memstore.flush.size：默认值128M，单位字节，一旦有memstore超过该值将被flush，如果regionserver的jvm内存比较充足(16G以上)，可以调整为256M； 
  * hbase.hstore.compaction.max：默认值为10,一次最多合并多少个storefile，避免OOM； 
  * hbase.hstore.blockingStoreFiles：默认为7，如果任何一个store(非.META.表里的store)的storefile的文件数大于该值，则在flush memstore前先进行split或者compact，同时把该region添加到flushQueue，延时刷新，这期间会阻塞写操作直到compact完成或者超过hbase.hstore.blockingWaitTime(默认90s)配置的时间，可以设置为30，避免memstore不及时flush。 **客户端**


  * hbase.client.write.buffer：默认为2M，写缓存大小，推荐设置为5M，单位是字节，当然越大占用的内存越多，此外测试过设为10M下的入库性能，反而没有5M好； 
  * hbase.client.pause：默认是1000(1s)，如果你希望低延时的读或者写，建议设为200，这个值通常用于失败重试，region寻找等； 
  * hbase.client.retries.number：默认值是10，客户端最多重试次数,可以设为11，结合上面的参数，共重试时间71s； 
  * hbase.ipc.client.tcpnodelay：默认是false，建议设为true，关闭消息缓冲； 
  * hbase.client.scanner.caching：scan缓存，默认为1，避免占用过多的client和rs的内存，一般1000以内合理，如果一条数据太大，则应该设置一个较小的值，通常是设置业务需求的一次查询的数据条数，如果是扫描数据对下次查询没有帮助，则可以设置scan的setCacheBlocks为false，避免使用缓存； 
  * 限定扫描范围：指定列簇或者指定要查询的列，指定startRow和endRow。通过java多线程入库和查询，并控制超时时间。 **CSDN知识库：HBase的rowkey设计需要注意哪些情况，有哪些好的设计方案？**

**代立冬：**整体上说rowkey设计尽量分布均匀，避免热点。常见的均匀rowkey设计可考虑逆序，加盐值打散，如果是递增数字，还可考虑使用Long.maxValue减去当前值。

rowkey的特点：


  * 三维有序rowkey（行主键,按ascii排序）  
  * columnkey(columnFamily+qualifier) 
  * timestamp(时间戳) 举个简单例子，比如手机号分为13,15,17,18等号段，但是13开头的手机号码特别的多，这时就可以考虑逆序下是否能满足需求。rowkey的设计应具体场景具体对待。如果怎么设计，都避免不了会产生热点的问题，也不用太担心，下面这个问题是针对热点的一些处理办法。

**CSDN知识库：HBase发生数据热点问题时，应该从哪些角度分析问题？**

**代立冬：**热点问题通常是某些region读取或写入压力过大，热点可以通过设置监控阀值来发现。   
 在监控检测到热点region时，可以手动(自动)进行热点region split，如果发现某台region server上不少region是热点，可以考虑，move region到其它空闲的region server上，注意监控region server服务器的硬件，比如cpu、memory、io、network等指标。如果需要删除大量数据，尽量考虑设置TTL数据自动过期删除。而后可以返回来去考虑主键的设计，是否采用压缩等。

**CSDN知识库：如何基于HBase实现二级索引，开源方案中常用的有哪些，以及性能如何？**

**代立冬：**这个我没有实际使用过，一些开源的二级索引通过使用协处理器去做的，但协处理器运行在region server本身的内存中，对于大型集群，不建议使用。有些替代的办法比如可以考虑结合ElasticSearch使用

**CSDN知识库：从哪些方面可以判断HBase集群是否健康？**

**代立冬：**对于1个需要7*24小时在线服务的集群来说，这个通常需要有一套监控报警系统去监控HBase集群的运行状况，我们当时调研过世面上很多开源的监控，都不大满意。最后参照sematext公司的产品实现了1套自己的hbase(含hdfs)监控报警体系。

对于Hbase健康，我主要关注以下指标：


  * zookeeper的状态、连接数(一般一台zookeeper节点上连接数不会超过200)、发送和接收数据量 
  * region热点，region storefile个数和size，region split失败次数情况(吃过大亏) 
  * region memstore大小、flush队列大小、flush耗时及大小情况。 
  * block cache命中率及cache大小 
  * region server的合并/分裂队列数，我们是每天在业务低峰时定时合并/分裂region的，所以这个值大部分时间都是0 
  * region server上rpc连接数，jvm内存gc情况，经jvm参数优化，对64G的jvm内存还没有出现过full gc的情况 当然还有一些指标，不一一列举了。

**CSDN知识库：介绍一些HBase的典型应用场景？**

**代立冬：**个人总结的一些场景，欢迎补充：


  * 存储大量数据(TB级以上)，需要很高的写吞吐能力，在大规模数据集中随机访问(基于rowkey)，需要水平扩展能力，易维护； 
  * 不适合对事务要求高、多维度查询的场景。 **CSDN知识库:对HBase初学者有没有什么好的建议？**

**代立冬：**建议理论->实践->理论理论这样的路线，HBase的用法跟关系型数据库的用法不大一样，从关系型转过来的同学需要稍转变下思路，现在网上的关于HBase资料很多，多看，多练，对于测试集群来说，尝试把服务搞瘫痪，再尝试修复。还可以关注官方的issue列表，国内还可以加几个qq群，多问问群里的热心人

**CSDN知识库：你最近在研究哪些技术？谈谈自己的感受。**

**代立冬：**现在数据平台整体稳定，我个人抽时间学了些深度学习方面的技术，主要是cnn、rnn。对我冲击最大的是深度学习效果在某些场景（比如分类）为何如此好，当然也有我头疼的地方，就是它现在还需要有大量的标注数据然后效果才能上去。很多传统的机器学习做法，比如复杂的验证码识别等用深度学习实现，效果很好。

**CSDN知识库：关于技术学习您有什么心得？我们上线了知识库系统化学习的方法，您会怎么应用呢？**

**代立冬：**大数据时代随着很多新兴概念逐渐流行，相应的技术也会有很多，我觉得还是脚踏实地，尽量在工作中运用上所学技术，实践中积累，总结，同时多分享自己的所学知识给伙伴们，在给别人帮助同时受益可能最大的还是自己。现在在搜一些问题解决方法时，也经常能看到CSDN的身影，对CSDN付出如此大的力气去做这个知识体系建设，实属难得。我刚好在学习深度学习，现在知识库里面列出了深度学习体系的知识点，并且每个知识点有不少精选的文章，我会着重找下相关的最新的业界进展，顺便看下有没有相关开放的数据集，另外我也会搜集整理成自己的知识体系。

**推荐阅读：**


  * [Get IT技能知识库 50个领域一键直达]() 
  * [【知识库专访】蒋守壮：Hive性能优化实战分享]() 
  * [【知识库专访】阿里孙佰贵：深度学习十问十答]() 
  * [【知识库专访】亲加CTO郝飞：直播技术架构解密与优化之道]() 
  * [前端开发人员必须了解的七大技能图谱]() 
  * [来吧 主流编程语言图谱+知识库都在这了]() 
  * [主流编程语言图谱+知识库（二）]() 

![](https://img-blog.csdn.net/20161028092615173)

   
  