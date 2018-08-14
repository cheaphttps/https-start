## IIS7.5如何安装多域名SSL证书

## 第一步
先点击需要开启SSL的站点，然后点击`绑定`,然后,点击`添加`

`类型` 选择 `https`

`端口` 号 填写 `555` 随便填写，只要不是 `443` 就可以

`SSL证书` 选择之前导入的证书

然后点击 `确定`

一共添加两次，第二次端口号和第一次不一样就可以，因为分带 `www` 和 `不带www` 的站点
![图1](https://cdn.drixn.com/img/src/IIS7M000.png)

## 第二步
打开文件 `C:\Windows\system32\inetsrv\config\applicationHost.config`

找到
```
<binding protocol="https" bindingInformation="*:443" />
<binding protocol="https" bindingInformation="*:555" />
<binding protocol="https" bindingInformation="*:666" />
<binding protocol="https" bindingInformation="*:777" />
<binding protocol="https" bindingInformation="*:888" />
<binding protocol="https" bindingInformation="*:999" />
```

修改成
```
<binding protocol="https" bindingInformation="*:443:www.mydomain2.com" />
<binding protocol="https" bindingInformation="*:443:mydomain2.com" />
<binding protocol="https" bindingInformation="*:443:www.mydomain3.com" />
<binding protocol="https" bindingInformation="*:443:mydomain3.com" />
<binding protocol="https" bindingInformation="*:443:www.mydomain4.com" />
<binding protocol="https" bindingInformation="*:443:mydomain4.com" />
```
如下图格式：
![图1](https://cdn.drixn.com/img/src/IIS7M001.png)

## 第三步
完成上述步骤后，重启每个站点即可(切记不是重启服务器)