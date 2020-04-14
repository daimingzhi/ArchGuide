Spring官网阅读 | 总结篇

> 接近用了4个多月的时间，完成了整个《Spring官网阅读》系列的文章，本文主要对本系列所有的文章做一个总结，同时也将所有的目录汇总成一篇文章方便各位读者来阅读。

下面这张图是我整个的写作大纲![微信截图_1111222](C:\Users\dell\Desktop\微信截图_1111222.png)

对应的文章目录汇总如下：

[Spring官网阅读（一）容器及实例化](https://blog.csdn.net/qq_41907991/article/details/103589868)

> 本文主要涉及到官网中的`1.2`,`1.3`节。主要介绍了什么是容器，容器如何工作。

[Spring官网阅读（二）（依赖注入及方法注入）](https://blog.csdn.net/qq_41907991/article/details/103589884)

> 本文主要涉及到官网中的`1.4`小节，主要涉及到Spring的依赖注入

[ Spring官网阅读（三）自动注入](https://blog.csdn.net/qq_41907991/article/details/103589903)

> 在对依赖注入跟方法注入有一定了解后，我们需要立马学习自动注入。通过这篇文章你会知道真正的`byName`跟`byType`。本文主要涉及到官网中的`1.4`小节

[Spring官网阅读（四）BeanDefinition（上）](https://blog.csdn.net/qq_41907991/article/details/103589939)

> 本文主要涉及到官网中的`1.3`及`1.5`中的一些补充知识。同时为我们`1.7`小节中`BeanDefinition`的合并做一些铺垫

[Spring官网阅读（五）BeanDefinition（下）](https://blog.csdn.net/qq_41907991/article/details/103866987)

> 上篇文章已经对BeanDefinition做了一系列的介绍，这篇文章我们开始学习BeanDefinition合并的一些知识，完善我们整个BeanDefinition的体系，Spring在创建一个bean时多次进行了BeanDefinition的合并，对这方面有所了解也是为以后阅读源码做准备。本文主要对应官网中的1.7小节

[Spring官网阅读（六）容器的扩展点（一）BeanFactoryPostProcessor](https://blog.csdn.net/qq_41907991/article/details/103867027) 

> 之前的文章我们已经学习完了BeanDefinition的基本概念跟合并，其中多次提到了容器的扩展点，这篇文章我们就开始学习这方面的知识。这部分内容主要涉及官网中的1.8小结。按照官网介绍来说，容器的扩展点可以分类三类，BeanPostProcessor,BeanFactoryPostProcessor以及FactoryBean。本文我们主要学习BeanFactoryPostProcessor，对应官网中内容为1.8.2小节

[Spring官网阅读（七）容器的扩展点（二）FactoryBean](https://blog.csdn.net/qq_41907991/article/details/103867036)

> 在上篇文章中我们已经对容器的第一个扩展点（BeanFactoryPostProcessor）做了一系列的介绍。其中主要介绍了Spring容器中BeanFactoryPostProcessor的执行流程。已经Spring自身利用了BeanFactoryPostProcessor完成了什么功能，对于一些细节问题可能说的不够仔细，但是在当前阶段我想要做的主要是为我们以后学习源码打下基础。所以对于这些问题我们暂且不去过多纠结，待到源码学习阶段我们会进行更加细致的分析。
>
> 在本篇文章中，我们将要学习的是容器的另一个扩展点（FactoryBean）,对于FactoryBean官网上的介绍甚短，但是如果我们对Spring的源码有一定了解，可以发现Spring在很多地方都对这种特殊的Bean做了处理。话不多说，我们开始进入正文。

[Spring官网阅读（八）容器的扩展点（三）（BeanPostProcessor）](https://blog.csdn.net/qq_41907991/article/details/103867048)

> 在前面两篇关于容器扩展点的文章中，我们已经完成了对BeanFactoryPostProcessor很FactoryBean的学习，对于BeanFactoryPostProcessor而言，它能让我们对容器中的扫描出来的BeanDefinition做出修改以达到扩展的目的，而对于FactoryBean而言，它提供了一种特殊的创建Bean的手段，能让我们将一个对象直接放入到容器中，成为Spring所管理的一个Bean。而我们今天将要学习的BeanPostProcessor不同于上面两个接口，它主要干预的是Spring中Bean的整个生命周期（实例化—属性填充—初始化—销毁），关于Bean的生命周期将在下篇文章中介绍，如果不熟悉暂且知道这个概念即可，下面进入我们今天的正文。

[ Spring官网阅读（九）Spring中Bean的生命周期（上）](https://blog.csdn.net/qq_41907991/article/details/104786530)

> 在之前的文章中，我们一起学习过了官网上容器扩展点相关的知识，包括FactoryBean，BeanFactroyPostProcessor,BeanPostProcessor，其中BeanPostProcessor还剩一个很重要的知识点没有介绍，就是相关的BeanPostProcessor中的方法的执行时机。之所以在之前的文章中没有介绍是因为这块内容涉及到Bean的生命周期。在这篇文章中我们开始学习Bean的生命周期相关的知识，整个Bean的生命周期可以分为以下几个阶段：
>
> 实例化（得到一个还没有经过属性注入跟初始化的对象）
> 属性注入（得到一个经过了属性注入但还没有初始化的对象）
> 初始化（得到一个经过了初始化但还没有经过AOP的对象，AOP会在后置处理器中执行）
> 销毁
> 在上面几个阶段中，BeanPostProcessor将会穿插执行。而在初始化跟销毁阶段又分为两部分：
>
> 生命周期回调方法的执行
> aware相关接口方法的执行
> 这篇文章中，我们先完成Bean生命周期中，整个初始化阶段的学习，对于官网中的章节为1.6小结

[Spring官网阅读（十）Spring中Bean的生命周期（下）](https://blog.csdn.net/qq_41907991/article/details/104786584)

> 在上篇文章中，我们已经对Bean的生命周期做了简单的介绍，主要介绍了整个生命周期中的初始化阶段以及基于容器启动停止时LifeCycleBean的回调机制，另外对Bean的销毁过程也做了简单介绍。但是对于整个Bean的生命周期，这还只是一小部分，在这篇文章中，我们将学习完成剩下部分的学习，同时对之前的内容做一次复习。整个Bean的生命周期，按照我们之前的介绍，可以分为四部分
>
> 实例化
> 属性注入
> 初始化
> 销毁
> 本文主要介绍实例化及属性注入阶段

[ Spring官网阅读（十一）ApplicationContext详细介绍（上）](https://blog.csdn.net/qq_41907991/article/details/104890350) 

> 在前面的文章中，我们已经完成了官网中关于IOC内容核心的部分。包括容器的概念，Spring创建Bean的模型BeanDefinition的介绍，容器的扩展点（BeanFactoryPostProcessor，FactroyBean，BeanPostProcessor）以及最重要的Bean的生命周期等。接下来大概还要花三篇文章完成对官网中第一大节的其它内容的学习，之所以要这么做，是笔者自己粗读了一篇源码后，再读一遍官网，发现源码中的很多细节以及难点都在官网中介绍了。所以这里先跟大家一起把官网中的内容都过一遍，也是为了更好的进入源码学习阶段。
>
> 本文主要涉及到官网中的1.13,1.15,1.16小节中的内容以及第二大节的内容

[Spring官网阅读（十二）ApplicationContext详解（中）](https://blog.csdn.net/qq_41907991/article/details/104934770) 

> 在上篇文章中我们已经对ApplicationContext的一部分内容做了介绍，ApplicationContext主要具有以下几个核心功能：
>
> 国际化
> 借助Environment接口，完成了对Spring运行环境的抽象，可以返回环境中的属性，并能出现出现的占位符
> 借助于Resource系列接口，完成对底层资源的访问及加载
> 继承了ApplicationEventPublisher接口，能够进行事件发布监听
> 负责创建、配置及管理Bean
> 在上篇文章我们已经分析学习了1，2两点，这篇文章我们继续之前的学习

[Spring官网阅读（十三）ApplicationContext详解（下）](https://blog.csdn.net/qq_41907991/article/details/105197581) 

> 在前面两篇文章中，我们已经对ApplicationContext的大部分内容做了介绍，包括国际化，Spring中的运行环境，Spring中的资源，Spring中的事件监听机制，还剩唯一一个BeanFactory相关的内容没有介绍，这篇文章我们就来介绍BeanFactory，这篇文章结束，关于ApplicationContext相关的内容我们也总算可以告一段落了。本文对应官网中的1.16及1.15小结

[ Spring官网阅读（十四）Spring中的BeanWrapper及类型转换](https://blog.csdn.net/qq_41907991/article/details/105214244) 

> BeanWrapper是Spring中一个很重要的接口，Spring在通过配置信息创建对象时，第一步首先就是创建一个BeanWrapper。这篇文章我们就分析下这个接口，本文内容主要对应官网中的`3.3`及`3.4`小结

[Spring官网阅读（十五）Spring中的格式化（Formatter）](https://blog.csdn.net/qq_41907991/article/details/105237926) 

> 在上篇文章中，我们已经学习过了Spring中的类型转换机制。现在我们考虑这样一个需求：在我们web应用中，我们经常需要将前端传入的字符串类型的数据转换成指定格式或者指定数据类型来满足我们调用需求，同样的，后端开发也需要将返回数据调整成指定格式或者指定类型返回到前端页面。这种情况下，Converter已经没法直接支撑我们的需求了。这个时候，格式化的作用就很明显了，这篇文章我们就来介绍Spring中格式化的一套体系。本文主要涉及官网中的3.5及3.6小结

[Spring官网阅读（十六）Spring中的数据绑定](https://blog.csdn.net/qq_41907991/article/details/105333258) 

> 在前面的文章我们学习过了Spring中的类型转换以及格式化，对于这两个功能一个很重要的应用场景就是应用于我们在XML中配置的Bean的属性值上，如下：
>
> <bean class="com.dmz.official.converter.service.IndexService" name="indexService">
> ​		<property name="name" value="dmz"/>
>  <!-- age 为int类型-->
> ​		<property name="age" value="1"/>
> </bean>
> 1
> 2
> 3
> 4
> 5
> 在上面这种情况下，我们从XML中解析出来的值类型肯定是String类型，而对象中的属性为int类型，当Spring将配置中的数据应用到Bean上时，就调用了我们的类型转换器完成了String类型的字面值到int类型的转换。
>
> 那么除了在上面这种情况中使用了类型转换，还有哪些地方用到了呢？对了，就是本文要介绍的数据绑定–DataBinder。

[ Spring官网阅读（十七）Spring中的数据校验](https://blog.csdn.net/qq_41907991/article/details/105337481) 

> 在前文中我们一起学习了Spring中的数据绑定，也就是整个DataBinder的体系，其中有提到DataBinder跟校验相关。可能对于Spring中的校验大部分同学跟我一一样，都只是知道可以通过@Valid / @Validated来对接口的入参进行校验，但是对于其底层的具体实现以及一些细节都不是很清楚，通过这篇文章我们就来彻底搞懂Spring中的校验机制。
>
> 在学习Spring中某个功能时，往往要从Java本身出发。比如我们之前介绍过的Spring中的国际化（见《Spring官网阅读（十一）》）、Spring中的ResolvableType（见《Spring杂谈》系列文章）等等，它们都是对Java本身的封装，沿着这个思路，我们要学习Spring中的数据校验，必然要先对Java中的数据校验有一定了解。
>
> 话不多说，开始正文！



