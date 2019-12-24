---
layout: tags
title: hexo怎么添加本地搜索功能
date: 2019-06-20 16:11:17
tags: 笔记
---
1. 在blog主文件夹内将searchdb插件安装进来 npm install hexo-generator-searchdb --save
2. mac下如果出现gym错误 access deny错误的话, 在blog主文件夹内运行 
	sudo chown -R $USER ./ 
	目的是将blog的归属改为当前用户, 当前用户拥有归属权的时候就不会出现权限问题
3. 在站点配置文件_config.yml文件下添加
	search:
  	path: search.xml
  	field: post
  	format: html
  	limit: 10000
  	目的是在public文件夹内生成search.xml文件, 将所有发不过的文件形成链接放入search.xml内, 这样子就有了搜索的能力
4. 在主题配置文件_config.yml文件内找到
	local_search:
	enable: false
	trigger: auto
	top_n_per_article: 1
	将false改为true, 如果本来就是true就做理会, 如果没有上面这段代码就添加上去
	由于页面配置原因, 就不把注释的部分贴进来了, 请自行脑补
5. 在blog主文件内执行一次 hexo g 这样子你的博客就拥有了本地搜索功能了

