---
title: docker部署mysql
date: 2023-06-02 15:08:10
tags: 
  - docker
  - mysql
categories: 运维
cover: false
---

首先拉取mysql镜像
docker pull mysql:8.0.28

//一键部署mysql
docker run -p 3306:3306 --name mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql:8.0.28
//启动docker
sudo systemctl start docker

//docker安装mysql后更改密码连接方式
1.进入容器
docker exec -it mysql /bin/bash

2. 登录mysql
mysql -u root -p

ps:我新装的docker这个命令不好使，使用的另一个命令
首先执行 docker ps -a 查看mysql的容器id
然后执行 docker exec -it xxxxxxx mysql -u root -p
等同于执行了1、2两步

3. 输入密码
输入mysql安装时设置的密码（123456）
4. 切换数据库
use mysql;
4. 修改密码方式
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY '123456';
5. 刷新生效
FLUSH PRIVILEGES;
5. 退出mysql
exiit;
5.退出容器
exit;
三. 关机后再次开机启动mysql容器
docker start mysql