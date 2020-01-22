---
layout:  post
title:   Wordpress更换ip不能正常访问解决方法
date:   2016-10-10 10:55:16
author:  "张耀文"
catalog:   false
tags:

---
一 前言  
1 网站更换ip后,wordpress不能正常访问  
2 查看:wp-config.php中mysql相关设置,DB_HOST一般为localhost

二 更改worpress的站点url和主页  
1 登陆mysql: mysql -u root -p  
2 加载数据库: use wordpress  
3 更新站点url和主页

    
    
    update wp_options set option_value='http://ip/blg' where option_name ='siteurl' or option_name='home'

