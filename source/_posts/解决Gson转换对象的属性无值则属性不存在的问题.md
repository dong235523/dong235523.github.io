---
title: 解决Gson转换对象时空属性不存在的问题
date: 2018-07-23 17:36:33
tags: 解决方案
categories: 后端技术
cover: false
---

<font size=4>
Gson g = new GsonBuilder().serializeNulls().create();

这样new Gson，g.toJson()的结果空属性是显示null，而不是直接不存在
</font>
