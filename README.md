# 2020年你应该选择哪个Java微服务框架  -- 探讨 Micronaut， Quarkus 和  Spring Boot ，及各自的优缺点

至今，Java仍旧是用来构建web应用的最流行编程语言之一，但是它不得不面对诸如Go, Python, 和 TypeScript 等新语言的严峻挑战

在Java世界里，Spring框架早已成为微服务开发过程中的事实标准。通过诸如Spring Boot和Spring Data这样的库，Spring框架变得简单易用，大部分场景下，开发过程高效无痛点。

然而，最近几年一些新框架不断涌现，声称可以降低java应用的启动时间和内存占用。我最近一直在用Java去构建大型的微服务架构应用，再次之前，我查了哪些Java框架最合适微服务架构

因此，我主要关注在框架带来的易用性和资源管理

Spring虽然是Java平台最流行的框架，但是从来没有人说它是最好的框架。在资源管理这方面，尤其是单进程所需要的性能开销这块，Spring差强人意。在应用服务开发的旧时代，这不是一个大问题，因为进程实例数量不多。然而，随着微服务架构的崛起，我们会有大量的进程，这就是得Sring的这个问题更加突出。Christian Lusardi最近就这么说：

“我发现就算一个简单的java应用，要是用了Spring Boot，把它跑起来，那么至少需要1GB的内存。要是开发一个中间件，这样的开销还可以接受，但是对于微服务架构来说，太糟糕了!”


## 可选框架
### Spring
Spring是2003年面世的，以应对旧时代Java企业级开发的复杂性。Spring以依赖注入和面向切面编程为核心，演进成一个易用的web应用开发框架。Spring有着非常多的文档，广泛的使用率和数不清的库, 让开发者高效的创建和维护应用程序，并且提供了扁平的学习曲线。

Spring通过反射在运行期间执行依赖注入。当一个spring applicaiton启动时，在类路径(classpath)中，被标记的类（annotated classes）会被扫描到，由此，具体的类对象被实例化和被连接

虽然这提升应用程序的弹性，但是也使得应用程序的启动时间变慢，并且内存开销变大。同时，这个机制使得迁移到GraalVM变得非常困难，因为GraalVM不支持反射。




### Micronaut
Micronaut是一个现代化的微服务架构框架，由Grails框架作者在2018年创造。

它提供了所有必要的工具来创造功能全面的微服务应用。同时，它的目标是赋予应用程序快速的启动时间和更低的内存开销。这一切都是在编译期间而非运行期间，使用java annotation处理器执行依赖注入，创建面向切面代理，配置应用程序。

Micronaut的许多API从Spring 和 Grails中获得灵感。这样的设计快速吸引了新开发者的注意。Micronaut提供了很多的模块，诸如Micronaut HTTP, data, security, 和连接其他技术的适配器。然而，就成熟度而言，Micronaut这些库要落后于Spring里对应库


### Quarkus
Quarkus 是在2019年由于红帽创造，是一个Kubernetes原生的java框架框架。它依托于 MicroProfile, Vert.x, Netty, 和 Hibernate

Quarkus的目标是让java在Kubernetes环境中有着更快的启动速度，更低的内存开销和近乎瞬间的扩容伸缩能力，
并让java在Kubernetes环境成为一个主导平台。Quarkus通过自定义Maven插件在编译期间做尽可能做更多的工作

Quarkus使用了大量已存在的标准技术，同时对扩展开放。然而这个项目是一年前才开始的，这些扩展的成熟度和兼容性还不明确，很有可能在将来放生改变 

### Helidon MicroProfile
MicroProfile 项目始于2016年，那时候大家，对于Oracle会在Java企业级开发这块持续发力，觉得前途未卜
像它的先驱JEE, MicroProfile只是一份规范，可以被不同的具体架构来实现。

随后，许多具体的实现出现在大家面前，其中最著名的是Payara Micro 和 Helidon MP。Payara是一种起源于GlassFish的 Jakarta企业级服务器。也是MicroProfile一个实现。Helidon则是一个运行时，由Oracle公司在2018年发起,并提供对于MicroProfile规范的实现

虽然它们都来自于JEE，并且MicroProfile规范文档成熟与完善，但是缺少了针对现代技术的适配器和像是Spring Data 和 Spring Security这样的库的替代

也因此，MicroProfile的未来是不明朗的，同时Jakarta EE也刚刚开始。
很有可能两个项目会合并，或者至少紧密合作。


## 框架的比较

为了对上面提及过的框架进行比较，对每一个框架，我都创建了一个简单的应用程序，程序由REST接口和数据库连接器组成。REST接口 对objects做增删改查操作，数据库连接器则把这些objects存入数据库中。

如果一个框架支持多种方式接入数据库，我会一一实现这些变种，然后对这些应用程序做性能作比较。

我把这些应用跑在OpenJDK Docker镜像里。如果某个框架支持对原生GraalVM镜像生成，我也会把这些拿来作比较。你也可以看下我的另一篇文章 “Reactive Database Access with R2DBC, Micronaut and GraalVM” 来获得更多关于GraalVM的资讯
这些源码你可以在GitHub上找到，地址是https://github.com/lizzyTheLizard/medium-java-framework-compare

我主要从这几个关键点来比较这些应用程序的性能

- 有多容易去实现这些程序样例？为了能够实现这些框架，我不得不去查看相关文档，并同时在stack overflow这类的平台上去寻找相关信息。

- 要编译一个程序要花多久的时间？我测量了执行一次程序构建所需要的时间，这包含了Docker镜像生成时间。至于GraalVM这类，则包含了生成原生镜像所花的时间。

