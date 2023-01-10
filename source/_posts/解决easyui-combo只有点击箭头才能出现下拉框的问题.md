---
title: 解决easyui combo只有点击箭头才能出现下拉框的问题
date: 2019-01-17 09:11:39
tags: 
  - easyui
  - 解决方案
categories: 前端技术
cover: false
---
<font size=4>
在页面初始化函数中加入如下代码
</font>
``` code
$(".combo").click(function(){
  if ($(this).prev().combobox("panel").is(":visible")) {
      $(this).prev().combobox("hidePanel");
  } else {
     $(this).prev().combobox("showPanel");
  }
});
``` 
