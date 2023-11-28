---
title: 'IDEA2021 报Error running ‘Application‘: Command line is too long解决方案'
date: 2023-11-28 09:17:24
tags:
  - idea
categories: 开发工具配置
cover: false
---
## 问题背景

IDEA在启动项目时，突然报错
```
Error running 'Application':
Command line is too long.Shorten command line for Application or also for Spring Boot default configuration.
```
## 解决方案

### springboot项目

当前项目点击运行下拉框的Edit Configurations
{% asset_img 1.png %}
在Application界面选择Shorten command line下拉框，选择classpath file或者JAR manifest，点击OK确认
{% asset_img 2.jpg %}

### main函数

当前项目点击运行下拉框的Edit Configurations
{% asset_img 3.jpg %}
在主函数页面下点击Modify optins
{% asset_img 4.jpg %}
在弹出页面点击Shorten command line
{% asset_img 5.jpg %}
页面上会多出Shorten command line选项
{% asset_img 6.jpg %}
选择classpath file或者JAR manifest，点击OK确认