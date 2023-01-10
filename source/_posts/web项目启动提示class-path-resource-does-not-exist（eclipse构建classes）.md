---
title: web项目启动提示class path resource does not exist（eclipse构建classes）
date: 2019-11-22 17:00:12
tags: 
  - myEclipse
categories: 开发工具配置
cover: false
---
今天想搭建一个web工程，参考以前项目的结构，配置文件一般都会放到conf文件夹中，conf与src是同级的关系。刚开始只是建了一个conf，把配置文件丢进去了，部署到tomcat时却提示class path resource [config.xml] cannot be opened because it does not exist，就是说在classes中找不到config.xml。打开web-inf下的classes一看，果真是没有。为什么没有搞进来呢？后来才知道，是没有把conf弄到 build path中，只有build path中有的文件夹，编译后才会在classes中。
{% asset_img 1.jpg %}
