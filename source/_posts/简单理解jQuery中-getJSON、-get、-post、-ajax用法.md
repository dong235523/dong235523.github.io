---
title: 简单理解jQuery中$.getJSON、$.get、$.post、$.ajax用法
date: 2019-01-17 10:04:41
tags: 
  - JS
  - 理论知识
categories: 前端技术
cover: false
---
##    在WEB开发中异步请求方式普遍使用，ajax技术减少程序员的工作量，也提升用户交互体验。AJAX的四种异步请求方式都能实现基本需求。
---
> ## $.getJSON
>> + $.getJSON()是专门为ajax获取json数据而设置的，并且支持跨域调用，其语法的格式为：
>> ```
    $.getJSON(
       url,                 //请求URL
       [data],           //传参，可选参数
       [callback]       //回调函数，可选参数
    );   
```
>> + url：string类型， 发送请求地址  
>> + data ：可选参数， 待发送 Key/value 参数 ，同get，post类型的data
>> + callback ：可选参数，载入成功时回调函数，同get，post类型的callback
>> + JSON是一种理想的数据传输格式，它能够很好的融合与JavaScript或其他宿主语言，并且可以被JS直接使用。使用JSON相比传统的通过 GET、POST直接发送“裸体”数据，在结构上更为合理，也更为安全。至于jQuery的getJSON()函数，只是设置了JSON参数的 ajax()函数的一个简化版本。这个函数也是可以跨域使用的，相比get()、post()有一定优势。另外这个函数可以通过把请求url写 成"myurl?callback=X"这种格式，让程序执行回调函数X。
>> + 特别注意：$.getJSON是以GET方式提交数据，所以不能提交过大的数据量，可选$.post。
    ```java
    //——项目中的实际案例
    function changeAnswerShow(id){
      $.getJSON(
              "/legalconsult/admin/changeshowanswer",{id:id},　　　　//URL和参数
              function(data){
               if(data.error==0){　　　　　　　　　　　　　　　　　　　　//回调函数，与后台交互
                  showSuccessMessage("操作成功,正在刷新。。。。");
                  setTimeout("window.location.reload()",2200);
                  var i=$("td.show"+id+" a i" );
                 if(i.hasClass("splashy-gem_okay")){
                     i.removeClass("splashy-gem_okay").addClass("splashy-gem_remove");
                 }else{
                    i.removeClass("splashy-gem_remove").addClass("splashy-gem_okay");
                 }
                 return true;
              }else{
                 showErrorMessage(data.error);
                 return false;
              }
            }
        );
    }
    ```
> ## $.get
>> + $.get是以GET方式实现AJAX请求，在提交短数据时可用。但基本不常用，因为$.getJSON完全满足功能需要，正如上所说，结构合理、更加安全。即便是为了提交长数据，也不会选用$.get,而是$.post。
> ## $.post
>> ```java
 $.post(
     url,
     [data],
     [callback],
     [type]
 );
```
>> + url：string类型，ajax请求的地址。
>> + data：可选参数，object类型，发送至服务器的key/value数据会作为QueryString附加到请求URL中。
>> + callback：可选参数，function类型，当ajax返回成功时自动调用该函数。
>> + type：type为请求的数据类型，可以是html,xml,json等类型，如果我们设置这个参数为：json，那么返回的格式则是json格式的，如果没有设置，就和$.get()返回的格式一样，都是字符串的。
>> + 这个方法和$.get()用法差不多，唯独多了一个type参数，那么这里就只介绍type参数吧，其他的参考上面$.get()的。
>> ```java
  //实际项目案例
  function changeMarkShow(id){
       $.post(
               "/legalconsult/admin/markquestion",{id:id},
               function(data){
                   if(data.error==0){
                       showSuccessMessage("操作成功,正在刷新。。。。");
                       setTimeout("window.location.reload()",2200);
                       var i=$("td.markshow"+id+" a i" );
                      if(i.hasClass("splashy-star_full")){
                          i.removeClass("splashy-star_full").addClass("splashy-gem_empty");
                      }else{
                          i.removeClass("splashy-gem_empty").addClass("splashy-gem_full");
                      }
                      return true;
                      }else{
                          showErrorMessage(data.error);
                          return false;
                      }
                 },
             "json"                                             //声明数据格式
              );
 }
```
> ## $.ajax
>> + $.ajax是一种常见的普通封装异步方式。