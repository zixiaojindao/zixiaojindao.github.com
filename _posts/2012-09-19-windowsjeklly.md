---
layout: post
title: "windows下搭建jeklly博客"
description: "jeklly作为博客引擎是在是程序员们的一大福音，github的pages功能对于jeklly的完美支持可以让你轻松拥有自己域名的博客，这篇文章主要介绍在windows搭建jeklly博客的步骤。"
category: 
tags: []
---
###1.什么是git
git是一个分布式版本控制工具(DVCS)，不需要服务端软件支持(即使在地铁里也可以正常commit)，Linux内核开发用的版本控制工具就是它。
常用的SVN属于集中式版本控制工具(CVCS)，需要在服务端开启SVN服务，然后客户端checkout,commit,update。
更详细的介绍请参考<a href="http://baike.baidu.com/view/1531489.htm" target="_blank">百度百科git</a>
###2.什么是github
<a href="https://github.com" target="_blank">GitHub</a>可以托管各种git库，并提供一个web界面，但与其它像 SourceForge或Google Code这样的服务不同，GitHub的独特卖点在于从另外一个项目进行分支的简易性。为一个项目贡献代码非常简单︰首先点击项目站点的fork的按 钮，然后将代码检出并将修改加入到刚才分出的代码库中，最后通过内建的pull request机制向项目负责人申请代码合并。已经有人将GitHub称为代码玩家的MySpace。
github的<a href="https://pages.github.com" target="_balank">pages</a>功能是给用户提供发布网页的功能，你可以自己提交html文件让github帮你发布，你也可以提交按照固定模板的文档来让github帮你为这些文档生成html。下面是别人总结过的github pages的优缺点:
github Pages有以下几个优点：

* 轻量级的博客系统，没有麻烦的配置
* 使用标记语言，比如Markdown
* 无需自己搭建服务器
* 根据github的限制，对应的每个站有300MB空间
* 可以绑定自己的域名  

当然他也有缺点：

* 使用Jekyll模板系统，相当于静态页发布，适合博客，文档介绍等。 * 动态程序的部分相当局限，比如没有评论，不过还好我们有解决方案。
* 基于Git，很多东西需要动手，不像Wordpress有强大的后台

###3.原理
我解决问题，一般会从原理上来思考。一旦你解决了原理，你碰到问题思考的程度就深，也容易找出问题的突破口。利用github写博客的原理，其实就是利用了
github pages的服务。github pages本来是用来给托管在github上的项目展示说明和文档的,它会在你的项目中产生一个gh-pages的分支，这个分支里按照一定
的目录和文件格式生成，最最重要的是有一个index.html。github向你展示的就是这个页面，你可以在index中写内容，也可以加链接。链接可以是外部的也可以是
内部的，所以你可以在分支里添加其他html，然后把这些html的链接添加在index.html中，你整个静态的网站也就形成了。但是如果仅仅是这些还不够，我们写博客
关注的内容，需要把博主从繁重的html标记中解脱出来，因此有了jellky. jeklly可以使你仅仅按照markdown的语法来书写博文，通过自定义的css样式和页面结构来
生成最后的html，这样博主在写文章的时候只关注markdown语法的博文就可以了，至于css样式和页面结构，jellky也提供了几个模板供你选择，如果你不满意的话可以
去把更改模板，如果你还不满意的话可以把别人的在github上的博客fork到自己的版本库中，使用他们的网站主题。

更令人兴奋的是，如果你在github上的用户名是USERNAME,并且博客项目的名称也是USERNAME.github.com的话，github可以为你产生一个二级域名USERNAME.github.com，
这样你就可以通过访问USERNAME.github.com来访问你的博客了。如果你有自己的一级域名的话，可以提供给github,这样就可以直接访问这个以及域名来访问你的博客了。

###4.一步一步搭建博客
这里主要介绍利用jeklly bootstrap，即官方推荐的方法。

###4.1 搭建环境(windows 版)
####4.1.1 安装github for windows
这一步其实是为了安装git，我最开始安装的是<a href="http://msysgit.github.com/" target="_blank">git for windows</a>。后来我发现有github for windows这个
好东西，不仅有了git，而且有了和github的接口，快哉，快哉。安装方法很简单，在http://windows.github.com/上下载最新版的release，按部就班的安装就ok。安装完了
之后你的桌面就有了GitHub和Git Shell。GitHub是一个图形化的程序，可以图形化的使用git命令和github通信。Git Shell一个terminal的git工具，可以使用各种git命令。
安装完之后你第一步要做的事情就是配置你的github账户，只要运行GitHub，然后输入用户名密码就ok啦。如果跳过这个步骤会使得你在使用Git Shell来push到github上会
放生ssh key的问题。

总结一下：

1.安装<a href="http://windows.github.com/" target="_blank">github for windows</a>
2.运行GitHub,配置github账户。


