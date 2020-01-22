---
layout:  post
title:   Linux搭建wordpress
date:   2015-08-03 15:45:05
author:  "张耀文"
catalog:   false
tags:
    - blog
    - wordpress
---
Linux搭建wordpress

一、环境  
vps：Centos 6 x86 minimal 512ram小内存  
xshell 5： ssh远程连接主机

首先搭建lamp环境（linux apache mysql php或python等）

二、apache  
1 安装 ` yum install httpd `  
2 启动apache ` service httpd start `  
直接在浏览器中输入IP地址，应该就可以访问到Apache的欢迎页面了

三、mysql  
1 安装 ` yum install mysql-server `  
2 启动 ` service mysqld start `  
3 首次安装后需要设置密码

    
    
    # mysql -u root -p
    mysql>use mysql;
    mysql> update user set password=password("123456") where user='root';
    mysql> flush privileges;
    mysql> exit;  

4 创建数据库供wordpress使用

    
    
    # mysql -u root -p
    输入密码：123456
    create database wordpress;

四、php  
1 安装

    
    
    yum install php php-mysql 
    yum install php-gd php-imap php-ldap php-odbc php-pear php-xml php-xmlrpc 

2 测试php  
创建：info.php  
vim是编辑器（ ` yum install vim ` ）或是其他熟悉的编辑器

    
    
    vim /var/www/html/info.php 
    

输入：

    
    
    <?php phpinfo(); ?> 
    

然后在浏览器中，输入IP地址加上/info.php，就可以看到PHP的信息了

五、开机默认启动Apache和Mysql服务

    
    
    chkconfig httpd on 
    chkconfig mysqld on 
    

六、apache和mysql优化  
1 apache： ` vim /etc/httpd/conf/httpd.conf `

    
    
    在wordpress修改url设置会出现404 not found等，需要把httpd.conf中的AllowOverride None 改成AllowOverride All

其他优化参考： [ http://blog.csdn.net/fujs999/article/details/7251923](http://blog.csdn.net/fujs999/article/details/7251923)  
2 mysql ` vim /etc/my.cnf `

    
    
    [mysqld]中修改或添加
    key_buffer = 8M
    query_cache_size = 0
    skip-innodb

七、wordpress  
1 下载：首先你可以去wordpress官方网站看下最新的wordpress的下载地址多少。比如wordpress 4.2.2的下载地址是： [
http://cn.wordpress.org/wordpress-4.2.2-zh_CN.zip](http://cn.wordpress.org/wordpress-4.2.2-zh_CN.zip)

先用mkdir命令随便建立一个目录，这里使用wordpress，在该目录下用wget来请求wordpress的下载地址，这里用：

    
    
    mkdir wordpress
    cd wordpress
    wget http://cn.wordpress.org/wordpress-4.2.2-zh_CN.zip
    unzip wordpress-3.8-zh_CN.zip  （可能需要安装unzip： yum install unzip）
    cp wp-config-sample.php wp-config.php
    vim wp-config.php

修改 wp-config.php 添加

    
    
    define('DB_NAME', 'wordpress'); 
    define('DB_USER', 'root'); 
    define('DB_PASSWORD', '123456')
    define('DB_HOST', 'localhost');

然后把所有的文件复制到/var/www/html 目录下。用

    
    
    cp -rf wordpress/* /var/www/html/

访问wordpress： [ http://ip/wordpress/wp-admin/index.php ](http://ip/wordpress/wp-admin/index.php)  
2 设置权限：wordpress前台设置时，出现权限不足提示。

    
    
    //775 不行的话，用777
    chmod 775 -R /var/www/html
    chown -R apache /var/www/html

3 上传文件大小限制修改

    
    
    vim /etc/php.ini
    

找到post_max_size 和 upload_max_filesize ，把它们都改大点

