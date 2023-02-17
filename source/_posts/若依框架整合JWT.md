---
title: 若依框架整合JWT
date: 2023-01-12 17:33:27
tags: 
   - 若依框架
   - JWT token
categories: 后端技术
cover: false
---
若依官方文档提供了整合JWT的相关bean类
在此只记录遇到的问题
新的ShiroConfig配置文件中
首先放开了
```java
filterChainDefinitionMap.put("/jwt/login", "anon");
```
获取token的jwt/login接口
通过这个接口可以获取到token
{% asset_img 1.png %}
然后配置了
```java
filters.put("jwt", new JwtFilter());
```
jwt过滤器
但是它只配置了/api路径下的接口才会执行jwtFielter
当时就是没注意到这里，所以我手动加了/jwt路径下的接口
```java
// jwt 请求单独验证
filterChainDefinitionMap.put("/api/**", "jwt");
filterChainDefinitionMap.put("/jwt/**", "jwt");
```
在这个配置过的路径下的接口，会执行token验证
{% asset_img 2.png %}

JwtUtils中的EXPIRE_TIME字段是设置过期时间