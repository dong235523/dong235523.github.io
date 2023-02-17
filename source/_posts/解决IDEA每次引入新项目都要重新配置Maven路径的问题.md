---
title: 解决IDEA每次引入新项目都要重新配置Maven路径的问题
date: 2023-02-06 14:52:06
tags:
  - idea
categories: 开发工具配置
cover: false
---
**每次导入maven项目的时候，代码都会提示找不到jar包，这是因为项目使用了svn默认配置！其实原理很简单，因为每次配置maven路径是在项目中配的，这个修改的是相当于局部变量，所以在别的项目中不生效，所以解决思路在于配置全局变量。方法如下：**

+ ## 进入全局设置
    + ### 首先我们需要先关闭项目
    {% asset_img 10.png %}
    + ### 然后点开Customize的All setting进行配置
	{% asset_img 3.png %}

+ ## 修改全局变量
	{% asset_img 4.png %}