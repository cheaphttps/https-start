## 网站如何启用HSTS

## 第一步
### Nginx 配置
在网站配置文件的 `server` 块儿中添加下面一行
``` bash
add_header Strict-Transport-Security "max-age=63072000; includeSubdomains; preload";
```

`max-age` 单位：秒 用来告诉浏览器在指定时间内，网站必须通过 HTTPS 协议来访问。也就是对于这个网站的 HTTP 地址，浏览器需要先在本地替换为 HTTPS 之后再发送请求。
`includeSubDomains` 可选参数，指定这个参数，表明这个网站所有子域名也必须通过 HTTPS 协议来访问。
`preload` 可选参数

然后输入命令重启 Nginx
``` bash
service nginx restart
```

### Apache2 配置
``` bash
# Optionally load the headers module:
LoadModule headers_module modules/mod_headers.so
 
<VirtualHost 0.0.0.0:443>
    Header always set Strict-Transport-Security "max-age=63072000; includeSubdomains; preload"
</VirtualHost>
```

#### HTTP 重定向 HTTPS
``` bash
<VirtualHost *:80>
  [...]
  ServerName example.com
  Redirect permanent / https://example.com/
</VirtualHost>
```

#### 仅做重定向
``` bash
<VirtualHost *:80>
  [...]
  <IfModule mod_rewrite.c>
    RewriteEngine On
    RewriteCond %{HTTPS} off
    RewriteRule (.*) https://%{HTTP_HOST}%{REQUEST_URI}
  </IfModule>
</VirtualHost>
```

#### 重启 Apache2

### Lighttpd 配置
``` bash
server.modules += ( "mod_setenv" )
$HTTP["scheme"] == "https" {
    setenv.add-response-header  = ( "Strict-Transport-Security" => "max-age=63072000; includeSubdomains; preload")
}
```

#### 重启 Lighttpd

## 第二步
### 提交 Preload List
### HSTS Preload List
可以看到 HSTS 可以很好的解决 HTTPS 降级攻击，但是对于 HSTS 生效前的首次 HTTP 请求，依然无法避免被劫持。浏览器厂商们为了解决这个问题，提出了 HSTS Preload List 方案：内置一份可以定期更新的列表，对于列表中的域名，即使用户之前没有访问过，也会使用 HTTPS 协议。
目前这个 Preload List 由 Google Chrome 维护，Chrome、Firefox、Safari、IE 11 和 Microsoft Edge 都在使用。如果要想把自己的域名加进这个列表，首先需要满足以下条件：
* **拥有合法的证书（如果使用 SHA-1 证书，过期时间必须早于 2016 年）**
* **将所有 HTTP 流量重定向到 HTTPS**
* **确保所有子域名都启用了 HTTPS**
* **输出 HSTS 响应头**
   * max-age 不能低于 18 周（10886400 秒）；
   * 必须指定 includeSubdomains 参数；
   * 必须指定 preload 参数；

点击[提交](https://hstspreload.org/)【可能需要搭梯子】

### 第一步
在 `example.com` 处输入域名点击 `Check status and eligibility`
![图1](https://cdn.drixn.com/img/src/HSTS00115.png)

### 第二步
第一步点击后，如果没什么意外情况，就会出现下图，然后将 `Submit` 下的两个选框打钩，点击 `Submit yourdomain.com to the HSTS preload list`
![图2](https://cdn.drixn.com/img/src/HSTS0013.jpg)

### 第三步
第二步提交后会提示成功字样，如下图所示
![图1](https://cdn.drixn.com/img/src/HSTS0014.jpg)

### 第四步
再次查询就会提示域名正在提交中...
![图1](https://cdn.drixn.com/img/src/HSTS0016.png)

审核通过后，你的域名就会跟随此列表被推送到新版本的 Chrome, Firefox/Mozilla 和 Safari 浏览器中，从而实现更安全的HTTPS预加载。