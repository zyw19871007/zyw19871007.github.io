---
layout:  post
title:   activity和fragment生命周期
date:   2015-06-24 11:21:26
author:  "张耀文"
catalog:   false
tags:
    - android

---
1、activity  
![这里写图片描述](http://img-blog.csdn.net/20150624111453651)  
2、fragment  
![这里写图片描述](http://img-blog.csdn.net/20150624112033002)  
3、对比  
![这里写图片描述](http://img-blog.csdn.net/20150624112057381)

onAttach方法：Fragment和Activity建立关联的时候调用。  
onCreateView方法：为Fragment加载布局时调用。  
onActivityCreated方法：当Activity中的onCreate方法执行完后调用。  
onDestroyView方法：Fragment中的布局被移除时调用。  
onDetach方法：Fragment和Activity解除关联的时候调用。

