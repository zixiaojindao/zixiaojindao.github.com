---
layout: post
title: "apache整合tomcat和svn"
description: "你有一个tomcat搭建的jsp网站，如果你想加入svn服务的话，这篇文章就可以帮到你了。"
categories: 
- NetWork 
tags:
- apache
- tomcat
- svn
---
我在学校实验室的时候，有一天老师让我给我们的网站<http://gdm.fudan.edu.cn>加入svn服务使得实验室的同学可以
通过外网访问svn。于是我就去google上搜索可能的解决方案，首先我看到了apache的VisualSVN,这是一个图形化的svn
服务器，你可以选取一个本地端口开放给它。当我迅速搭建好之后并且已经可以在局域网中正常使用svn服务的时候，我
想到了如何能够使得svn服务外网访问。自然我可以向学校信息办申请一个新的域名比如svngdm.fudan.edu.cn指向我局域网
的机器ip:port。但是既然http://gdm.fudan.edu.cn已经可以通过外网访问了，我只要通过合理的配置就一定可以使得用类似
http://gdm.fudan.edu.cn/svn的url来访问svn服务，于是有了这篇文章。

首先我们要明确的是svn不能整合在tomcat下的，所以需要apache server这样的HTTP服务器来整合。apache server监听本地80端口，
将jsp的请求发送给本地的tomcat，将svn请求发送给svn服务器，各取所取，互不影响。

###1.前提假设
WebRoot:存放网站的根目录，WebRoot下可以放置多个网站，每个网站一个单独的目录。
WebSiteName:WebRoot下的某个目录，这个目录存放了该网站的所有文档。
HostName:你网站的名称(host name)。

###2.安装配置apache
1.在官网<http://httpd.apache.org/>上下载最新版的apache server(我用的是2.2的), 安装即可。

2.在apache的安装目录中找到conf/httpd.conf,打开它修改

	<Directory "path2yourwebroot">
	</Directory>

中的path2yourwebroot为WebRoot(WebRoot指的是你的WebRoot所在的目录，而不是WebRoot这个单词！)

修改'DirectoryIndex'为你网站的默认文档，也就是访问http://HostName时要访问的页面。

在最后面添加
	ProxyPass /images !
	ProxyPass /css !
	ProxyPass /js !
	ProxyPass /WebSiteName http://localhost:8080/WebSiteName

###3.配置tomcat(7.0及以上)
1.修改tomcat目录中的conf/server.xml, 找到'protocal="HTTP/1.1"'对应的一行，修改为
	<Connector port="8080" protocol="HTTP/1.1"
	connectionTimeout="20000"
	redirectPort="8443" proxyPort="80" proxyName=HostName/>

再次注意HostName是你的HostName，而不是单词HostName。
2.如果你原来的文档是放在tomcat默认的webapp中，最好的迁移方法是，开辟一个新的WebRoot(不在tomcat目录及子目录)。
而不是让WebRoot指向tomcat的webapp目录，这样的话如果你建一个php的网站还要放在tomcat下，岂不是非常混乱。如果你
按照我的说法，新开辟了WebRoot的话，那就需要在修改server.xml中的appbase为WebRoot，以及在<Host></Host>中加入
	<Context path="" docBase=WebRoot reloadable="true" />

###4.安装配置svn
1.下载安装<a href="http://subversion.tigris.org/files/documents/15/39559/svn-1.4.5-setup.exe", target="_blank">svn-1.4.5.exe</a>

2.新建一个版本库名字是MyProject, 使用svnadmin工具, cmd中输入
	svnadmin create E:\svnroot\MyProject
这样在E:\svnroot下新建了MyProject的目录，这个目录已经有了svn的基本文件结构。
