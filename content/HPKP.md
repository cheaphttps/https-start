## 如何配置HTTPS公钥扎钉HPKP

## 第一步
获取中级证书或者叫中间证书公钥的pin-SHA256指纹数据

### 途径一
购买证书时，压缩包里会有。以科摩多证书为例，压缩包里边的 `mydomain.ca-bundle` 即为中级证书

### 途径二
我列了几个常见证书的中级证书公钥的pin-SHA256指纹数据，直接复制使用
{% note info %}
COMODO ECC Domain Validation Secure Server CA:
EohwrK1N7rr3bRQphPj4j2cel+B2d0NNbM9PWHNDXpM=
COMODO RSA Domain Validation Secure Server CA:
klO23nT2ehFDXCfx3eHTDRESMz3asj1muO+4aIdjiuY=
TrustAsia DV SSL CA - G5 :
IiSbZ4pMDEyXvtl7Lg8K3FNmJcTAhKUTrB2FQOaAO/s=
Let's Encrypt Authority X3:
YLh1dUR9y6Kja30RrAn7JKnbQG/uEtLMkBgFF2Fuihg=
{% endnote %}

### 途径三
点击[Qualys SSL Labs's SSL Server Test](https://www.ssllabs.com/ssltest/index.html)前往，然后输入你的域名检测。在结果中寻找如下图字段，即为中级证书公钥的pin-SHA256指纹数据
![图1](https://cdn.drixn.com/img/src/HSTS-HPKP006.jpg)

## 第二步
将 `mydomain.ca-bundle` 重命名中级证书为`comodo.pem`格式

## 第三步
上传中级证书至服务器根目录并
``` bash
#首先对证书进行校验，核对证书信息是否正确
openssl x509 -in comodo.pem -noout -subject
```

``` bash
#接着生成Public.key
openssl x509 -in comodo.pem -noout -pubkey | openssl asn1parse -noout -inform pem -out public.key
#生成后再取得证书的哈希值
openssl dgst -sha256 -binary public.key | openssl enc -base64
#comodo中级证书的哈希值
EohwrK1N7rr3bRQphPj4j2cel+B2d0NNbM9PWHNDXpM=
```
![图2](https://cdn.drixn.com/img/src/HSTS-HPKP005.png)

``` bash
#同样操作再对亚洲诚信的证书获取哈希值
openssl x509 -in trustasia.pem -noout -subject
subject= /C=GB/ST=Greater Manchester/L=Salford/O=COMODO CA Limited/CN=COMODO RSA Domain Validation Secure Server CA
```

``` bash
#接着生成Public.key
openssl x509 -in trustasia.pem -noout -pubkey | openssl asn1parse -noout -inform pem -out public.key
#生成后再取得证书的哈希值
openssl dgst -sha256 -binary public.key | openssl enc -base64
#亚洲诚信中级证书的哈希值
IiSbZ4pMDEyXvtl7Lg8K3FNmJcTAhKUTrB2FQOaAO/s= 
```

## 第四步
将中级证书公钥的pin-SHA256指纹数据添加到网站配置文件中

### Nginx 配置
添加以下行到你的 HTTPS 配置的 server 块中：
``` bash
add_header Public-Key-Pins 'pin-sha256="EohwrK1N7rr3bRQphPj4j2cel+B2d0NNbM9PWHNDXpM="; pin-sha256="IiSbZ4pMDEyXvtl7Lg8K3FNmJcTAhKUTrB2FQOaAO/s="; max-age=63072000; includeSubDomains';
```
然后重新启动 `Nginx服务`


### Apache 配置
编辑 Apache 配置文件（如 /etc/apache2/sites-enabled/website.conf 或 /etc/apache2/httpd.conf），并添加下列行到你的 VirtualHost 中
``` bash
# 如需要，载入 headers 模块。
LoadModule headers_module modules/mod_headers.so
Header set Public-Key-Pins "pin-sha256=\"klO23nT2ehFDXCfx3eHTDRESMz3asj1muO+4aIdjiuY=\"; pin-sha256=\"633lt352PKRXbOwf4xSEa1M517scpD3l5f79xMD9r9Q=\"; max-age=2592000; ncludeSubDomains"
```

### Lighttpd 配置
Lighttpd 更简单一些，将下列行添加到 Lighttpd 配置文件（如 /etc/lighttpd/lighttpd.conf）中
``` bash
server.modules += ( "mod_setenv" )
$HTTP["scheme"] == "https" {
    setenv.add-response-header  = ( "Public-Key-Pins" => "pin-sha256=\"klO23nT2ehFDXCfx3eHTDRESMz3asj1muO+4aIdjiuY=\"; pin-sha256=\"633lt352PKRXbOwf4xSEa1M517scpD3l5f79xMD9r9Q=\"; max-age=2592000; includeSubDomains")
}
```

## 第五步
检查
### 方法一
打开[SSL LABS](https://www.ssllabs.com/ssltest/index.html)，输入自己的域名，检测结果出现下图即可证明配置成功
![图1](https://cdn.drixn.com/img/src/HPKP000.jpg)
![图3](https://cdn.drixn.com/img/src/HSTS-HPKP001.png)

### 方法二
打开火狐浏览器，输入你的域名，邮件单击打开 `检查元素` ，然后点击左侧 `网络` 标签，再次点击右侧 `安全性标签` 出现下图即可证明配置成功
![图4](https://cdn.drixn.com/img/src/HSTS-HPKP002.jpg)





