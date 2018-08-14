---
title: Tomcat 证书部署
date: 2017-03-10 14:15:18
categories:
- SSL证书
- 证书安装
tags:
- SSL
- HTTPS
- Tomcat
---
## 证书安装 ##
<!--more-->
### 第一步 ###
配置SSL连接器，将www.domain.com.jks文件存放到conf目录下，使用编辑器或者 vi 打开 tomcat 安装目录/conf/server.xml  ,添加一个 Connector：
```
<Connector port="443" protocol="org.apache.coyote.http11.Http11NioProtocol"
			SSLEnabled="true" maxThreads="150" scheme="https" secure="true"
               clientAuth="false" keystoreFile="getssl.jks" keystorePass="123456" sslProtocol="TLSv1.2"
              sslEnabledProtocols="TLSv1.2,TLSv1.1,TLSv1"
              ciphers="TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384,TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,
                       TLS_ECDH_RSA_WITH_AES_256_GCM_SHA384,TLS_ECDH_ECDSA_WITH_AES_256_GCM_SHA384,
                       TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,
                       TLS_ECDH_RSA_WITH_AES_128_GCM_SHA256,TLS_ECDH_ECDSA_WITH_AES_128_GCM_SHA256,
                       TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384,TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384,
                       TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA,TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA,
                       TLS_ECDH_RSA_WITH_AES_256_CBC_SHA384,TLS_ECDH_ECDSA_WITH_AES_256_CBC_SHA384,
                       TLS_ECDH_RSA_WITH_AES_256_CBC_SHA,TLS_ECDH_ECDSA_WITH_AES_256_CBC_SHA,
                       TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256,TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256,
                       TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA,TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA,
                       TLS_ECDH_RSA_WITH_AES_128_CBC_SHA256,TLS_ECDH_ECDSA_WITH_AES_128_CBC_SHA256,
                       TLS_ECDH_RSA_WITH_AES_128_CBC_SHA,TLS_ECDH_ECDSA_WITH_AES_128_CBC_SHA"
 />
```

clientAuth	如果设为true，表示Tomcat要求所有的SSL客户出示安全证书，对SSL客户进行身份验证
keystoreFile	指定keystore文件的存放位置，可以指定绝对路径，也可以指定相对于 （Tomcat安装目录）环境变量的相对路径。如果此项没有设定，默认情况下，Tomcat将从当前操作系统用户的用户目录下读取名为 “.keystore”的文件。
keystorePass	密钥库密码，指定keystore的密码。（如果申请证书时有填写私钥密码，密钥库密码即私钥密码）
sslProtocol	指定套接字（Socket）使用的加密/解密协议，默认值为TLS

### 第二步 ###
http自动跳转https的安全配置
到conf目录下的web.xml。在</welcome-file-list>后面，</web-app>，也就是倒数第二段里，加上这样一段
```
<web-resource-collection >
    <web-resource-name >SSL</web-resource-name>
    <url-pattern>/*</url-pattern>
</web-resource-collection>
<user-data-constraint>
    <transport-guarantee>CONFIDENTIAL</transport-guarantee>
</user-data-constraint>
```
这步目的是让非ssl的connector跳转到ssl的connector去。所以还需要前往server.xml进行配置：
```
<Connector port="80" protocol="HTTP/1.1"
    connectionTimeout="20000"
    redirectPort="443" />
```
(3) 打开$CATALINA_HOME/conf/web.xml，在该文件末尾增加：
```
<security-constraint> 
       <web-resource-collection > 
              <web-resource-name >SSL</web-resource-name> 
              <url-pattern>/*</url-pattern> 
       </web-resource-collection>
                             
       <user-data-constraint> 
              <transport-guarantee>CONFIDENTIAL</transport-guarantee> 
       </user-data-constraint> 
</security-constraint>
```

### 配置完成 ###
redirectPort改成ssl的connector的端口443，重启后便会生效。

安装后重启tomcat
```
shutdown.sh / shutdown.bat
startup.sh / startup.bat
```