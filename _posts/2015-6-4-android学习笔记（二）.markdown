---
layout:  post
title:   android学习笔记（二）
date:   2015-06-04 16:31:15
author:  "张耀文"
catalog:   false
tags:

---
**2015年7月3日 15:37:33**  
子线程中进行UI操作：

  1. Handler的post()方法 

  2. View的post()方法 

  3. Activity的runOnUiThread()方法 

**2015年7月2日 16:13:33**  
1 新建module，整个project会同步，比较耗时，取消同步  
![这里写图片描述](http://img-blog.csdn.net/20150702161347153)  
2 新建module，不能引用R文件需要手动make module  
3 parcelable 接口需要自定义CREATOR（必须全部大写）

    
    
     public static final Parcelable.Creator<T> CREATOR 

**2015年7月1日 14:02:52**  
1 之前setting设置编码，如果新建工程会不起作用。全局编码设置下图位置设置  
![这里写图片描述](http://img-blog.csdn.net/20150701140402992)

**2015年6月26日 10:11:39**

  1. 工程下gradle文件夹的内容上传至github，能提升导入该项目的速度，且统一gradle版本。 
  2. 动态添加控件，用LayoutParams重新布局 

    
    
    View view = layoutInflater.inflate(R.layout.button_layout, null);
            LinearLayout.LayoutParams layoutParams = new LinearLayout.LayoutParams(ViewGroup.LayoutParams.WRAP_CONTENT, ViewGroup.LayoutParams.WRAP_CONTENT);
            linearLayout.addView(view,layoutParams);

3 关闭 as启动打开上次工程

![这里写图片描述](http://img-blog.csdn.net/20150626102411063)  
4 android.R.layout自带布局存放位置：sdk\platforms\android-22\data\res\layout

**2015年6月16日 10:28:33**

  1. 对一个服务既调用了 startService()方法，又调用了 bindService()方法的，   
这种情况下该如何才能让服务销毁掉呢？根据 Android 系统的机制，一个服务只要被启动或  
者被绑定了之后，就会一直处于运行状态，必须要让以上两种条件同时不满足，服务才能被 销毁。所以，这种情况下要同时调用  
stopService()和 unbindService()方法，onDestroy()方法才 会执行。

**2015年6月15日 16:21:52**

  1. 通知（notification api16）创建 

    
    
    Notification notification = new Notification.Builder(MainActivity.this).setContentTitle("title").setContentText("lala,alala").setSmallIcon(R.mipmap.ic_launcher).build();
    //必须设置smallicon，标题，内容，通知才能显示                

**2015年6月12日 15:11:30**

  1. button中text自动转为大写： android:textAllCaps=”false” 
  2. SharedPreferences 存储数据格式xml文件： 

    
    
    <?xml version='1.0' encoding='utf-8' standalone='yes' ?>
    <map>
        <boolean name="boolean_value" value="true" />
        <string name="name">admin</string>
        <string name="psw">123</string>
        <set name="setString">
            <string>a</string>
            <string>b</string>
        </set>
    </map>
    

**2015年6月10日 09:38:33**

  1. 在广播接收器中是不允许开启线程的 

**2015年6月8日 15:05:44**

  1. 尽量将控件或布局的大小指定成 match_parent 或 wrap_content，如果必须要指定一个固定值，则使用 dp 来作为单位，指定文字大小的时候 使用 sp 作为单位。 

**2015年6月5日 11:41:01**

  1. 自定义控件如何关闭（finish）其所属活动（activity）： [ http://stackoverflow.com/questions/11679108/android-finishing-activity-from-class-view-by-touchevent ](http://stackoverflow.com/questions/11679108/android-finishing-activity-from-class-view-by-touchevent)   
使用： ` ((Activity)getContext()).finish(); `

**2015年6月4日 16:32:43**

  1. 更改android studio 内存：安装目录下：studio64.exe.vmoptions（对应64位系统）   
第二行为最大内存，默认为750m，改为1050

  2. 升级ide：先前手动忽略更新并关闭更新，导致check for update，提示已是最新版本。   
解决方法：（参考： [ http://stackoverflow.com/questions/17474412/removing-ignored-android-studio-or-intellij-update-builds](http://stackoverflow.com/questions/17474412/removing-ignored-android-studio-or-intellij-update-builds) ）  
移除
%HOMEPATH%.AndroidStudio\config\options\other.xml（我的是在updates.xml里找到的）中ignoredBuildNumbers代码块。（ps：退出as再修改）

  3. 前面修改过内存配置文件，blank_activity xml模板文件，导致升级提示冲突。内存配置文件，自动替换。所以模板文件手动换成原来的（可以从sdk目录下找到原版）。 

  4. 取消启动时fetching sdk   
1）进入刚安装的Android Studio目录下的bin目录。找到idea.properties文件，用文本编辑器打开。  
2）在idea.properties文件末尾添加一行： disable.android.first.run=true ，然后保存文件。  
3）关闭Android Studio后重新启动，便可进入界面。

