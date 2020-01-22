---
layout:  post
title:   java端连接https注意事项
date:   2015-07-30 15:52:55author:  "唐传林"
header-img: "img/post-bg-2015.jpg"
catalog:   false
tags:

---
双向证书生成

    
    
    生成证书
    D:\jdk1.5.0_06\bin\keytool.exe -genkey -keyalg RSA  -alias server  -keystore c:\server.keystore  -validity 3650 -dname "cn=localhost,ou=haiyi,o=haiyi,l=china,st=yantai,c=cn"
    
    D:\jdk1.5.0_06\bin\keytool.exe -genkey -keyalg RSA  -alias client  -keystore c:\client.jks  -validity 3650  -dname "cn=localhost,ou=haiyi,o=haiyi,l=china,st=yantai,c=cn"
    //不需要导入ie，故不需要转成pkcs12格式 -storetype PKCS12 -rfc
    提取cer
    
    D:\jdk1.5.0_06\bin\keytool.exe -export -alias client -file c:\client.cer -keystore c:\client.jks
    
    D:\jdk1.5.0_06\bin\keytool.exe -export -alias server -file c:\server.cer -keystore c:\server.keystore
    
    导入cer
    
    D:\jdk1.5.0_06\bin\keytool.exe -import  -file c:\client.cer -keystore c:\server.keystore
    
    D:\jdk1.5.0_06\bin\keytool.exe -import  -file c:\server.cer -keystore c:\client.jks

java端

    
    
            //System.setProperty("http.protocols", "TLSv1");
            System.setProperty("java.protocol.handler.pkgs","javax.net.ssl");
            //需要是jks结尾 
            System.setProperty("javax.net.ssl.trustStore","c:/client.jks");
            System.setProperty("javax.net.ssl.trustStorePassword","123456");
            //双向认证添加
            System.setProperty("javax.net.ssl.keyStore","c:/client.jks");
            System.setProperty("javax.net.ssl.keyStorePassword","123456");
            //设置证书cn与实际访问域名匹配
            HostnameVerifier hv = new HostnameVerifier() {
    
                public boolean verify(String hostname, SSLSession session) {
                    // TODO Auto-generated method stub
                    System.out.println(hostname);
                    System.out.println(session.getPeerHost());
                    return hostname.equals(session.getPeerHost());
                }
            };
            HttpsURLConnection.setDefaultHostnameVerifier(hv);

