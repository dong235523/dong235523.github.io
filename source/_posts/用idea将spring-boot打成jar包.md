---
title: 用idea将spring boot打成jar包
date: 2022-11-23 11:15:56
tags: 
  - idea
categories: 运维
cover: false
---
## 简单版方法：

在idea右侧窗口找到maven选项，右键package，选择Run Maven Build
{% asset_img 6.jpg %}
然后在target目录下，可以得到jar文件，通过cmd进入命令窗口，然后执行java -jar XXX.jar 命令即可启动项目


## 复杂版方法：

使用的项目中有其他自己写的其他jar包引用，如果直接使用使用Maven中的package打包，打包出来的只有当前的项目，运行很有可能运行不成功。

错误显示：部分类找不到

主要问题是建议打包的位置层级不够高，没有将整个项目打包。

操作流程
选择file–>Project Structure

选择Artifcats–>点击左上角‘+’号

选择jar–>From modules with depemencies
{% asset_img 1.png %}
选择项目中的主函数
最重要的一步来了：
下面的路径我选择到src路径底下，这样就会将src底下所有的jar包打包，如果项目简单，直接到主函数前面的位置底下也可以，但如果有引用到其他jar包建议路径尽量和我一样取大一点。
{% asset_img 2.png %}
下面就会生成一个MANIFEST.MF的文件
{% asset_img 3.png %}
Build -> BuildArtifacts
{% asset_img 4.png %}
选择Build Artifacts -->Build
{% asset_img 5.png %}
大功告成！