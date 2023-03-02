---
title: bootstrap File Input 多文件上传插件使用记录（一）配置及批量同步上传
date: 2023-03-02 15:22:33
tags:
	- JS
	- Bootstrap
	- 未验证
categories: 前端技术
cover: false
---
### 需求：
1.在表单中上传多张图片，并且表单中还要其他数据。
2.上传成功后关闭弹出框，并重置上传插件。

### HTML
```
<form id="add_commodity_form" method="post" enctype="multipart/form-data">
         ...//其他form参数
        <div class="form-group" style="width:99%">
           <input id="add_bachPic" name="commoPicArr" type="file" multiple  class="file-loading">
        </div>
</form>
```

### js
```
 //初始化文件上传插件
function initFileUpload(prefix){
    $("#add_bachPic").fileinput({
    	language: 'zh',                                 //中文
    	uploadUrl:'<%=basePath%>/commodity/addCommodityPic',
    	showPreview: true,				//展前预览
    	showUpload: false,				//不显示上传按钮
    	showCaption: false,				//不显示文字表述
    	uploadAsync:false,                             //采用同步上传
    	removeFromPreviewOnError:true,                 //当文件不符合规则，就不显示预览
    	maxFileCount: 5,
    	maxFileSize: 1024*10,                          //单位为kb，如果为0表示不限制文件大小
    	allowedFileExtensions: ["jpg", "jpeg", "gif", "png","bmp"],
        uploadExtraData: function(previewId, index) {   //额外参数 返回json数组
                  return {
                          'id':commId
                         };
                  }
        }).on("filebatchuploadsuccess", function(event, data) {	//当上传成功回调函数
                var response = data.response;
                if(response.resultCode=='0'){
                        $("#closedAddWin").click();
	 	        table.ajax.reload();
		}
	    });
 } 
```
 原来我的思路是不使用插件的上传按钮，而自己通过js将插件里面的多文件与表单一起提交，但是到后台只能获取到最后一个文件，我不是文件数组。我查了很多办法好像都没办法，后来改变思路：还是自己用js触发提交表单，只是先将表单其他数据传到后台保存返回数据库id,然后通过 $('#add_bachPic').fileinput('upload');触发提交文件上传，并传递额外参数id,最后保存成功！

### 确认新增按钮
```
var commId;
function addCommodity(){
 
	if($('#add_bachPic').fileinput("getFilesCount")<=0){          //获取文件个数
		myAlert(1,"请至少上传一张详情图片!");
		return;
	}
	$("#add_commodity_form").attr('action','<%=basePath%>/commodity/addCommodity');
	$("#add_commodity_form").ajaxSubmit({
  	       success : function(data){
                        var result = $.parseJSON(data);
                        if(result.resultCode == 0){
                                var id = result.result;	                //先保存基本数据，返回数据库id
                                commId = id;                            //保存id值
                                $('#add_bachPic').fileinput('upload');        //然后触发插件开始上传文件
                        }	
               }
         });
}
```

### controller代码
```
       /**
		 * 
		 * @MethodName: addCommodityPic
		 * @Description: 商品信息管理：新增详情图片
		 * @param param
		 * @return DataGrid
		 */
		@RequestMapping("/addCommodityPic")
		@ResponseBody
		public Result addCommodityPic(Long id,HttpServletRequest request,@RequestParam MultipartFile[] commoPicArr){
		Result result = new Result(Result.FAIL, "新增商品失败！");
		try{
			//commoPicArr数组则是上传的多文件，然后就可以做进一步处理，在此省略....
 
			.....
			int a = commService.updateCommodityPic(vo);
			if(a>0){
				result.setResultCode(Result.SUCC);
				result.setMsg("新增商品成功!");
			}
		} catch (Exception e) {
			logger.error("商品信息管理：新增图片", e.getCause());
			e.printStackTrace();
		}
		return result;
	}
```
后台代码通过同步上传就能获取到文件数组，然后就可以做进一步处理了，这里就不详细说java代码了。当重新打开对话框，需要对插件进行重置，可以使用方法
$("#add_bachPic").fileinput('reset'); //重置预览表中的所有文件，
还有其他的方法例如$("#add_bachPic").fileinput('clear'); 只是清理还未上传的预览，上传成功的不会清除。

{% asset_img 1.png %}
如果想限制图片不能单独上传，也就是不显示预览中的上传按钮，我的做法很简单粗暴，直接取源码中删除了这个方法
{% asset_img 2.png %}
### 效果
{% asset_img 3.png %}
