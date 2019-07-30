---
title: 干货整理 Unity3D资源汇总
date: 2016-08-16 10:33:16
tags: CSDN迁移
---
 版权声明：本文为博主原创文章，未经博主允许不得转载。 https://blog.csdn.net/mengyidan/article/details/80124818   
  Unity3D是目前最流行的游戏开发工具，是一个可以让玩家轻松创建诸如三维视频游戏、建筑可视化、实时三维动画等类型互动内容的多平台的综合型游戏开发工具，是一个全面整合的专业游戏引擎。

为了让开发者能够系统全面的学习Unity3D知识，CSDN特定邀请了Unity3D方面的知名专家，资深程序员一起打造了[Unity3D知识库](http://lib.csdn.net/base/unity3d)，经过各位特邀编辑的层层筛选，目前已收录的核心知识点精华文章已达300多篇，包括视频课程、实战Demo、图书等优秀的学习资源。

[我也想成为特邀编辑，参与CSDN知识库，筛选优质内容，如何加入？](http://lib.csdn.net/experts/invite) 

![](http://img.knowledge.csdn.net/upload/base/1470302448640_640.png?attname=Unity3D%E7%9F%A5%E8%AF%86%E7%BB%93%E6%9E%84%E5%9B%BE.jpg)



[**Unity3D知识图谱**](http://img.knowledge.csdn.net/upload/base/1470302448640_640.png?attname=Unity3D%E7%9F%A5%E8%AF%86%E7%BB%93%E6%9E%84%E5%9B%BE.jpg)

**Unity3D优秀资源一瞥**

[零基础一步一步教你制作跑酷类游戏(填坑完整版)]()

在两个月前曾写了一篇《【Unity3D实战】零基础一步一步教你制作跑酷类游戏（1）》，里面一步一步演示了制作跑酷类游戏，然而由于时间原因，只写到了让角色往前移动为止。这个坑一直没有时间去填，（虽然也没多少人看啦），今天刚好有时间完成了一个跑酷类游戏的Demo。放上来给有兴趣的朋友看看。

[Unity3D客户端在游戏场景中创建阻挡并用二进制导出](http://lib.csdn.net/article/47/28133?knId=1267)

整体思路是把地形用一米见方的格子分隔，然后在格子里放置cube代替阻挡，存盘时用一维数组，1表示阻挡，0表示没有阻挡。如果要实现更精确的阻挡也可以直接保存位置和旋转信息。

[Unity3D 导出的apk进行混淆和加固（防止反编译）](http://lib.csdn.net/article/47/33413?knId=1282)

对于辛辛苦苦完成的apk程序被人轻易的反编译了，那就得不偿失了，这篇文章就是解决Unity打包出来的apk进行代码加固和混淆。

[Unity3d Human skin real time rendering 真实模拟人皮实时渲染](http://lib.csdn.net/article/47/28137?knId=1280)

由于网上下的模型是拼的，所以眼皮，脸颊，嘴唇看起来像存在裂痕，解决方式是加入曲面细分和置换贴图进行一定隆起，但是博主试了一下fragment shader的曲面细分，虽然细分成功了但是着色效果变的很奇怪，这里就不用曲面细分了，大家如果有在fragment shader上用曲面细分的好办法，可以的话请告诉我参数设置。

[Unity3D游戏开发之在3D场景中选择物体并显示轮廓效果强化版](http://lib.csdn.net/article/47/28136?knId=1267)

本文主要介绍Unity3D游戏开发复杂模型的选取与轮廓高亮显示。

[Unity3d Hair real time rendering 真实头发实时渲染](http://lib.csdn.net/article/47/28129?knId=1267)

本文是博主根据一些外文文档，翻译对加上自己的理解所做出的一款作品。效果很棒，大家可以点进去学习学习。

[Unity5新版Shader模板源码解析](http://lib.csdn.net/article/47/24531?knId=1280)

本文对Unity5中全新的三种Shader模板的源码进行了解析，然后还讲解了运动模糊屏幕特效的实现方法。

[Unity3D和Android交互（Android调用Unity的场景）](http://lib.csdn.net/article/47/28128?knId=1267)

Unity3D非常适合制作Android的游戏，但是制作普通的应用来说似乎没有在Eelipse下如此完美和得心应手。另外，一些硬件模块和软件的功能都已经在Eclipse下开发完成。因此，如果可以仅仅在Android端调用Unity中的场景，并实现一些交互就非常完美了。

[Unity3D游戏开发之编辑器扩展程序开发实例](http://lib.csdn.net/article/47/24011?knId=1267)

开发Unity3D编辑器扩展程序的命名空间主要是UnityEditor，在该命名空间下常用的类有EditorGUI、EditorGUILayout、EditorWindow等。为Unity3D编辑器开发的扩展程序同样是一种脚本，通常需要将脚本文件放在项目资源文夹下的Editor文件夹中，即Assets/Editor。   
![图片描述](https://img-blog.csdn.net/20160816104210105)



扫描二维码，获取Unity3D知识库全部内容

**更多内容请关注[CSDN知识库](lib.csdn.net)。目前，CSDN已发布30多个技术领域知识库，每个知识库包含了专家精心绘制的知识图谱，及由特邀编辑（领域专家）从海量数据中层层筛选出的精华内容和资源（学习视频、实践Demo、图书）。**

   
  