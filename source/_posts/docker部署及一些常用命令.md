---
title: docker部署及一些常用命令
date: 2023-06-02 14:11:33
tags: 
  - docker
categories: 运维
cover: false
---
docker部署需要centos7环境

官方一键安装docker命令
```
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
```

docker的一些常用命令：

```
启动docker
service docker startup
```

```
查看docker内的全部容器
docker ps -a
```

```
结束指定端口号的任务
docker stop 123234234
```

```
启动指定端口号的任务
docker start 123123123
```

```
删除指定端口号的容器
docker rm 123123123
```

