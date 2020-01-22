---
layout:  post
title:   Jboss配置https
date:   2015-07-17 17:19:38
author:  "张耀文"
catalog:   false
tags:
    - java
    - https

---
Jboss配置http  
1、生成服务器端证书

    
    
    keytool -genkey -keyalg RSA -dname "cn=localhost,ou=sango,o=none,l=china,st=beijing,c=cn" -alias server -keypass password -keystore server.jks -storepass password -validity 3650

2、生成客户端证书

    
    
    keytool -genkey -keyalg RSA -dname "cn=sango,ou=sango,o=none,l=china,st=beijing,c=cn" -alias custom -storetype PKCS12 -keypass password -keystore custom.p12 -storepass password -validity 3650

客户端的CN可以是任意值。  
3、客户端证书添加为服务器的信任认证  
由于是双向SSL认证，服务器必须要信任客户端证书，因此，必须把客户端证书添加为服务器的信任认证。由于不能直接将PKCS12格式的证书库导入，我们必须先把客户端证书导出为一个单独的CER文件，使用如下命令，先把客户端证书导出为一个单独的cer文件：

    
    
    keytool -export -alias custom -file custom.cer -keystore custom.p12 -storepass password -storetype PKCS12 -rfc

然后，添加客户端证书到服务器中（将已签名数字证书导入密钥库）

    
    
    keytool -import -v -alias custom -file custom.cer -keystore server.jks -storepass password

4、查看证书内容

    
    
    keytool -list -v -keystore server.jks -storepass password

5、将server.jks 放入 \server\default\conf下  
6、配置jboss server.xml文件  
jboss-4.2.3.GA\server\default\deploy\jboss-web.deployer\server.xml

    
    
     <Connector port="8443" protocol="HTTP/1.1" SSLEnabled="true"
                   maxThreads="150" scheme="http" secure="true"
                   clientAuth="true" sslProtocol="TLS" 
                   keystoreFile="conf/server.jks" keystorePass=" password "
                   truststoreFile="conf/server.jks" truststorePass=" password "
                   keystoreType="jks"/>

clientAuth=”true”表示双向认证  
7、导入客户端证书到浏览器  
双向认证需要强制验证客户端证书。双击“custom.p12”即可将证书导入至IE。

