## nginx 证书安装 ##

### 第一步 ###
将域名 www.domain.com 的证书文件www.domain.com.crt 、私钥文件www.domain.com.key保存到同一个目录，例如/usr/local/nginx/conf目录下。

### 第二步 ###
更新Nginx根目录下 conf/nginx.conf 文件如下：

``` conf
server {
    ##ssl端口号为443，开启 fastopen 和 reusepor t特性
    listen 443 ssl http2 fastopen=3 reuseport; 
    ##域名
    server_name www.domain.com;
    ##首页排序
    index index.html index.htm index.php;
    ##站点根目录，即网站程序存放目录 
    root /home/wwwroot/www.drixn.com;
    ##错误页面
    error_page 404 /404.html;
    error_page 502 /502.html;
    ##隐藏 nginx 版本号。浏览器访问时抓包，查看HTTP响应的Server头没有版本号
    server_tokens off;
    ##启用SSL功能
    ssl on;
    ##SSL 证书
    ssl_certificate www.domain.com.crt;
    ##key
    ssl_certificate_key www.domain.com.key;
    ##激活会话重续提高https性能
    ##session超时时间
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 10m;
    ssl_session_tickets on;

    ##允许的 SSL 协议
    ssl_protocols TLSv1.1 TLSv1.2 TLSv1.3;
    ##加密套件，写法遵循openssl标准
    ssl_ciphers TLS13-AES-256-GCM-SHA384:TLS13-CHACHA20-POLY1305-SHA256:TLS13-AES-128-GCM-SHA256:TLS13-AES-128-CCM-8-SHA256:TLS13-AES-128-CCM-SHA256:EECDH+CHACHA20:EECDH+CHACHA20-draft:EECDH+ECDSA+AES128:EECDH+aRSA+AES128:RSA+AES128:EECDH+ECDSA+AES256:EECDH+aRSA+AES256:RSA+AES256:EECDH+ECDSA+3DES:EECDH+aRSA+3DES:RSA+3DES:!MD5;
    ##启动加密算法
    ssl_prefer_server_ciphers on;
    ##日志存放路径
    access_log /home/wwwlogs/www.domain.com_nginx.log combined;

    location / { 
        ##反向代理HTTP1.1支持
        proxy_http_version 1.1;
        ## HSTS
        add_header Strict-Transport-Security "max-age=63072000; includeSubdomains; preload";
        ## frame页面的地址只能为同源域名下的页面
        add_header X-Frame-Options SAMEORIGIN;
        ## 失效某些浏览器的内容类型探嗅
        add_header X-Content-Type-Options nosniff;
        ## 防止跨站脚本 Cross-site scripting (XSS)，目前已经被大多数浏览器支持#默认是激活的，如果被用户失效，可以使用这个配置激活。
        add_header X-XSS-Protection "1; mode=block";
        add_header X-Robots-Tag none; 
        ##设置不使用缓存
        add_header Cache-Control no-cache;
    }
    
    ##将符合(gif|jpg|jpeg|png|bmp|swf|flv|mp4|ico)文件的等设定expries缓存参数，要求浏览器缓存。
    location ~ .*\.(gif|jpg|jpeg|png|bmp|swf|flv|mp4|ico)$ {
        expires 30d; ##客户端缓存上述数据30天
        access_log off; ##禁止记录 access 日志

    ##将符合js,css文件的等设定expries缓存参数，要求浏览器缓存。
    location ~ .*\.(js|css)?$ {
        expires 7d; ##客户端缓存上述数据7天
        access_log off; ##禁止记录 access 日志
    }

}

##301重定向 不带www跳转至带www 官方推荐方法
server {
  listen 80;  
  server_name  domain.com;
  rewrite ^(.*)$ https://www.domain.com$1 permanent;
}

```

### 配置完成 ###
配置完成后，先用 `nginx –t` 来测试下配置是否有误，正确无误的话，重启Nginx,就可以使用 `https` 来访问了。

## 使用全站加密，http自动跳转https(可选) ##

对于用户不知道网站可以进行https访问的情况下，让服务器自动把http的请求重定向到 https。</br>
在服务器这边的话配置的话，可以在页面里加 js 脚本，也可以在后端程序里写重定向，当然也可以在 web 服务器来实现跳转。Nginx 是支持 rewrite 的（只要在编译的时候没有去掉 pcre ）</br>
在 http 的 server 里增加 `rewrite ^(.*) https://$host$1 permanent;`</br>
这样就可以实现80进来的请求，重定向为https了。</br>