---
title: hexo博客添加搜索功能
date: 2023-03-02 16:03:36
tags:
  - hexo
categories: 博客
cover: false
---
### 安装插件
在自己博客根目录下（我的目录：D:\workspace\hexo），执行如下命令
```
cnpm install hexo-generator-searchdb --save
```

### 修改站点配置文件
修改根目录下的_config.yml（我的目录：D:\workspace\hexo_config.yml），在最底部添加如下配置
```
search:
  path: search.xml
  field: post
  format: html
  limit: 10000
```

### 修改主题配置文件
修改主题下的themes\butterfly\ _config.yml配置文件（我的目录：D:\workspace\hexo\themes\butterfly\ _config.yml），搜索local_search，修改enable为true
```
local_search:
  enable: true
  # if auto, trigger search by changing input
  # if manual, trigger search by pressing enter key or search button
  trigger: auto
  # show top n results per article, show all results by setting to -1
  top_n_per_article: 1
```

### 预览效果
开启本地server
```
hexo clean
hexo g
hexo s
```
访问：http://localhost:4000/ ，即可看到想要的搜索功能了

### 注：
这个搜索功能非常卡，连本地搜索效率也难以保证，就更别说git服务器上了，所以不打算开启这个功能
