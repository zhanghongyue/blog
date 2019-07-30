---
title: 使用Java构建微服务
date: 2016-03-08 17:33:32
tags: CSDN迁移
---
 版权声明：本文为博主原创文章，未经博主允许不得转载。 https://blog.csdn.net/mengyidan/article/details/50510489   
  Building Microservices With Java   
 使用Java构建微服务   
 【编者按】微服务背后大的理念将大型、复杂且历时长久的应用在架构上设计为内聚的服务，这些服务能够随着时间的流逝而演化。本文作者主要介绍了利用Java生态来构建微服务，并且分析了每种方法的利弊。

 Quick View   
 Strategies for building microservices in the Java ecosystem include container-less, self-contained, and in-container.   
 Container-less microservices package the application, with all its dependencies, into a single “fat” JAR file.   
 Self-contained microservices also package a single fat JAR, but these also include an embedded framework with optional third-party libraries that will be compatible.   
 In-container microservices package an entire Java EE container and its service implementation in a Docker image.

 
# 快速预览

  
  2. 在Java生态系统中构建微服务的策略主要有：ontainer-less、self-contained、in-container； 
  4. Container-less微服务把应用程序及起所有依赖打包成一个单一的jar文件； 
  6. Self-contained微服务也打包成一个单一的Jar文件，但它还包含一个可能含有第三方库的嵌入式框架； 
  8. In-container微服务是打包成一个完整的Java EE容器，并且它的服务是在Docker image中实现。  Architectures based on microservices introduce new challenges for architects and developers. An ever-increasing list of languages and tools brings with it the capabilities to conquer this challenge. Java is no exception. This article explores different approaches to building microservices using the Java ecosystem.   
 基于微服务的架构设计是架构师和程序员门面临的一项新挑战。然而，语言及工具的不断更新，架构师们完全有能力征服这样的挑战。Java也不例外，本文探讨了使用Java生态来构建微服务的几种不同方式。

 Introduction   
 This article does not discuss whether microservices are good or evil, nor whether you should design your app for microservices upfront or extract the services as they emerge from your monolith application.

 
# 介绍

 本文并不会讨论微服务的好与坏，也不会建议让你提前为微服务设计应用程序，当它们出现时，也不会从你的monolith应用程序中抽取服务。

 The approaches described here are not the only ones available, but they should give you a pretty good overview of several possibilities. Even though the Java ecosystem is the main focus in this article, the concepts should be transferrable to other languages and technologies.   
 本文介绍的方法并不是唯一的，但应该会给你几种不一样的方法概述。尽管本文的重点是使用Java来构建微服务，但这些概念同样可以转移到其它语言和技术上。

 I have named the approaches in this article container-less, self-contained, and in-container. These terms may not be entirely established, but they fulfill their purpose here to differentiate the approaches. I will describe what each means in the sections that follow.   
 笔者把本文用到的方法命名为container-less、self-contained和 in-containe。接下来会对这几种方法进行详细地描述。

 Container-less   
 In the container-less approach, the developer treats everything on top of the JVM as a part of the application.

 
