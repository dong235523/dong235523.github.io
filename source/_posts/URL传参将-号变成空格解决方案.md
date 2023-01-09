---
title: URL传参将+号变成空格解决方案
date: 2018-01-30 10:10:38
tags: 解决方案
categories: 后端技术
cover: false
---
**问题：**使用url传递参数时会将+号转义成空格
---
**解决方案：**

    方案一：
        修改客户端，将客户端带“+”的参数中的“+”全部替换为‍“2B%”，这样参数传到服务器端时就能得到“+”了。

    方案二：
        ``` code
        encodeURIComponent(querySql);
        strURL = "search.do?param=" + encodeURIComponent(str);
        ```
        普通的传参出现乱码问题，可使用encodeURI来转译，它会转译成UTF-8的格式;
        ``` code
        strURL = "search.do?param=" + encodeURI(str);
        ```