---
layout:  post
title:   android studio连接汇总
date:   2015-05-13 16:57:05
author:  "张耀文"
catalog:   false
tags:

---
android  
1 视频： [ http://wenku.baidu.com/course/view/be906bec0975f46527d3e100?cid=5](http://wenku.baidu.com/course/view/be906bec0975f46527d3e100?cid=5)  
2 mars安卓  
3源码查看工具：source insight3.5 注册码：SI3US-361500-17409  
android studio  
1、 android studio 安装文件： [ http://www.oschina.net/news/62021/android-studio-1-2-final ](http://www.oschina.net/news/62021/android-studio-1-2-final)  
2、android studio 学习网站： [ http://www.android-studio.org/index.php/docs/guide/](http://www.android-studio.org/index.php/docs/guide/)  
3、Gradle Android插件用户指南： [ http://avatarqing.github.io/Gradle-Plugin-User-Guide-Chinese-Verision/ ](http://avatarqing.github.io/Gradle-Plugin-User-Guide-Chinese-Verision/)  
4、android studio新建hello world时出现Rendering Problems：  
[ http://www.bubuko.com/infodetail-798360.html](http://www.bubuko.com/infodetail-798360.html)  
部分原文：  
大部分是说添加v4 support, 进行安装，但是实际是

res/values/styles.xml 文件里

    
    
    <!-- Base application theme. -->
    <style name="AppTheme" parent="Base.Theme.AppCompat.Light.DarkActionBar">
        <!-- Customize your theme here. -->
    </style>

少了个Base……

