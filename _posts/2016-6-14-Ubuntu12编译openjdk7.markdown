---
layout:  post
title:   Ubuntu12编译openjdk7
date:   2016-06-14 13:38:45author:  "唐传林"
header-img: "img/post-bg-2015.jpg"
catalog:   false
tags:

---
参考博文：  
[ http://www.myhack58.com/Article/48/66/2015/69362.htm](http://www.myhack58.com/Article/48/66/2015/69362.htm)  
[ http://my.oschina.net/u/2358721/blog/525510?fromerr=7eBLWyQx](http://my.oschina.net/u/2358721/blog/525510?fromerr=7eBLWyQx)  
[ http://zhaohe162.blog.163.com/blog/static/382167972014101910839474/](http://zhaohe162.blog.163.com/blog/static/382167972014101910839474/)  
[ http://blog.csdn.net/pange1991/article/details/51580969](http://blog.csdn.net/pange1991/article/details/51580969)  
一 环境软件预备  
操作系统：ubuntu12.04lts64位  
openjdk：openjdk-7-fcs-src-b147-27_jun_2011.zip 此时官网最新版为openjdk-7u40-fcs-src-b43-26_aug_2013.zip（推荐官网最新版）  
netbeans：7.01 c++版本（7.4以上版本debug源码有问题）  
Oracle jdk 7： /usr/local/java/jdk1.7….  
二 openjdk编译  
1 安装aptitude

    
    
    sudo apt-get install aptitude

2 以后安装软件全部用aptitude（对依赖包安装便捷）  
有的类包需要降版本才能排除错误

    
    
    sudo aptitude build-dep openjdk-7
    sudo aptitude install openjdk-7-jdk
    sudo aptitude install build-essential gawk m4 libasound2-dev libcups2-dev libxrender-dev xorg-dev xutils-dev x11proto-print-dev ant

3 编译使用的脚本

    
    
    #!/bin/bash
    export LANG=C
    export ALT_BOOTDIR=/usr/local/java/jdk1.7（oracle的jdk）
    #export ALT_DEVTOOLS_PATH=/usr/bin
    export ALLOW_DOWNLOADS=true #是否需要自动下载，jaxp，jaws可以自动下载
    export SKIP_DEBUG_BUILD=false #是否跳过编译debug版
    export SKIP_FASTDEBUG_BUILD=false #是否跳过编译fastdebug版
    #export DEBUG_NAME=debug
    export HOTSPOT_BUILD_JOBS=2  #多线程
    export ALL_PARALLEL_COMPILE_JOBS=2 #多线程
    #export ALT_DEVTOOLS_PATH=/usr/bin
    export SKIP_COMPARE_IMAGES=true
    export USE_PRECOMPILED_HEADER=true
    export SKIP_COMPARE_IMAGES=true
    export USE_PRECOMPILED_HEADER=true
    export BUILD_LANGTOOLS=true
    export BUILD_HOTSPOT=true
    export BUILD_JDK=true
    BUILD_DEPLOY=false
    BUILD_INSTALL=false
    
    unset CLASSPATH
    unset JAVA_HOME
    make sanity & make all DISABLE_HOTSPOT_OS_VERSION_CHECK=OK 2>&1

4 编译出错时使用过的指令

    
    
    cp  build/linux-amd64/lib/amd64/libjsound.so build/linux-amd64/lib/amd64/libjsoundalsa.so
    sudo aptitude install libxext-dev
    sudo aptitude install libx11-dev
    sudo aptitude install libxrender-dev
    sudo aptitude install gcc-multilib
    sudo aptitude install g++-multilib

将/src/share/classes/java/util目录下的CurrencyData.properties文件中的时间改为当前时间10年以内

test_gamma的错误需要将相关文件内部所有 &&/test_gamma删除

动态链接库相关错误

    
    
    sudo gedit /etc/ld.so.conf
    添加相关路径：
    include /etc/ld.so.conf.d/*.conf
    /home/zyw/myopenjdk/openjdk/hotspot/build/linux/linux_amd64_compiler2/jvmg
    /usr/local/java/jdk1.7.0_80/jre/lib/amd64
    最后 sudo ldconfig

三 hotspot编译  
1 openjdk下hotspot下make

    
    
    make jvmg
    相关错误解决参照前文

四 导入netbeans  
1 新建项目相关修改  
导入源：openjdk/hotspot  
build command：

    
    
    ${MAKE} -f Makefile  jvmg ALT_BOOTDIR=/usr/local/java/jdk1.7.0_80 ARCH_DATA_MODEL=64 LANG=C

2 项目属性  
运行命令：  
`/home/zyw/myopenjdk/openjdk/hotspot/build/linux/linux_amd64_compiler2/jvmg/gamma
-version `  
环境  
` JAVA_HOME /home/zyw/myopenjdk/openjdk/build/linux-amd64/j2sdk-image  
`

