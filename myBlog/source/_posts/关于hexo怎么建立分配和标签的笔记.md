---
title: 关于hexo怎么建立分配和标签的笔记
date: 2019-06-20 11:50:09
tags: 笔记
---

1. 站点配置文件中将language改为zh-Hans(因为我的主题里面的中文样式是这个)
2. 主题配置文件中将language改为zh-Hans
3. 主题配置文件中在menu: 选项内把home(主页) tags(标签) categories(分配) archives(归档) 前面的#去掉
4. 在blog主文件下 hexo new page tags (这样就新建了标签分页)
5. 在blog主文件下 hexo new page categories (这样子就新建了分类分页)
6. 这样子你就有了各个分页的功能
7. 目前来说主页和各个分页的内容都存放在_posts文件夹内
