---
title: idea项目关联svn
date: 2023-01-10 11:01:03
tags:
  - idea
categories: 开发工具配置
cover: false
---
## 由于一直没研究明白如何在idea中配置svn，所以想了个曲线救国的方案。

+ ### 先在一个空白文件通过SVN checkout导出项目
{% asset_img 1.png %}
+ ### 然后将down下来的.svn文件替换idea工程中的.svn文件，既可完成idea关联svn项目