# Container-less

 在这个方法中，开发者会在JVM顶端处理任何事情，并且作为应用程序的一部分。

 The container-less approach enables so-called single JAR deployment (also called a “fat JAR deployment”). This means that the application, with all its dependencies, is packaged as a singleJAR file and can be run as a standalone Java process.   
 container-less方法也就是所谓的单jar部署（也可称作“fat jar部署”）。这也就意味着应用程序及其所有的依赖都会被打包成一个单一的jar文件，并且作为一个独立的Java程序运行。   
 ![这里写图片描述](https://lh4.googleusercontent.com/QU5dat6jx2UMmEEZlSUnyG-VAvTL984XaWKYJkvYIhvQUIk5xi1O58Muq9Jq6oC0Qju3sLiFCer8ow9HOVsaao4Dz_fjkrUUa0gdy_wQ3HCfZBcDjYQ0pGXce72MsFO38kDsHCmR)

 
```
$ java -jar myservice.jar
```
 One advantage of this approach is that it is extremely easy to start and stop services as needed when scaling up or down. Another advantage is convenient distribution. You just need to pass one JAR file around.   
 该方法的第一个优点就是当应用程序进行规模化伸缩的时候，服务很容易启动和停止；另一优点是方便部署，你只需要传递一个jar文件即可。   
 A downside of this approach is library compatibility. You are on your own for things like transaction support, or you need to bring in a third party library that provides support for this scenario. Later on, if you need support for something else, say persistence, you may need to fight compatibility issues between the libraries.   
 该方法的缺点就是库的兼容性。开发者在使用的时候，常常需要引入第三方库，而后，如果你需要更多的支持，你就需要解决库之间的兼容性问题。

 Self-contained   
 Another variant of single JAR deployment is building your services with an embedded framework. In this approach, the framework provides implementations of the services needed and the developer can choose which to include in the service.

 
# Self-contained

 另一个单jar部署就是使用一个嵌入式框架来构建服务。在这个方法中，框架提供了所需服务的实现，开发者可以选择一些东西引用到服务中。   
 You may argue that this is exactly the same as the container-less solution, but I like to distinguish them here since the self-contained approach actually gives you a set of third party libraries that you know are compatible.   
 你可能会认为这个方法与container-less完全一样，但笔者认为，它两的区别是self-contained方法会允许你使用一套第三方库，前提是你已经知道这些库是兼容的。所以，这个方法不存在库兼容性问题。   
 ![这里写图片描述](https://lh6.googleusercontent.com/9jNWZP04T25vGZ-zoeNS90yiEg6t4wh7IDm0StNoVq9uT6AG5gwcPEtALUaRd3AtTtb4LZkS4Etlz3L_LpYf8YX2aYPxWf26VXUgpaWHJtj-sz2xzzE4q7-kk1lP4wftIT0KeqR1)

 该方法可能涉及像Spring Boot和Wildfly Swarm这些工具。   
 Spring Boot   
 Spring Boot and the Spring Cloud Netflix projects have excellent support for building microservices in Java. Spring Boot allows you to pick and choose various parts of the Spring ecosystem, as well as popular external tools and then package them along with your application in a JAR file. Spring Initializr allows you to do this with a simple checkbox list form. A simpleHello World service is shown in this example: Gist Snippet

 
# Spring Boot

 在Java中，[Spring Boot](http://projects.spring.io/spring-boot)和[Spring Cloud Netflix](http://cloud.spring.io/spring-cloud-netflix)项目对构建微服务提供了很好地支持。Spring Boot允许你选择各种Spring工具和流行的外部工具，然后把它们和你的应用打包成一个jar文件。[Spring Initializr](https://start.spring.io/)提供了一个简单的复选框列表来完成上面这些事。一个简单的Hello World服务示例：[Gist Snippet](https://gist.github.com/ivargrimstad/8bbc2b1085948a38fcdd)

 Wildfly Swarm   
 A Java EE counterpart to Spring Boot is WildFly Swarm. It enables you to pick and choose which parts of the Java EE specification you need and package them and your application in a JAR file. The Hello World example looks like this: Gist Snippet

 
# Wildfly Swarm

 在Java EE中，和Spring Boot相对应是[Wildfly Swarm](http://wildfly.org/swarm)。它允许你根据自己的需求挑选Java EE规范，然后把它们和你的应用程序打包成一个jar文件。这里有一个简单的Hello World示例：[Gist Snippet](https://gist.github.com/ivargrimstad/2d2fee6193e33bc554b7)。

 The advantage of the self-contained approach is that you get to select only what you need in order for the service to run.   
 self-contained方法的优点是你可以只选择满足服务运行的东西。

 One disadvantage of this approach is that the configuration is a little more complex and the resulting deliverable JAR file is a bit bigger since it builds in the required container capabilities in the actual service.   
 这种方法的缺点是配置更加复杂，由此产生的jar文件要稍大一些，因为它是构建实际服务所需的容器功能。

 In-container   
 While it seems like a lot of overhead to require an entire Java EE container to be able to deploy a microservice, keep in mind that some developers argue that the the ‘micro’ in microservice does not necessarily mean that the service is small or simple.

 
# In-container

 虽然看起来在Java EE容器中部署一个微服务是需要很大开销的，然而，一些开发者认为，微服务中的“微”并不表示该服务的小或者简单。   
 ![这里写图片描述](https://lh6.googleusercontent.com/IcDvxBKZf6OiXyrUIkMofX2HT7tveNbmSP2w4WlNrr3TrSQ8cnbyq8NXZ7U5JDncAWhJrA2HVH8jnUIm86m7crpnVeetSyaaI7TcLIm8rYqDIpfl1TE_WTWAsSkdEAa1TFqxCkbZ)

 In these cases it may seem appropriate to treat the Java EE container as the required platform. Thus, the only dependency you need is the Java EE API. Note that the dependency is providedsince the implementation is provided by the container. That means that the resulting WAR file is extremely lean. The implementation of the service is the same as the Wildfly Swarm example above. See it here: Gist Snippet   
 在这些案例中，将Java EE容器作为所需平台似乎是合适的。因此，你所需的唯一依赖就是Java EE API。注意，由于其实现是由容器提供的，因此该依赖项已经满足了。这也就意味着所产生的war文件是非常精简的。该服务的实现与上面Wildfly Swarm的例子是一样的：[Gist Snippet](https://gist.github.com/ivargrimstad/c368221fa079285856e7)。

 The advantage of this approach is that the container provides tested and verified implementations of standard functionality through standard APIs. Thus, you as a developer can focus entirely on the business functionality and leave the plumbing out of the application source.   
 该方法的优点是，容器通过标准APIs提供了测试和验证标准功能的实现。因此，作为开发者可以完全聚焦业务功能，并且在应用代码之外维护底层代码。

 Another advantage of this approach is that the actual application code does not depend on the Java EE application server it is deployed to, whether it is GlassFish, WildFly, WebLogic,WebSphere, or any other Java EE compatible implementations.   
 另外一个优点是应用程序代码不依赖Java EE应用服务器，无论该应用是部署到[GlassFish](https://glassfish.java.net/)、[WildFly](http://wildfly.org/)、[WebLogic](http://www.oracle.com/us/products/middleware/cloud-app-foundation/weblogic/overview/index.html)、[WebSphere](http://www.ibm.com/software/websphere)还是任何其它Java EE兼容性实现。   
 The disadvantage is that you need to deploy the service into a container and thus increase the complexity of the deployment.   
 该方法的缺点是你需要把服务部署到容器中，这样就增加了部署的复杂性。

 Docker   
 This is where Docker comes in. By packaging the Java EE Container and the service implementation in a Docker image, you achieve more or less the same result as you would with a single JAR deployment. The difference is that now the service is contained in a Docker image and not a JAR file.

 
# Docker

 现在来谈谈[Docker](https://www.docker.com/)。通过把Java EE容器和服务实现打包到Docker镜像，你可以得到与单一的jar部署差不多的结果。唯一的不同是服务打包在Docker镜像中，而不是一个jar文件。

 
```
Dockerfile
FROM jboss/wildfly:9.0.1.Final
ADD myservice.war /opt/jboss/wildfly/standalone/deployments
```
 Docker引擎启动Docker镜像来唤醒服务：

 
```
$ docker run -it -p 8081:8080 myorganization/myservice
```
 Snoop   
 The observant reader may have noticed the @EnableEurekaClient annotation in the Spring Boot code snippet from before. This annotation registers the service with Eureka, making it discoverable by service consumers. Eureka is a part of the Spring Cloud Netflix bundle and is an extremely easy-to-use and configure service discovery solution.

 
# Snoop

 细心的读者可能已经在先前的Spring Boot代码段中注意到了@EnableEurekaClient注解。该注解在[Eureka](https://github.com/Netflix/eureka/wiki/Eureka-at-a-glance)中注册服务，使其能够被服务消费者发现。Eureka是Spring Cloud Netflix包的一部分，并且是一个极其容易使用和配置服务发现的解决方案。

 Java EE does not offer this functionality out of the box, but there are several open-source solutions available. One such solution is Snoop, which functions in a similar way to Eureka. The only thing needed to make a Java EE microservice available for service lookup is the @EnableSnoopClient annotation as shown in this example: Gist Snippet   
 Java EE在外部并没有提供这样的功能，但有一些开源解决方案可以使用。其中一个就是[Snoop](https://github.com/ivargrimstad/snoop)，[和Eureka功能相似](https://github.com/ivargrimstad/snoop)。唯一需要做的是使用@EnableSnoopClient注解，如本例所示：[Gist Snippet](https://gist.github.com/ivargrimstad/34bfe4b5368a35d30007)。

 Conclusion   
 Java is an excellent choice when building microservices. Any of the approaches described in this article will get things done. The most appropriate method for your particular case depends on the requirements of the service. For simpler services, a container-less or self-contained service is the better choice, but more advanced services may be faster and easier to implement with the power of an in-container implementation. Either way, Java is a proven ecosystem for implementing microservices.

 
# 总结

 当构建微服务时，Java是一个非常好的选择。本文中介绍的任何一个方法都可以完成。当然，最好的方法还得取决于服务需求。对于简单的服务而言，container-less或者self-contained服务就是不错的选择。借助in-container开发者可以更快更简单地实现更高级的服务。无论哪种方法，构建微服务，Java都是一种行之有效的生态系统。

 原文地址：[https://dzone.com/articles/building-microservices-with-java](https://dzone.com/articles/building-microservices-with-java)

   
  