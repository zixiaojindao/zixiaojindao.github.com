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

	<Directory "path_to_yourwebroot">
	</Directory>

中的path_to_yourwebroot为WebRoot(WebRoot指的是你的WebRoot所在的目录，而不是WebRoot这个单词！)

修改<code class="code">DirectoryIndex</code>为你网站的默认文档，也就是访问http://HostName时要访问的页面。

在最后面添加
	ProxyPass /images !
	ProxyPass /css !
	ProxyPass /js !
	ProxyPass /WebSiteName http://localhost:8080/WebSiteName

###3.配置tomcat(7.0及以上)
1.修改tomcat目录中的conf/server.xml, 找到<code class="code">protocal="HTTP/1.1"</code>对应的一行，修改为
	<Connector port="8080" protocol="HTTP/1.1"
	connectionTimeout="20000"
	redirectPort="8443" proxyPort="80" proxyName=HostName/>

再次注意HostName是你的HostName，而不是单词HostName。

2.如果你原来的文档是放在tomcat默认的webapp中，最好的迁移方法是，开辟一个新的WebRoot(不在tomcat目录及子目录)。
而不是让WebRoot指向tomcat的webapp目录，这样的话如果你建一个php的网站还要放在tomcat下，岂不是非常混乱。如果你
按照我的说法，新开辟了WebRoot的话，那就需要在修改server.xml中的appbase为WebRoot，以及在`<Host></Host>`中加入
	<Context path="" docBase=WebRoot reloadable="true" />

###4.安装配置svn
1.下载安装<a href="http://subversion.tigris.org/files/documents/15/39559/svn-1.4.5-setup.exe", target="_blank">svn-1.4.5.exe</a>

2.新建一个版本库名字是MyProject, 使用svnadmin工具, cmd中输入
	svnadmin create E:\svnroot\MyProject
这样在E:\svnroot下新建了MyProject的目录，这个目录已经有了svn的基本文件结构。

3.打开MyProject/conf/svnserve.conf, 把<code class="code">#password-db = passwd</code>中的'#'去掉。

4.打开MyProject/conf/passwd文件,在[users]下面每一行代表一个用户名和密码对，例如lily=123表示用户名为lily,密码为123。你可以在这个文件中任意添加你需要的用户名和密码。

5.新建MyProject/conf/access.authz文件，这个文件用来定义每个用户的权限和用户组权限。你可以指定一个module, 然后为这个module指定用户权限。例如:
	[/]
	lily=rw
表示lily对整个版本库具有读写权限。而
	[/module1]
	lily=rw
	[/module2]
	jim=rw
表示lily对module1具有读写权限，jim对module2具有读写权限。

6.创建apache认证文件users.auth。cmd进入apache bin目录,输入命令：
	htpasswd -cb users.auth lily 123
bin目录下生成了一个users.auth文件，并且产生了一行md5加密的lily和123。继续添加passwd文件中的其他用户则需要使用 -b参数。-cb参数是在第一次产生新文件用的,-c 表示create。完成之后，把users.auth移动到MyProject/conf目录下。

7.找到Subersion的安装目录,进入它的bin目录,发现如下两个文件:__mod_authz_svn.so__,__mod_dav_svn.so__。我们把这两个文件复制粘帖到apache的安装目录下的modules文件中,然后打开apache安装目录下的conf/httpd.conf文件.增加如下两行：

__LoadModule dav_svn_module modules/mod_dav_svn.so__

__LoadModule authz_svn_module modules/mod_authz_svn.so__

注意，由于dav_svn需要dav_module的支持，所以要确保下面这一行没有被注释，并且保证在dav_svn之前加载：

__LoadModule dav_module modules/mod_dav.so__

8.打开apache conf/httpd.conf文件,在最后面添加
	<Location /svn/MyProject>
	DAV svn
	SVNListParentPath on
	SVNPath		path_to_MyProject 

	AuthzSVNAccessFile path_to_MyProject/conf/access.auth
	Satisfy Any
	Require valid-user

	AuthType Basic
	AuthName "Subversion repositories"
	AuthUserFile  path_to_MyProject/conf/users.auth
	Require valid-user
	</Location> 
你需要修改path_to_xxx部分。这样配置以后重启apache就可以使用http://HostName/svn/MyProject的url来享受svn服务了。

9.如果浏览器访问http://HostName/svn/MyProject出现404错误，则在WebRoot下新建一个空的svn目录即可。

10.如果你还要增加新的版本库的话，就把上面的2,3,4,5,6,8重复一遍即可。

11.如果你发现你在通过浏览器访问http://HostName/svn/MyProject的时候，明明输入了正确的用户名和密码却无法登陆的情况，多半是你的网络前端对HTTP认证的不支持引起的。我当时就遇到这个情况，学校的反向代理代理了我的HTTP认证，最后我打电话给信息办，他们更改了设置变ok了。

