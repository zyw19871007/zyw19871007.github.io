---
layout:  post
title:   ssl双向认证-openssl生成证书
date:   2016-07-23 13:46:30
author:  "张耀文"
catalog:   false
tags:

---
一、前言  
已有教程大部分是使用jdk自带keytool生成自签名证书，自己测试可以的，但是用于实际使用时，在ssl双向认证中服务器认证客户端时，不可避免的每增加一个客户，服务器端需要添加信任列表。  
解决方法：使用同一ca(向第三方机构申请或者自己模拟一个)签名过的服务器和若干客户端，服务器只需要添加该ca的信任认证即可。  
二、工具链接（推荐openssl）  
1 keytool 签名： [ http://www.cnblogs.com/youxia/p/java002.html](http://www.cnblogs.com/youxia/p/java002.html)  
2 openssl [ http://blog.csdn.net/sunyujia/article/details/3017405](http://blog.csdn.net/sunyujia/article/details/3017405)  
3 关于缺失 openssl.cnf 手动创建放在\bin下，文件内容

    
    
    ################################################################
    # openssl example configuration file.
    # This is mostly used for generation of certificate requests.
    #################################################################
    [ ca ]
    default_ca= CA_default # The default ca section
    #################################################################
    [ CA_default ]
    dir=C:/Program Files/Apache Group/Apache2/conf/ssl # Where everything is kept
    certs=$dir # Where the issued certs are kept
    crl_dir= $dir/crl # Where the issued crl are kept
    database= $dir/index.txt # database index file
    new_certs_dir= $dir/new_certs # default place for new certs
    certificate=$dir/CA/OrbixCA # The CA certificate
    serial= $dir/serial # The current serial number
    crl= $dir/crl.pem # The current CRL
    private_key= $dir/CA/OrbixCA.pk # The private key
    RANDFILE= $dir/.rand # private random number file
    default_days= 365 # how long to certify for
    default_crl_days= 30 # how long before next CRL
    default_md= md5 # which message digest to use
    preserve= no # keep passed DN ordering
    # A few different ways of specifying how closely the request should
    # conform to the details of the CA
    policy= policy_match 
    # For the CA policy [policy_match]
    countryName= match
    stateOrProvinceName= match
    organizationName= match
    organizationalUnitName= optional
    commonName= supplied
    emailAddress= optional
    # For the `anything' policy
    # At this point in time, you must list all acceptable `object'
    # types
    [ policy_anything ]
    countryName = optional
    stateOrProvinceName= optional
    localityName= optional
    organizationName = optional
    organizationalUnitName = optional
    commonName= supplied
    emailAddress= optional
    [ req ]
    default_bits = 1024
    default_keyfile= privkey.pem
    distinguished_name = req_distinguished_name
    attributes = req_attributes
    [ req_distinguished_name ]
    countryName= Country Name (2 letter code)
    countryName_min= 2
    countryName_max = 2
    stateOrProvinceName= State or Province Name (full name)
    localityName = Locality Name (eg, city)
    organizationName = Organization Name (eg, company)
    organizationalUnitName = Organizational Unit Name (eg, section)
    commonName = Common Name (eg. YOUR name)
    commonName_max = 64
    emailAddress = Email Address
    emailAddress_max = 40
    [ req_attributes ]
    challengePassword = A challenge password
    challengePassword_min = 4
    challengePassword_max = 20
    unstructuredName= An optional company name

三 补充  
1 p12转jks

    
    
    keytool -importkeystore -srckeystore server.p12 -srcstoretype PKCS12 -deststoretype JKS -destkeystore  server.jks

2 导入信任cer到jks

    
    
    keytool -import -v -alias root -file root.cer -keystore server.jks 

3 server.jks 应包含 server的秘钥，信任的root的公钥  
4 浏览器里应该有的证书：root的包含公钥的证书（一般cer格式导入），由root签发的各个用户的含有秘钥的证书（一般p12格式导入）  
5 应用服务器server.xml 一般配置

    
    
     <Connector port="8443" protocol="HTTP/1.1" SSLEnabled="true"
                   maxThreads="150" scheme="http" secure="true"
                   clientAuth="true" sslProtocol="TLS" 
                   keystoreFile="server.jks" 
                   keystorePass="123456" 
                   keystoreType="jks" 
                   truststoreFile="server.jks" 
                   truststorePass="123456" 
                   truststoreType="JKS" 
                   />

