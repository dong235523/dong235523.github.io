---
title: MySQL8版本以上安装教程
date: 2023-02-17 16:09:58
tags: 
	- mysql
categories: 数据库
---

+ ## 环境预备：
	+ ### win10 64位系统
	+ ### 你的电脑需要拥有Microsoft Visual C++ 2015-2019 ,如下图：
	{% asset_img 1.jpg %}
	+ ### 如何查看自己有没有？首先按win+R，输入control 然后回车，点击卸载程序即可看见自己的应用程序
	{% asset_img 2.jpg %}
+ ## 可视化安装
	+ ### 下载安装包
	+ ### 我们这里从官网下载，如下图：
	{% asset_img 3.jpg %}
	+ ### 然后往下滑，找到MySQL Community (GPL) Downloads 如图所示，点击
	{% asset_img 4.jpg %}
	+ ### 找到MySQL Installer for Windows，点击
	{% asset_img 5.jpg %}
	+ ### ①我们先选择我们的系统，这里默认为Microsoft Windows，如果你的需求刚好为Windows，那么保持默认就好，如果不是，请到复选框选择你对应的系统。②点击下载
	{% asset_img 6.jpg %}
	如果你想安装以前的版本，点击Archives，如下图：
	{% asset_img 7.jpg %}
	 ①选择你要下载的MySQL版本 ②选择你的系统 ③点击下载
	{% asset_img 8.jpg %}
	+ ### 点击 No thanks, just start my download.
	{% asset_img 9.jpg %}
	+ ### 这样就下载到本地了
	{% asset_img 10.jpg %}
	+ ### 双击安装，然后选择Custom，点击Next
	{% asset_img 11.jpg %}
	+ ### ①点击MySQL Servers，依次展开 ②点击MySQL Server 8.0.27-x64，并且点击向右箭头 ③点击MySQL Server 8.0.27-x64
	{% asset_img 12.jpg %}
	如果你不想安装到别的路径，那么默认安装到C:\Program Files\MySQL\MySQL Server 8.0，数据目录到
	C:\ProgramData\MySQL\MySQL Server 8.0，如下图所示：
	{% asset_img 13.jpg %}
	{% asset_img 14.jpg %}
	如果你想要安装到C盘以外的盘（这里推荐安装到C盘以外的盘），①先在你的D或者E或者其他盘建立MySQL文件夹，这里我现在盘 ②在E盘下创建MySQL8.0.27的文件夹如下图所示：
	{% asset_img 15.jpg %}
	然后在MySQL8.0.27的文件夹再建立MySQL的一个数据目录data，如下图：
	{% asset_img 16.jpg %}
	①点击，选择我们刚刚在E盘创建的MySQL8.0.27文件夹（注意：这里要换成你创建MySQL8.0.27文件夹） ② 选择我们刚刚在E盘创建的MySQL8.0.27文件夹下的data文件夹，点击ok，然后再点ok 如下图：
	{% asset_img 17.jpg %}
	{% asset_img 18.jpg %}
	{% asset_img 19.jpg %}
	+ ### 如果你要使用MySQL连接可视化软件：如Navicat，Excel，PowerBI，Tableau等，我们就需要安装Connector/ODBC 8.0.27 - X64和Connector/NET 8.0.27 - X86（这里推荐安装） 如果不指定安装路径，则默认到C:\Program Files\MySQL\Connector ODBC 8.0，C:\Program Files (x86)\MySQL\Connector NET 8.0。
	{% asset_img 20.jpg %}
	如果需要自定义安装目录，首先在E盘下创建MySQL_Connector_ODBC8.0 和MySQL_Connector_Net8.0 文件夹如下图所示：
	{% asset_img 21.jpg %}
	然后分别点击Advanced Options，选择对应的安装目录之后，点击ok 如下图：
	{% asset_img 22.jpg %}
	{% asset_img 23.jpg %}
	+ ### 当①的步骤都完成之后，在②的位置点击Next
	{% asset_img 24.jpg %}
	+ ### 点击Next，点击yes
	{% asset_img 25.jpg %}
	+ ### 点击Execute。等待①为全部完成，然后点击②的Next
	{% asset_img 26.jpg %}
	{% asset_img 27.jpg %}
	+ ### 点击Next
	{% asset_img 28.jpg %}
	+ ### 点击Next
	{% asset_img 29.jpg %}
	+ ### 选择Use Strong Password Encryption for Authentication （RECOMMENDED）推荐选择这个。然后点击Next
	{% asset_img 30.jpg %}
	+ ###  ①输入进入数据库的密码， ②再次输入①所输入的密码，③默认为root用户，如果你要添加用户，点击即可，我这里直接默认为root用户， ④点击Next
	{% asset_img 31.jpg %}
	如果你要添加用户，按照如下图所示操作即可
	{% asset_img 32.jpg %}
	+ ### 点击Next
	{% asset_img 33.jpg %}
	+ ### 点击Execute
	{% asset_img 34.jpg %}
	+ ### 点击 Finish
	{% asset_img 35.jpg %}
	+ ### 点击Next
	{% asset_img 36.jpg %}
	+ ### 点击Finish
	{% asset_img 37.jpg %}
	+ ### 按win+R，输入cmd，然后再输入mysql -u root -p，出现如下界面，那是因为我们还没有配置环境变量。
	{% asset_img 38.jpg %}
	+ ### 找到我们的MySQL下的bin的路径，然后复制
	{% asset_img 39.jpg %}
	+ ### 右键我的电脑，点击属性。① 点击高级系统设置 ，② 点击环境变量 ，③ 点击Path ，④ 点击编辑
	{% asset_img 40.jpg %}
	{% asset_img 41.jpg %}
	+ ### ①点击新建 ② 粘贴刚刚复制的路径 ③ 点击确定 。 然后一直确定
	{% asset_img 42.jpg %}
	+ ### 再安装第23步的操作，如下图，证明我们已经安装成功
	{% asset_img 43.jpg %}