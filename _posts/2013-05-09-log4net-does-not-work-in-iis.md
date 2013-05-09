---
layout: post
title: "Log4Net Does Not Work in IIS"
description: "Log4Net是被广大程序员广泛使用的记录log的类库，我最近碰到的一个问题就是在visual studio中
配置正常的log4net，发布到iis中后就无法输出log了，我花了很多时间，终于解决了这个问题，现在把涉及到的
问题都罗列出来，希望可以帮助到大家。"
categories: 
- Headache Problems 
tags: 
- IIS
- Log4Net
---
###1.前提假设
本文已经假设你已经有了一个在visual studio正常工作的log4net配置,关于如何使用log4net,以及如何配置，你可以自行[google](https://www.google.com.hk/search?q=log4net+configuration)一下。

###2.几个可能的问题
####2.1权限问题
安全起见iis进程运行在一个非常低权限的账户上，这样可以保证iis下的应用不会破坏计算机的其他内容。所以，首先你应该找到你iis进程的账户信息，然后给予这个账号对你日志文件(或者目录)的读写权限。

找到iis进程的账户，有两种方法。

第一种通过iis管理器(控制面板->管理工具->Internet 信息服务(IIS)管理器),首先查看你的web应用的应用程序池，点击web应用的高级设置，如下图所示：

<img src="/images/iis-application-applicationpool.jpg" class="img-center" alt="thumb" >

然后在应用程序池中查看相应的应用程序池(比如我的是DefaultAppPool)的账户信息,点击应用程序池的高级设置，如下图所示：

<img src="/images/app-pool-account.jpg" class="img-center" alt="thumb">

第二种方法就是首先触发一下你的web应用，这时候，查看任务管理器(所有用户进程)中w3wp.exe的账户，如下图所示：

<img src="/images/application-pool-identities-624-appPoolid2.png" class="img-center" alt="thumb">

下一步就是赋予日志文件(或者目录)权限，右键日志文件->属性->安全->编辑->添加，添加对应的账户，然后给予读写权限。这一步需要注意的是，四个账户中ApplicationPoolIdentity对应的账户需要输入<code class="code">IIS AppPool\DefaultAppPool</code>,然后检查名字，就出来DefaultAppPool账户了。

如果你想测试权限的是否真的配置好了，可以在自己的web应用代码中主动加入一个写文件的测试(写到给予权限的目录里)，如果成功写入，表示权限是配好了。

如果这部分你有疑问可以参见[Application Pool Identities](http://www.iis.net/learn/manage/configuring-security/application-pool-identities)。

####2.2Log4Net配置文件位置
如果你使用visual studio的发布功能，发布了你的web应用到iis下。一般会在你的web目录中生成web.config文件和bin目录。bin目录中有你的web dll和配置文件，其中log4net.dll和log4net的配置文件也在bin目录中。

如果你的log4net配置在web.config中，那就应该没有问题了。但是很多时候，我们都是把log4net的配置文件放在一个单独的文件中，这个文件与log4net在同一目录下。当你发布iis web应用的时候，相对路径的主路径是在web.config所在的目录路径，所以你应该把bin目录中的log4net的配置文件拉出来放到web.config同一级目录下。

至此应该没有什么问题了。

