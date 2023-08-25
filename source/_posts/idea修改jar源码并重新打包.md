---
title: idea修改jar源码并重新打包
date: 2023-08-25 14:05:30
tags:
  - idea
categories: 开发工具配置
cover: false
---

1、在idea中找到我们需要修改的源文件(属于哪个jar)

2、文件中找到那个jar，进行备份

3、idea中可以直接查看jar包源码，对需要修改的class源码进行复制

4、在项目中新建一个跟待修改的class文件一样路径的package和Java文件，把待修改的class文件内容复制到新建的Java文件中

5、修改完成后，选中修改的文件，右键 Build Module 'test',然后在项目中找到这个路径下的class文件，如果找不到单独的class文件编译选项，就直接build整个项目,再去target中找class文件

6、将原来的jar包进行解压缩，替换掉原来的class文件

7、在jar包解压缩的目录中打开cmd命令窗，输入
```
jar -cfM0 jar包名.jar *
```
命令将解压的jar包文件重新打成jar包