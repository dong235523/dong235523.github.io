---
title: docker安装redis
date: 2024-05-18 16:39:30
tags: 
  - docker
categories: 运维
cover: false
---

官方推荐安装方式：
1、执行命令，下载 redis 镜像
```
docker pull redis:4.0.1 
```
如下所示：

{% asset_img 1.jpg %}
2、执行命令，创建并启动 redis 容器
```
docker run --rm -d --name redis6379 -p 6379:6379 redis:4.0.1 --requirepass "123456"
```

测试一下，已经安装成功！