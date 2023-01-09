---
title: 在html中动态写入项目路径
date: 2019-02-15 10:48:09
tags:
  - JS
categories: 前端技术
cover: false
---

```
<head>
<title></title>
<script type="text/javascript">
	var pathName = window.document.location.pathname;
	var projectName = pathName.substring(0, pathName.substr(1).indexOf('/') + 1);
	
 
	var link1 = document.createElement('link');
		link1.setAttribute('type', 'text/css');
		link1.setAttribute('rel', 'stylesheet');
		link1.setAttribute('href', projectName+'/modulePackage/css/main.css');
	var link2 = document.createElement('link');
		link2.setAttribute('type', 'text/css');
		link2.setAttribute('rel', 'stylesheet');
		link2.setAttribute('href', projectName+'/modulePackage/css/icon_style.css');
	
	document.head.appendChild(link1);
	document.head.appendChild(link2);
	
</script>
<script>
	var pathName = window.document.location.pathname;
	var projectName = pathName.substring(0, pathName.substr(1).indexOf('/') + 1);
	
	var script1 = '<script type="text/javascript" src="'+projectName+'/modulePackage/js/jquery-1.11.3.min.js">'+'<'+'/script>';
	var script2 = '<script type="text/javascript" src="'+projectName+'/modulePackage/js/echarts/echarts.min.js">'+'<'+'/script>';
	var script3 = '<script type="text/javascript" src="'+projectName+'/modulePackage/js/data.js">'+'<'+'/script>';
	var script4 = '<script type="text/javascript" src="'+projectName+'/modulePackage/js/echarts/map/mudanjiang.js">'+'<'+'/script>';
	var script5 = '<script type="text/javascript" src="'+projectName+'/modulePackage/js/function.js">'+'<'+'/script>';
	var script6 = '<script type="text/javascript" src="'+projectName+'/modulePackage/js/initSet.js">'+'<'+'/script>';
	var script7 = '<script type="text/javascript" src="'+projectName+'/modulePackage/js/chartsInit.js">'+'<'+'/script>';
	var script8 = '<script type="text/javascript" src="'+projectName+'/modulePackage/js/setData.js">'+'<'+'/script>';
	var script6 = '<script type="text/javascript" src="'+projectName+'/modulePackage/js/initSet.js">'+'<'+'/script>';
 
	document.write(script1,script2,script3,script4,script5,script6,script7,script8);
 
	/*function getRootPath() {
	    //获取当前网址，如： http://localhost:8083/uimcardprj/share/meun.jsp
	    var curWwwPath = window.document.location.href;
	    //获取主机地址之后的目录，如： uimcardprj/share/meun.jsp
	    var pathName = window.document.location.pathname;
	    var pos = curWwwPath.indexOf(pathName);
	    //获取主机地址，如： http://localhost:8083
	    var localhostPaht = curWwwPath.substring(0, pos);
	    //获取带"/"的项目名，如：/uimcardprj
	    var projectName = pathName.substring(0, pathName.substr(1).indexOf('/') + 1);
	    return (projectName);
	}*/
</script>
</head>
```
通过document向html写入字符串形式动态赋值项目名。但是这种方法意义不大，因为在刷新页面时css需要重新生成，页面会先掉样式然后再加载，效果不理想。