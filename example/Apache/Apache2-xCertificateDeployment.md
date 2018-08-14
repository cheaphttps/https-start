## Apache2.x 证书部署

### 第一步 ###
编辑Apache根目录下 conf/httpd.conf 文件，

找到`#LoadModule ssl_module modules/mod_ssl.so`和`#Include conf/extra/httpd-ssl.conf`，去掉前面的#号注释；

### 第二步 ###
编辑Apache根目录下 conf/extra/httpd-ssl.conf 文件，修改如下内容：
```
<VirtualHost www.domain.com:443>
    DocumentRoot "/var/www/html"
    ServerName www.domain.com
    SSLEngine on
    SSLCertificateFile /usr/local/apache/conf/2_www.domain.com_cert.crt
    SSLCertificateKeyFile /usr/local/apache/conf/3_www.domain.com.key
    SSLCertificateChainFile /usr/local/apache/conf/1_root_bundle.crt
</VirtualHost>
```
### 配置完成 ###
配置完成后，重新启动 Apache 就可以使用`https`来访问了。


| Plugin | README |
| ------ | ------ |
| SSLEngine on | 启用SSL功能 [PlDb] |
| SSLCertificateFile | 证书文件 [PlGh] |
| SSLCertificateKeyFile | 私钥文件 [PlGd] |
| SSLCertificateChainFile | 证书链文件 [PlOd] |
