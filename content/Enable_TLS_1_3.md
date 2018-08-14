## 博客支持TLS1.3

## 第一步
进入你的服务器源码存放目录，比如：`/opt/`
下载openssl 1.1.1 的18个分支
```
git clone -b tls1.3-draft-18 --single-branch https://github.com/openssl/openssl.git openssl
```
或者
```
wget -O openssl.zip https://github.com/openssl/openssl/archive/tls1.3-draft-18.zip
unzip tls1.3-draft-18.zip
mv openssl-tls1.3-draft-18 openssl
```

## 第二步
下载新版本的 Nginx 源码
```
wget -c https://nginx.org/download/nginx-1.13.4.tar.gz
tar -xzvf nginx-1.13.4.tar.gz
```

## 第三步
编译 Nginx
```bash
cd nginx-1.13.4/
#加上编译参数
./configure 粘贴你先前的参数 --with-openssl=../openssl --with-openssl-opt=enable-tls1_3
## 然后编译
make
```

## 第四步
平滑升级 Nginx 请移步 [如何热升级 Nginx](https://linan.blog/2014/UpdateNginx)

## 第五步
浏览器开启 TLS1.3 支持

### Chrome Canny 开启TLS1.3
下载并安装 [Chrome Canny](https://www.google.com/chrome/browser/canary.html) 
在地址栏输入 `chrome://flags/` 然后回车键，如下图所示，找到 `TLSTLS 1.3 Mac, Windows, Linux, Chrome OS, AndroidSets the TLS 1.3 variant used ` 下拉选项，选择 `Enable Draft` 如下图所示，修改完毕后重启 Chrome Canny。
![图1](https://cdn.drixn.com/img/src/TLS1.3-002.jpg)
![图2](https://cdn.drixn.com/img/src/TLS1.3-003.jpg)

### Firefox 开启TLS1.3
下载并安装F [irefox nightly](https://nightly.mozilla.org/)
在地址栏输入 `about:config` 然后回车键，如下图所示，点击 `我了解此风险!` ，在搜索框搜索 `security.tls.version.max` ，然后将其值由 `3` 修改成 `4` .修改完毕后重启 Firefox。
![图3](https://cdn.drixn.com/img/src/TLS1.3-005.jpg)
![图4](https://cdn.drixn.com/img/src/TLS1.3-004.jpg)

## 第六步
验证 TLS1.3 是否支持
点击[Qualys SSL Labs's SSL Server Test](https://www.ssllabs.com/ssltest/index.html)前往，然后输入域名检测。结果出现如下图所示即证明已经支持 `TLS1.3`
![图5](https://cdn.drixn.com/img/src/TLS1.3-000.jpg)