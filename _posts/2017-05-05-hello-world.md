---
layout: post
title: "利用Jekyll在Github搭建博客"
description: "First blog"
author: "张波"
category: 杂谈 
tags: [jekyll github]
---
这周末刚好项目不那么忙，抽空利用jekyll在本地上搭建博客，然后推送到自己的[Github博客](https://bobz653.github.io "博客")中，对日常中好奇，好玩，复习的知识进行记录，也分享给感兴趣的朋友。

## 1.环境准备
我是在mac中搭建的，所以其他环境请找相应的安装包。

>1.安装Jekyll：使用
<pre>
gem -v;
gem update --system;
sudo gem install jekyll -n /usr/local/Cellar/;
jekyll -v;
</pre>
如果可以正常显示版本信息，说明安装成功。
>
>2.github 博客：在[Github](https://github.com)注册账户
>
>新建一个以用户名.github.io为名称的工程即可，这个工程有且仅有一个，是github使用gitpages服务托管的仅支持jekyll这样的静态页面的服务。在本地使用ssh-genkey创建rsa_pub文件，加入到git的ssh pubkey中即可通过
><pre>git clone;
>git push origin master
></pre> 等命令在本地博客更新后，来更新线上博客了。

## 2.搭建博客
> 1.合适的模板：在jeklly中有很多免费的模板供大家使用，比如[Jekyll theme](https://jekyllthemes.io/)，找到一个就git clone下来吧。
>
>
>2.本地运行：
><pre>
>jekyll build;
>jekyll server;
></pre>
>使用http://127.0.0.1:4000/即可在本地查看。
>
>3.修改：
>
>文章在_post文件夹中，全局配置在_config.yml中，css在_sass中,布局在_layout中。修改后所有的网站最终文件在_site文件夹中。
>
>新建的文章都在_post文件夹中，以YYYY-MM-DD-title.md方式存在。且头部是一些模板需要读取的信息，比如作者，类别，标题，标签等信息。可以用rake命令自动生成，定义好Rakefile之后，使用
><pre>rake post title='hello world' </pre> 
>这样的命令即可。
>4.针对公式编辑在概率，算法中常常用到，我们可以使用mathjax来进行。
>
><pre> <script type="text/javascript" async src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML"> </script>
formula1: $$n==x$$
formula2: $$n!=x$$
></pre>
>效果：
><script type="text/javascript" async src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML"> </script>
>
>formula1: $$n==x$$
>
>formula2: $$n!=x$$
>
>5.当然也可以加一些分享，评论的JS到博客中，在_layout中可以修改post模板，以增加这些功能。

## 3.推送到线上
>有前面的准备，这个步骤就简单了啊，直接使用
><pre>
>git add -A ;#这里是加入删除选项
>git commit -m 'comments';
>git push origin master;
></pre>
>
>然后访问https://youname.github.io 即可，可能有几秒的延迟。