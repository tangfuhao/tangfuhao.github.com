---
title: Mac下搭建Hexo + Github 详细步骤 教程
date: 2016-03-03 16:30:19
categories: 工具和插件
tags: [Hexo,Github,工具] 
---


## 感想，第一篇日记

{% blockquote %}
做技术5年多了,其间有多次想好好写博客，一直没有坚持下来，中间断断续续使用过一些博客产品都没有坚持。
最近看了一博文，感触很深，所以决定重新开启博客之旅，并好好坚持下去。
一来是，给自己技术学习的一种积累，梳理。
二来，提高自己书面表达能力，前几年书没有觉得，现在是觉得自己表达能力真的出了问题。
之前就有使用过github搭建博客，不过之前使用的是Octopress，然后也是跟风，搭完就没用了。
这次看了文章[《我为什么坚持写博客》](http://bbs.ruoren.com/thread-35485316-1-1.html)目标是每周至少3篇认真原创文章。
好，废话不多说，开始。
{% endblockquote %}






## 准备工作，从零开始
Hexo是一款基于Node.js的静态博客框架，而Node.js可以简单理解为可以用js语言写服务器后端的框架。
然后如果要发布到github上需要git和github帐号。
因为用的是mac环境，所以直接用Homebrew（这是mac下超强的包管理工具，基本开发人员必备）来安装。

**如果有安装过可以跳过步骤**
**这里可能出现一些问题，可能由于权限问题，如果是，在命令前加sudo。**


### 安装 Homebrew
命令行直接输入

	$ ruby -e "$(curl -fsSL https://raw.github.com/Homebrew/homebrew/go/install)”



### 安装 git
因为安装过Homebrew，可以使用命令brew

	$ brew install git



### 安装 node.js 
	$ brew install node



### 安装 hexo
	$ npm install -g hexo

这里的npm是node下的一个包管理工具。
－g命令代表全局安装，否则安装到命令执行时当前.／node_modules目录。
－g会安装到{prefix}／node_modules目录。prefix是默认制定的，一般在prefix = "/usr/local"
命令 npm config ls 可查看 prefix
命令 npm config set prefix "xxxxx" 可以指定prefix目录（不建议修改）

**到此为止，安装完毕**
**这里可能出现一些问题，可能由于权限问题，如果是，在命令前加sudo。**




## 安装博客,调试

### 创建博客
创建本地一个目录，用来创建博客，比如
{% codeblock %}
$ mkdir hexo
$ cd hexo
$ sudo hexo init
{% endcodeblock %}
创建完毕！

### 本地调试
{% codeblock %}
$ sudo hexo server
INFO  Start processing
INFO  Hexo is running at http://localhost:4000/. Press Ctrl+C to stop.
{% endcodeblock %}

或者，缩写
{% codeblock %}
$ sudo hexo s
INFO  Start processing
INFO  Hexo is running at http://localhost:4000/. Press Ctrl+C to stop.
{% endcodeblock %}
然后可以访问 http://localhost:4000 来查看结果

**注意如果访问一直没响应 可能有端口占用问题，可以运行下面命令指定端口**

	$ sudo hexo server -p xxxx

**更多hexo命令用法，可以查看我的[Hexo写博客需要的所有知识总结](http://tangfuhao.github.io/2016/03/03/Hexo%E5%86%99%E5%8D%9A%E5%AE%A2%E9%9C%80%E8%A6%81%E7%9A%84%E6%89%80%E6%9C%89%E7%9F%A5%E8%AF%86%E6%80%BB%E7%BB%93/)**


## 发布到github
*需要注册github帐号，这个很简单略过*

### 设置github帐号ssh验证(可以跳过)
首先需要在本机设置ssh key以保证push工程时不需要在授权,如果没有设置，也会在发布时要求输入帐号密码

### 利用github二级域名 创建资源
github会提供以用户名创建的二级域名，例如：yourusername.github.com
用法是需要创建一个名称为yourusername.github.io的资源
比如我的用户名为tangfuhao所以创建一个tangfuhao.github.io的资源
然后当发布完资源后可以通过yourusername.github.com或者yourusername.github.io来访问网站

创建资源过程略过。

### 安装发布插件
在博客文件夹运行下面命令，来安装git部署的工具

	$ npm install hexo-deployer-git --save

然后在_config.yml文件修改发布的配置，最后一行改为（注意替换yourusername）
{% codeblock %}
deploy:
  type: git
  repo: https://github.com/yourusername/yourusername.github.com.git
  branch: master
{% endcodeblock %}

### 运行生成发布
执行命令发布
{% codeblock %}
$ sudo hexo generate
$ sudo hexo deploy
{% endcodeblock %}

或者缩写

	sudo hexo d -g

以后更新了文章 再执行命令，来更新站点
{% codeblock %}
$ sudo hexo generate
$ sudo hexo deploy
{% endcodeblock %}

或者缩写

	sudo hexo d -g

如果改动了站点的源码，需要在发布之前
	$ sudo hexo clean

如果成功了就可以通过yourusername.github.com或者yourusername.github.io来访问你的博客了


## 自定义博客


### 更换主题

如果你对默认的主题不满意，可以通过克隆的方式，把别人的主题克隆过来,比如我使用的这一套

	git clone https://github.com/iissnan/hexo-theme-next.git themes/next

然后在_config.yml中theme选项下 指定主题，比如我指定为

	theme: next

更多的主题可以[参考](https://www.zhihu.com/question/24422335)























