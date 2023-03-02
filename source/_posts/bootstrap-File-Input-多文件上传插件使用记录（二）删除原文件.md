---
title: bootstrap File Input 多文件上传插件使用记录（二）删除原文件
date: 2023-03-02 15:38:16
tags:
	- JS
	- Bootstrap
	- 未验证
categories: 前端技术
cover: false
---
### 使用场景：

在后台管理框架中，一条数据中包含不固定的多张图片属性，然后需要同其他数据一起做增删改查。多文件同时新增上一篇已经做过了，需要的请点击打开链接，但是编辑的时候，就需要吧原来上传的图片展示出来，然后可以进行删除和重新上传，，这就是我现在要做的功能。
{% asset_img 1.png %}

### HTML
```
<form>  
     .......//其他数据  
     <div class="form-group" style="width:99%">  
         <input id="update_bachPic" name="commoPicArr" type="file" multiple >  
     </div>  
</form>  
```
### JS
当点击数据的编辑按钮，则通过后台返回的数据初始化编辑页面，并初始化文件上传插件。
```
$("#update_bachPic").fileinput({  
        language: 'zh',                                        //语言  
        uploadUrl:'<%=basePath%>/commodity/addCommodityPic',   //上传地址  
        showPreview: true,              //展前预览  
        showUpload: false,              //显示上传按钮  
        showCaption: false,             //显示文字表述  
        uploadAsync:false,                             //false 同步上传，后台用数组接收，true 异步上传，每次上传一个file,会调用多次接口  
        removeFromPreviewOnError:true, //当选择的文件不符合规则时，例如不是指定后缀文件、大小超出配置等，选择的文件不会出现在预览框中，只会显示错误信息  
        maxFileCount: 5,  
        maxFileSize: 1024*10,           //单位为kb，如果为0表示不限制文件大小  
        allowedFileExtensions: ["jpg", "jpeg", "gif", "png","bmp"],  
                                  
        previewFileIcon: "<i class='glyphicon glyphicon-king'></i>",  
        overwriteInitial: false,  
        initialPreviewAsData: true,  
        initialPreview: [        //这里配置需要初始展示的图片连接数组，字符串类型的，通常是通过后台获取后然后组装成数组直接赋给initialPreview就行了  
                        "http://......img",  
                        "http://......img",  
                        ],  
        initialPreviewConfig: [ //配置预览中的一些参数   
                                {caption: "transport-1.jpg", size: 329892, width: "120px", url: "deletePic", key: 1},  
                                {caption: "transport-2.jpg", size: 872378, width: "120px", url: "deletePic", key: 2}  
                              ]  
        }).on('filepredelete', function(event, key, jqXHR, data) {  
                    console.log('Key = ' + key);  
                    console.log(jqXHR);  
                    console.log(data);   
        });  
```
注意：
    配置initialPreview:[ ] 配置的是数组，也就是需要初始展示图片的地址数组，字符串类型，当然通常是通过ajax从后台获取到链接后再组装成数组直接赋给他就行了。
    配置initialPreviewConfig:[ ]也是一个数组,他主要是配置预览图片的相关显示参数，名字啊、大小啊。但是最重要的配置是url，他是你在点击图中的删除按钮后，会调用的地址，然后通过ajax去后台删除原图片。配置key，表示删除的时候向后台传递的参数，看图：
    {% asset_img 2.png %}
    很神奇，你会发现，在url中我只配置了deletePic，但是通过调试发现，删除请求的url竟然自动拼接了前面的一段url，我猜测可能是从上面的配置updateUrl而来，或者是从请求头中的Referer而来。
    配置的key是请求传递的参数，实测发现名字key不能改成自己的其他名字，而且传递的值不能是对象，这就有个问题了，万一我想传多个值怎么办？那就自己动手丰衣足食，改造源码吧。。

### 更改fileinput.js源码
需求一：将key传递的参数改为： key: "{'id':'1','name':'name1'}"
注意：既然直接传递对象不行，那就传递json字符串吧，然后在源码中转换成json不就行了？
{% asset_img 3.png %}
更改源码中2379行左右，当点击删除按钮时，会调用settings,里面就是已经配置好的参数，我们获取key值，然后将单引号替换成双引号，然后转换为json对象，替换data参数。

