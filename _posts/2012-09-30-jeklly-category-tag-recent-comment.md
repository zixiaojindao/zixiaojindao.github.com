---
layout: post
title: "为jeklly博客添加分类，标签和评论"
description: "Jeklly bootstrap中自带了分类，标签，评论，但是很多人选择了使用自己的主题或者fork别人的主题，那么就很有可能没有这三项内容，这篇文章讲解为jeklly博客添加
分类，标签和评论的方法。"
categories: 
- Blogging
tags:
- 添加分类
- 添加标签
- 添加评论
---

###1 添加分类
在博文的头部可以指定categories，并赋值，来给博文分类，例如:
<pre>
categories:
- cat1
- cat2
</pre>
这篇博文就隶属于cat1和cat2两个分类。添加分类的原理就是通过读取_post目录下每篇文章的categories域来罗列每个分类下的文章，我们需要
做的就是有一个categories.html来罗列所有的分类和各个分类下的文章。具体操作方法如下：

1.下载<a href="https://gist.github.com/3805859", target="_blank">categories.md</a>，放到github博客根目录下。

2.在你的首页上也就是index.html, index.md中添加categories.html的超链接

3.第1步中你需要加上自己的layout域来符合你的主题。

###2 添加标签
在博文的头部可以指定tags, 并赋值，来给博文加标签，例如：
<pre>
tags:
- tag1
- tag2
</pre>
这篇博文就属于tag1和tag2两个标签。添加标签的原理和添加分类的原理一样，不再赘述。但是大家可能看很多人的博客中的标签是大小不一，颜色渐进
的罗列，英文中称为tag cloud(我实在不会翻译这个词了),其实是使用了一个js脚本来实现的，具体操作方法如下：

1.下载<a href="https://gist.github.com/3805966", target="_blank">tags.md</a>, 放到github博客根目录下。 

2.下载<a href="https://gist.github.com/3805897", target="_blank">jquery.tagcloud.js</a>,放入github博客根目录下的js目录中。

3.在你的首页上也就是index.html, index.md中添加tags.html的超链接

4.第1步中你需要加上自己的layout域来符合你的主题。

###3 添加评论
添加评论的原理就是在你的post的模板中加入disqus的通用代码，具体方法如下：

1.注册<a href="http://disqus.com/", target="_blank">disqus</a> 

2.注册你的博客域名，生成你的短域名，使用universe code，把相应的代码粘贴到你post使用的模板中。

3.添加最新评论到首页，在disqus中点击admin->tools->recent comments, 把相应的代码粘贴到index.md, index.html中，并指定合适的位置，一般
在右侧。

###4 总结
添加分类，标签可以方便读者了解你整个博客的结构，添加评论可以使得读者和博主互动。如果你在使用我上面的方法遇到了困难，可以留言，发邮箱给我。
但是最好的方法，就是你clone<a href="https://github.com/zixiaojindao/zixiaojindao.github.com", target="_blank">我的这个博客</a>到你本地，对应的
我的设置来更改你的设置，这是我认为最快的解决方法。


