---
title: iframe嵌套时父子页面互相调用的问题
date: 2018-10-31 09:24:52
tags: 
  - JS
  - 解决方案
categories: 前端技术
cover: false
---
子页面调用父页面的方法：parent.父页面方法名();

父页面调用子页面的方法：

`var child=$("#testIframe")[0].contentWindow;`

child.子页面方法名();

注：testIframe为iframe的id