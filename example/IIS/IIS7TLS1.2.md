## IIS7.5如何开启TSL1.2

## 第一步
### 源站下载
点击下面链接下载 `IISCrypto`
<div class="Download"><ul class="repo"><li class="repo-download"><a href="https://www.nartac.com/Downloads/IISCrypto/IISCrypto.exe" target="_blank" title="Download" data-action="download"><i class="icon icon-download"></i> IIS Crypto GUI </a></li><li class="repo-download"><a href="https://www.nartac.com/Downloads/IISCrypto/IISCryptoCli.exe" target="_blank" title="Download" data-action="download"><i class="icon icon-download"></i> IIS Crypto CLI </a></li></ul></div>

### 本地下载
点击下面链接下载 `IISCrypto`
<div class="Download"><ul class="repo"><li class="repo-download"><a href="https://files.drixn.com/IIS/IISCrypto.exe" target="_blank" title="Download" data-action="download"><i class="icon icon-download"></i> IIS Crypto GUI </a></li><li class="repo-download"><a href="https://files.drixn.com/IIS/IISCryptoCli.exe" target="_blank" title="Download" data-action="download"><i class="icon icon-download"></i> IIS Crypto CLI </a></li></ul></div>

将下载好的 `IISCrypto` 放在桌面待用
## 第二步
右键单击以管理员身份运行
<center>![图1](https://cdn.drixn.com/img/src/IIS7TLS000.png)</center>

## 第三步
勾选需要开启的协议，建议只开启 `TLS1.2`,关闭其他协议
<center>![图2](https://cdn.drixn.com/img/src/IIS7TLS001.png)</center>

## 第四步
然后点击 `Apply`
<center>![图3](https://cdn.drixn.com/img/src/IIS7TLS002.png)</center>

## 第五步
弹出重启提示，重启服务器
<center>![图4](https://cdn.drixn.com/img/src/IIS7TLS004.png)</center>

## 第六步
验证是否开启
### 方法一
调出运行窗口，输入 `regedit`

打开注册表并定位到 `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\` 目录下

如下图，出现上面步骤勾选协议则证明成功开启
<center>![图5](https://cdn.drixn.com/img/src/IIS7TLS003.png)</center>

### 方法二：
打开浏览器，查看`协议版本`,如下图出现 `TLSv1.2` 说明开启成功
<center>![图6](https://cdn.drixn.com/img/src/IIS7TLS005.png)</center>