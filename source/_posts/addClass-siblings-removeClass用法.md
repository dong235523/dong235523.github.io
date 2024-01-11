---
title: addClass+siblings+removeClass用法
date: 2024-01-11 16:04:08
tags: 
  - JS
categories: 前端技术
cover: false
---

在一个项目中，通过这个方法实现了一排按钮，点击给当前选中按钮加上背景色，上一个选中的按钮则去掉背景色
```
$("#xm ul li").click(function () {
    $(this).addClass("listyle").siblings().removeClass("listyle");
});
```
addClass：当前元素添加class
siblings()：查找所有兄弟元素
removeClass：删除class

组合起来可以实现当前选中元素加上class事件，其余兄弟元素去掉class