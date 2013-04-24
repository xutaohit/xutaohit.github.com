---
layout: post
title: "github jekyll blog搭建步骤"
description: "使用 jekyll搭建博客"
category: blog
tags: [jekyll, blog, 教程]
---
{% include JB/setup %}

# github jekyll blog搭建步骤…
**备忘**


## 安装环境和配置
需要建立环境…  还要在你的github上建立一个项目,用来作为载体
### 建立新的 Repository
新建一个Repository 名为xxxxx.github.com吧~
### 安装jekyll-bootstrap在本地
clone一个到本地,然后push到刚刚新建的Repository上面去

	$ git clone https://github.com/plusjade/jekyll-bootstrap.git xxxxxx.github.com
	$ cd xxxxxx.github.com
	$ git remote set-url origin git@github.com:USERNAME/xxxxxx.github.com.git
	$ git push origin master
	
### 访问
http://xxxxxxx.github.com  貌似现在是 github.io了

---

## 本地编写
本地服务的安装和开启

	$ gem install jekyll
	$ cd USERNAME.github.com 
	$ jekyll --server
	
编写文章

	$ rake post title="Hello World"
	$ rake page name="pages/about.md" 
---

	rake page           # Create a new page.
	rake post           # Begin a new post in ./_posts
	rake preview        # Launch preview environment
	
## 发布

	git clone git@github.com:username/xxxxxx.github.com.git//本地如果无远程代码，先做这步，不然就忽略
	
	git pull origin master //先同步远程文件，后面的参数会自动连接你远程的文件
	git status //查看本地自己修改了多少文件
	git add .//添加远程不存在的git文件
	git commit * -m "what I want told to someone"
	git push origin master //更新到远程服务器上
	
---

	
## 安装主题

**可选主题: [主题预览](http://themes.jekyllbootstrap.com/)**

你可以单独安装一个主题(在主题预览的页面有git的地址)
或者,你安装一个主题包 然后在里面安装你想要的主题
	
	rake theme:install git="https://github.com/jekyllbootstrap/theme-the-program.git"
	 rake theme:install name="THEME-NAME"
	 
---

切换主题:

	rake theme:switch name="the-program"
	

自定义主题: 
[api](http://jekyllbootstrap.com/api/theme-api.html)

---

## blog的配置

_config.yml 中的又用的设置
permalink: /:categories/:year/:month/:day/:title  固定链接的格式
BASE_PATH:设置你的基础url...你设置了CNAME这个留空就好
其他的就是些 留言板和 分析的设置….

---

## 绑定域名

url: http://www.xutaopb.com 
根目录建个CNAME 里面写上你的域名
dnspod里面解析到cname : xxxx.github.io



