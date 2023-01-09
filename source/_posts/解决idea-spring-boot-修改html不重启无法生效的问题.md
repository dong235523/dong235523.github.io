---
title: 解决idea spring boot 修改html不重启无法生效的问题
date:  2022-05-09 15:34:16
tags:
  - idea
categories: 开发工具配置
cover: false
---
+ ## 引入spring-boot-devtools jar包
    + pom写法
```java
<dependency>
     <groupId>org.springframework.boot</groupId>
     <artifactId>spring-boot-devtools</artifactId>
     <optional>true</optional>
</dependency>
```
+ ## 设置idea
    + `“File” -> “Settings” -> “Build,Execution,Deplyment” -> “Compiler”`选中 ` “Build project automatically” `打勾。
    + `“File” -> “Settings” -> "Advanced Settings"`
{% asset_img 1.jpg %}
    + 选中打勾