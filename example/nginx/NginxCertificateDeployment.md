## 证书安装 ##
<!--more-->
### 第一步 ###
将域名 www.domain.com 的证书文件1_www.domain.com_bundle.crt 、私钥文件2_www.domain.com.key保存到同一个目录，例如/usr/local/nginx/conf目录下。

### 第二步 ###
更新Nginx根目录下 conf/nginx.conf 文件如下：

```
server {
        listen 443;
        server_name www.domain.com; #填写绑定证书的域名
        ssl on;
        ssl_certificate 1_www.domain.com_bundle.crt;
        ssl_certificate_key 2_www.domain.com.key;
        ssl_session_timeout 5m;
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2; #按照这个协议配置
        ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;#按照这个套件配置
        ssl_prefer_server_ciphers on;
        location / {
            root   html; #站点目录
            index  index.html index.htm;
        }
    }
```
### 配置完成 ###
配置完成后，先用bin/nginx –t来测试下配置是否有误，正确无误的话，重启Nginx,就可以使用`https`来访问了。


listen 443 SSL访问端口号为443
ssl on	启用SSL功能
ssl_certificate	证书文件
ssl_certificate_key	私钥文件
ssl_protocols	使用的协议
ssl_ciphers	配置加密套件，写法遵循openssl标准

## 使用全站加密，http自动跳转https(可选) ##

对于用户不知道网站可以进行https访问的情况下，让服务器自动把http的请求重定向到https。
在服务器这边的话配置的话，可以在页面里加js脚本，也可以在后端程序里写重定向，当然也可以在web服务器来实现跳转。Nginx是支持rewrite的（只要在编译的时候没有去掉pcre）
在http的server里增加rewrite ^(.*) https://$host$1 permanent;
这样就可以实现80进来的请求，重定向为https了。