- 启动一个应用程序要花多少时间？我测量了应用程序在敲下docker up命令之后，与它第一次能够正确响应HTTP请球之前的所需要的时间。同时我也比较了程序启在闲置状态下的内存占用。

- 负载:应用程序在高峰时期能够处理多少请求？我使用了JMeter来做压力测试，其中有25%的请求来执行程序的写操作,有75%的请求来做数据库读操作。在程序达到高负载的状态，测量它的内存占用。

我在谷歌云上面完成了所有的测试。虚拟机采用了四核的intel Haswell架构CPU和15GB的内存。系统则是Ubuntu 19.01。
所有的测试都重复都做了多次，以避免干扰因素。你可以在https://github.com/lizzyTheLizard/medium-java-framework-compare/tree/master/compare。 找到这些脚本和原始数据



## 结论

### 程序开发的易用性

由于之前我已对Spring Boot有一些使用经验，所以这方面的比较，有一点点的不公平。然而，当你查看了Spring的文档，相关信息与例子，Spring是目前为止最容易上手的框架。

Micronaut的文档也做得很棒，它有着与Spring和Grail类似的API，因此，对于用过Spring的开发者来说也是非常容易上手的。

Quarkus的学习曲线更陡峭一些，我认为，相较于Spring与Micronaut，Quarkus的API和库缺乏成熟度，尤其数据库连接方面，易用性特别糟糕。

Helidon在易用性方面是最糟糕的，因为我花了非常大的努力才使得应用程序跑起来。


### 编译
所有框架只要是使用了OpenJDK，那么编译时间是差不多的，在6.98秒（使用JDBC的Spring应用程序）到10.7秒（使用Quarkus的应用程序）之间。

而原生GraalVM镜像生成的时间开销非常大，在231.2秒 (使用 JDBC的Micronaut应用程序) 到 351.7 秒 (使用JPA的Micronaut 应用程序 )之间. 从开发过程来说，这使得原生镜像变得基本无意义， 编译一个简单的应用程序需要等待4分钟，这是很过分的事。

。。。

### 启动
使用了spring Data的Spring Boot应用程序平均花费8.16秒来启动。当去除了JPA和Spring Data，这个时间降到了5.8秒。



这里，Micronaut（使用JPA时，花费5.08秒启动 ， 使用JDBC时花费3.8秒）和Quarkus（花费5.7秒启动）都达到了他们的承诺，可以更快的速度启动应用程序。

只有Helidon MP 比 Spring 启动速度更慢 -- 平均要8.27秒 。

GraalVM，在启动方面，表现最好，启动时间在1.39 秒 (Quarkus应用程序) and 1.46 秒 (使用了JDBC的Micronaut应用程序)
远远快于基于OpenJDK的那些实现。

程序启动后的内存使用非常相似。Spring会在使用了Spring Data的情况下占用420 MB的内存，在使用了JDBC的情况下占用 261 MB内存

Micronaut 在使用了JPA的情况下，占用262MB的内存，在使用了 JDBC的情况下 占用178MB的内存


Quarkus表现得更好一些，内存开销在197 MB。Helidon MP则与Spring Boot类似，内存开销在414 MB


### 高负载

在高负载情况下， Spring Boot 表现相当的好，在使用了Spring Data情况下，每秒能够处理 342个请求，内存开销是581 MB
在使用了JDBC情况下每秒能够处理216个请求，内存开销是484 MB 。
毫无疑问，Helidon是排在最后，在高负载情况下，内存开销超过1 GB ，处理请求只有每秒175个

其他的框架高负载情况下，在400 请求/秒（使用了原生镜像的Quarkus应用程序） 到197 请求/秒（跑在OpenJDK上的Quarkus应用程序） 之间。 Micronaut相关的实现也在这个数值之间，当Micronaut搭配JDBC时，每秒处理能力 要比Micronaut搭配JPA时要稍微好一些。当Micronaut搭配原生镜像时要比Micronaut搭配OpenJDK时要好一些。

就内存使用角度而言，Quarkus搭配OpenJDK，出奇的好，内存开销仅要255 MB，这要远远低于Quarkus搭配原生镜像的时候，Quarkus搭配原生镜像时，平均开销在 368 MB 


## 总结

相较于Spring 和 MicroProfile这样现有的老框架，Micronaut和Quarkus这类的新框架， 有着更快的启动速度，和更低的内存占用。

但是这些优势是有条件的，仅当程序在空闲时期和低负载状态下才成立。在结合原生GraalVM镜像时，这样的优势更加突出。
但是在高负载情况下，这些优势就不明显了，即使是用了原生GraalVM镜像。

目前为止，我认为，Spring仍旧是拥有着最好的开发体验，最合适于微服务应用的java框架，即使它在启动速度方面表现糟糕

让我感到惊讶的是，使用 Hibernate/JPA/Spring Data，会给程序带来巨大的开销，即使是一个非常简单的程序，在使用了这些库后，对内存开销和每秒请求率影响巨大。在此，我特别喜欢Micronaut Data的解决方案，它自动生成相应代码，而不再需要JPA。这个功能真的应该加到Spring Data里去。

原生GraalVM镜像可以得程序在启动速度方面变得非常的快，内存效率也不错。但是当高负载情况下，就体现不出巨大的优势了。
同时，原生GraalVM镜像也带来了额外的痛点，使得编译时间大大增加，这就让这门技术，仅在要求程序快速
启动的场景下，才有意义 -- 比如说 无服务架构（serverless）或者 要求快速扩容伸缩的场景。在其他场景下，投入远大于回报

