---
title: 通过hexo在多台电脑同步github pages博客
date: 2023-01-10 17:03:59
tags:
  - git
categories: 前端技术
cover: false
---
+ ## 确保自己已经使用hexo在github搭建好了自己的个人博客。
	+ ### 对username.github.io仓库新建hexo分支，并克隆 
    + ### 在Github的dong235523.github.io仓库上新建一个hexo分支，并切换到该分支，并在该仓库->Settings->Branches->Default branch中将默认分支设为hexo，save保存；然后将该仓库克隆到本地，进入该dong235523.github.io文件目录。
    {% asset_img 1.jpg %}
    {% asset_img 2.jpg %}
	+ ### 完成上面步骤后，在当前目录使用Git Bash执行git branch命令查看当前所在分支，应为新建的分支hexo：
```code
$ git branch
*hexo
```
	+ ### 将hexo分支下载下来，会有一个dong235523.github.io文件目录，只保留.git文件夹，其余删除
+ ## 将本地博客的部署文件拷贝进dong235523.github.io文件目录
	+ ### 先将本地博客的部署文件（Hexo目录下的全部文件）全部拷贝进dong235523.github.io文件目录中去。
	+ ### 接下来，进入dong235523.github.io文件目录下，将该目录下的全部文件提交到hexo分支，提交之前需注意：
		+ #### 将themes目录以内中的主题的.git目录删除（如果有），因为一个git仓库中不能包含另一个git仓库，提交主题文件夹会失败。
		+ #### 可能有人会问，删除了themes目录中的.git不就不能git pull更新主题了吗，很简单，需要更新主题时在另一个地方git clone下来该主题的最新版本，然后将内容拷到当前主题目录即可
+ ## 提交hexo分支
	+ ### 执行		
```code
git add .
git commit -m 'back up hexo files'（引号内容可改）
git push
```	
	+ ### 即可将博客的hexo部署环境提交到GitHub个人仓库的hexo分支。
	+ ### 现在可以在GitHub上的dong235523.github.io仓库看到两个分支的差异了。
	+ ### 在文件_config.yml中可以看到，静态资源上传的分支是固定配置的，并不随着主分支的变化而变化
	{% asset_img 3.jpg %}
	+ ### main分支和hexo分支各自保存着一个版本，master分支用于保存博客静态资源，提供博客页面供人访问；hexo分支用于备份博客部署文件，供自己维护更新，两者在一个GitHub仓库内互不冲突。
+ ## 在新电脑下载资源
	+ ### 将新电脑的生成的ssh key添加到GitHub账户上
	+ ### 在新电脑上克隆dong235523.github.io仓库的hexo分支到本地，此时本地git仓库处于hexo分支
	+ ### 切换到dong235523.github.io目录，执行npm install(由于仓库有一个.gitignore文件，里面默认是忽略掉 node_modules文件夹的，也就是说仓库的hexo分支并没有存储该目录[也不需要]，所以需要install下)
	{% asset_img 4.jpg %}
+ ## 可以在新电脑写博客了
	+ ### 编辑完博客之后，依次执行以下指令
```code
git add .
git commit -m 'back up hexo files'（引号内容可改）
git push
```
	+ ### 保证hexo分支版本最新
	+ ### 执行指令
	```code
	hexo g
	hexo d
	```
	+ ### （在此之前，有时可能需要执行hexo clean），完成后就会发现，最新改动已经更新到main分支，两个分支互不干扰！
	+ ### 注意： 每次换电脑进行博客更新时，不管上次在其他电脑有没有更新，最好先 git pull**