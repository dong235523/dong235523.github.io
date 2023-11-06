---
title: 基于若依的summernote富文本编辑器上传视频功能
date: 2023-11-06 13:19:24
tags: 
	- 若依框架
categories: 前端技术
---

原框架只支持视频链接上传，局限性比较大，不能上传本地的视频，所以对summernote视频功能进行扩展

修改summernote.js文件

+ ##  在 callbacks 里增加一个 
```
onFileUpload: null,
```
{% asset_img 1.png %}


+ ##  在video方法里面增加 
```
attachment: 'Attachment', 
```
后面的值就是页面上显示的名称 这里可以自己改。 
{% asset_img 2.jpg %}

+ ##  在showVideoDialog方法里增加三段代码
```
var $videoAttachment = _this2.$dialog.find('.note-video-attachment');
var callbacks = _this2.options.callbacks;
```

```
$videoAttachment.off('change');
$videoAttachment.on('change', function (event) {
  if (callbacks.onFileUpload) {
    console.log(event.target.files);
    _this2.context.triggerEvent('file.upload',event.target.files);
  }
});
```

```
_this2.bindEnterKey($videoAttachment,$videoBtn);
```
{% asset_img 3.jpg %}

+ ##  在VideoDialog.initialize方法里增加页面按钮代码
```
'<div class="form-group note-form-group">',
	"<label class=\"note-form-label\">" + this.lang.video.attachment + "</label>",
	'<input class="note-video-attachment form-control note-form-control note-input" type="file" />',
'</div>',
```
{% asset_img 4.jpg %}

添加好了之后，页面效果是这样的 会多一个附件上传按钮
{% asset_img 5.png %}
这里的名字再第一步的时候可任意修改

+ ##  接下来我们写页面的上传方法
```
//初始化summernote控件
//toolbar可以屏蔽视频及图片上传功能
$('.summernote').summernote({
	placeholder: '请输入公告内容',
	height : 220,
	lang : 'zh-CN',
	followingToolbar: false,
	dialogsInBody: true,
	/*toolbar: [
		[ 'style', [ 'style' ] ],
		[ 'font', [ 'bold', 'italic', 'underline', 'strikethrough', 'superscript', 'subscript', 'clear'] ],
		[ 'fontname', [ 'fontname' ] ],
		[ 'fontsize', [ 'fontsize' ] ],
		[ 'color', [ 'color' ] ],
		[ 'para', [ 'ol', 'ul', 'paragraph', 'height' ] ],
		[ 'table', [ 'table' ] ],
		[ 'insert', [ 'link'] ],
		[ 'view', [ 'undo', 'redo', 'fullscreen', 'codeview', 'help' ] ]
	],*/
	callbacks: {
        onImageUpload: function (files) {
            sendFile(files[0], this);
        },
		onFileUpload: function(files) {
			//附件上传
			sendVideoFile(files[0], this);
		}
    }
});

function sendVideoFile(file, obj) {
	var size = file.size;
	if((size / 1024 / 1024) > 10) {
		alert("视频大小不能超过10M...");
		return false;
	}
	console.log("size="+size);
	var data = new FormData();
	data.append("file", file);
	$.ajax({
		data : data,
		type : "POST",
		url : ctx + "common/upload",    // 图片上传出来的url，返回的是图片上传后的路径，http格式
		cache : false,
		contentType : false,
		processData : false,
		dataType : "json",
		success: function(data) {//data是返回的hash,key之类的值，key是定义的文件名
			$(".note-video-url").val(data.fileName);
			//去掉插入视频禁用按钮
			$(".note-video-btn").removeAttr("disabled");
			/*if (result.code == web_status.SUCCESS) {
                //上传之后的url赋值到 视频地址文本框
                $(".note-video-url").val(result.url);
                //去掉插入视频禁用按钮
                $(".note-video-btn").removeAttr("disabled");
            } else {
                $.modal.alertError(result.msg);
            }*/
		},
		error:function(){
			alert("视频上传失败");
		}
	});
}
```