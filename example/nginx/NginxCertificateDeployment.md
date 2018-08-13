## nginx 证书安装 ##

### 第一步 ###
将域名 www.domain.com 的证书文件1_www.domain.com_bundle.crt 、私钥文件2_www.domain.com.key保存到同一个目录，例如/usr/local/nginx/conf目录下。

### 第二步 ###
更新Nginx根目录下 conf/nginx.conf 文件如下：

``` conf
server {
    ## 胜多负少
        listen 443 ssl http2 fastopen=3 reuseport;
        server_name www.domain.com;
        server_tokens off;
        ssl on;
        ssl_certificate 1_www.domain.com_bundle.crt;
        ssl_certificate_key 2_www.domain.com.key;
        ssl_session_timeout 5m;
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2; 
        ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
        ssl_prefer_server_ciphers on;

        location / {
            root   html; 
            index  index.html index.htm;
        }

    }
```

### 配置完成 ###
配置完成后，先用 `nginx –t` 来测试下配置是否有误，正确无误的话，重启Nginx,就可以使用 `https` 来访问了。

## 使用全站加密，http自动跳转https(可选) ##

对于用户不知道网站可以进行https访问的情况下，让服务器自动把http的请求重定向到 https。</br>
在服务器这边的话配置的话，可以在页面里加 js 脚本，也可以在后端程序里写重定向，当然也可以在 web 服务器来实现跳转。Nginx 是支持 rewrite 的（只要在编译的时候没有去掉 pcre ）</br>
在 http 的 server 里增加 `rewrite ^(.*) https://$host$1 permanent;`</br>
这样就可以实现80进来的请求，重定向为https了。</br>