这里为什么要替换单引号为双引号，为什么不直接在key配置的时候就写双引号呢？别问，我试了，不行，转换会出错，要不就是传递不过来，不然我也不会使用这个多此一举的办法啊。。。你直接传递json字符串到后台，用后台代码解析为json也可以。。然后我们的调试请求发现传递的值就变成我们想要的了。。
{% asset_img 4.png %}
好，传值的问题解决了。就可以点击按钮的时候去后台删除此图片了，并传递我们其他的参数。新选择的图片并不会调用后台方法，会直接删除的。

需求二：当用户点击删除按钮的时候，提示他这是原图片，并问他是否确认删除，确认后才去后台调用删除，取消则不删除。这是为了避免用户点错，而导致删除了原图片，那么久需要在后台调用ajax之前执行一段我们的提示代码。
解决：查看API，发现了这么几个事件可以调用。
```
    $('#input-id').on('filepreremove', function(event, id, index) {       //只是你删除重新选择的图片才会触发，而删除原图片不会触发。
       console.log('id = ' + id + ', index = ' + index);
    });


   $('#input-id').on('filepredelete', function(event, key, jqXHR, data) {  //就是在删除原图片之前触发，而新选择的图片不会触发。能满足我们的要求。
      console.log('Key = ' + key);
   });
```
解决一：
    采用filepredelete时间监听，在删除之前询问用户是否确定，并在确定后执行后面的。如果采用一般bootstrap的询问框，都是采用回调的方式监听用户操作的，还没等回调结束，后面的代码就已经开始调用ajax执行删除了。所以需要一个js线程的暂停机制，类似于alert，当用户操作后再往下执行，原始的js confirm()方法可以实现询问，然后点击确定则继续执行，所以代码改为：
```
$("#update_bachPic").fileinput({  
        language: 'zh',  
        uploadUrl:'<%=basePath%>/commodity/addCommodityPic',  
        showPreview: true,              //展前预览  
        showUpload: false,              //显示上传按钮  
        showCaption: false,             //显示文字表述  
        uploadAsync:false,  
        removeFromPreviewOnError:true,  
        maxFileCount: 5,  
        maxFileSize: 1024*10,           //单位为kb，如果为0表示不限制文件大小  
        allowedFileExtensions: ["jpg", "jpeg", "gif", "png","bmp"],  
        previewFileIcon: "<i class='glyphicon glyphicon-king'></i>",  
        overwriteInitial: false,  
        initialPreviewAsData: true,  
        initialPreview: urlArr,  
        initialPreviewConfig: [  
                            {caption: "transport-1.jpg", size: 329892, width: "120px", url: "deletePic", key: "{'id':'1','name':'name1'}"},  
                            {caption: "transport-2.jpg", size: 872378, width: "120px", url: "deletePic", key: "{'id':'1','name':'name1'}"},  
                            ]  
        }).on('filepredelete', function(event, key, jqXHR, data) {  
                if(!confirm("确定删除原文件？删除后不可恢复")){  
                    return false;  
                }  
        });  
```
{% asset_img 5.png %}
当用户点击取消，则返回false,到源码中去阻止事件的继续执行。
{% asset_img 6.png %}
在源码2323行左右，执行ajax方法中beforSend，我们在filepredelete中返回false，返回false则再return给beforSend，他就会停止执行ajax方法，从而达到我们的目的。

这种方法确实能实现我们的要求，但是使用原始的confirm难免有些难看，也不符合整个系统的UI。但是使用bootstrap其他的询问框，则没办法实现线程暂停机制。但是我们可以在他执行ajax之前去判断，从而阻止执行。

解决二：

  更改源码2379行左右，当点击删除按钮时，调用$.ajax（setting）方法之前采用其他bootstrap询问插件来监听，当然你也可以把他封装成一个内部事件，在初始化fileinput的时候去监听，
{% asset_img 7.png %}

{% asset_img 8.png %}
好了，这就是一些简单的删除原文件的方法，其中不乏需要我们去更改源码来符合我们的需求。后面使用中还遇到什么问题，再来研究吧。